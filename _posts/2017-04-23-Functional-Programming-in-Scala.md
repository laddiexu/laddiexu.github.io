---
layout: post
title: "笔记：Functional Programming in Scala"
description: ""
category: tech
tags: []
modify: 2017-05-31 08:33:55
---

update: 2017-05-31

早上四点就自然醒了，没了睡意，就整理下最近在看的函数式编程书籍[Functional Programming in Scala](https://www.manning.com/books/functional-programming-in-scala), 2015。同事推荐说有点烧脑，做了开头两章，确实挺好玩的。

官方在Github上有个仓库，放了习题、指导和答案，很方便，见[fpinscala/fpinscala](https://github.com/fpinscala/fpinscala)。遗憾的是我没有看到习题配套的单元测试用例来校验正确性，所以自己建了个mvn工程，作业都放在[facaiy/book_notes](https://github.com/facaiy/book_notes/tree/master/Manning_Functional_Programming_in_Scala/functional-programming)。希望尽量都做一遍，耗时预计在两个月。


### 目录

#### PART 1 INTRODUCTION TO FUNCTIONAL PROGRAMMING

##### 1 What is functional programming?

##### [2 Getting started with functional programming in Scala](https://github.com/facaiy/book_notes/blob/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/ChapterTwo.scala)

##### [3 Functional data structures](https://github.com/facaiy/book_notes/tree/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/c3)

##### [4 Handling errors without exceptions](https://github.com/facaiy/book_notes/tree/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/c4)

##### [5 Strictness and laziness](https://github.com/facaiy/book_notes/blob/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/c5/Stream.scala)

##### [6 Purely functional state](https://github.com/facaiy/book_notes/blob/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/c6/State.scala)


#### PART 2 FUNCTIONAL DESIGN AND COMBINATOR LIBRARIES

##### [7 Purely functional parallelism](https://github.com/facaiy/book_notes/tree/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/c7)

##### [8 Property-based testing](https://github.com/facaiy/book_notes/tree/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/c8)

##### [9 Parser combinators](https://github.com/facaiy/book_notes/tree/master/Manning_Functional_Programming_in_Scala/functional-programming/src/main/scala/io/github/facaiy/c9)


#### PART 3 COMMON STRUCTURES IN FUNCTIONAL DESIGN

##### 10 Monoids

##### 11 Monads

##### 12 Applicative and traversable functors


#### PART 4 EFFECTS AND I/O

##### 13 External effects and I/O

##### 14 Local effects and mutable state

##### 15 Stream processing and incremental I/O
