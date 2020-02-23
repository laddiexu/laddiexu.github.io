---
layout: post
title: "读书笔记 - 向上汇报"
description: "C# Entity Framework GroupBy 性能问题"
category: tech
tags: [C#, EF, GroupBy]
modify: 2020-02-20 00:00:00
---
用 C# / .NET 写后端代码的开发者，一般都会用 Entity Framework (以下简称 EF )来进行 DB 访问的，我最近遇到了一个性能上的问题，还是很常见的，在这里记录一下问题和解决的方法，希望给大家一个帮助，如果有更好的方法，也希望大家能写告诉我，先谢过各位啦~


## 问题
要解决的问题很简单很常见，从 StatusHistory 这张表中查出所有状态变化的最后的一条记录。StatusHistory 这张表中包含有 Id, ServiceId, FromStaus, ToStatus, Timestamp, InitiatedBy。这几个字段也很直白，从名字就能明白含义。根据这个问题，直接的写法就如下：
``` C#
var result = await this.Context.StatusHistory.GroupBy(item => new { item.ServiceId, item.FromStatus, item.ToStatus }).Select(group => group.OrderByDescending(i => i.Timestamp).FirstOrDefault()).ToListAsync();
```
性能表现：在Azure SQL Datebase 上，**当这张表的数据到达 8000 左右的时候，这个 query 居然需要运行 8 秒左右。**

## 原因
根本原因是 SQL 中的 GroupBy 和 LINQ 中的 GroupBy 其实概念不完全一样。上面这段代码直接这么写，用的是 EF 的 GroupBy，其实是 SQL 的 GroupBy,然后由于逻辑上的需求，这句话会被 EF 翻译成为一个复杂的 SQL 的多层嵌套的语句，导致运行的非常的慢。

## 解决方案
### 方案一: 改用 LINQ 的 GroupBy
先用 EF 读出所有数据，然后用 LINQ 的 GroupBy 来处理，逻辑很一致，代码上其实改动非常的小，只是先ToListAsync() 取出数据就可以。
这个方案的问题是如果 StatusHistory 的数据量大了，会导致性能问题，是有隐患的。
``` C#
var list = await this.Context.StatusHistory.ToListAsync();

var result = list.GroupBy(item => new { item.ServiceId, item.FromStatus, item.ToStatus }).Select(group => group.OrderByDescending(i => i.Timestamp).FirstOrDefault()).ToList();
```
### 方案二：改用 SQL Partition By 
这个问题如果直接用 SQL 来写的话，应该是用 Partition By 更合适的。但是 EF 不支持 Partition By 和 ROW_NUMBER(), 所以我认为更好的方法是直接运行一个 SQL Query。
``` c#
var result = await Context.StatusHistory.SqlQuery("SELECT Id,ServiceId,FromStatus,ToStatus,Timestamp,InitiatedBy FROM ( SELECT Id,ServiceId,FromStatus,ToStatus,Timestamp,InitiatedBy ,ROW_NUMBER() OVER (PARTITION BY ServiceId,FromStatus,ToStatus order by Timestamp desc) as [ROWNYMBER] FROM [Service].[StatusHistory] ) as H WHERE [ROWNYMBER] = 1 ").ToListAsync()
```
