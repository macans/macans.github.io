---
title: Java 之 java.util
date: 2018-08-14 15:11:04
tags: Java
hide: true
---

之前看了侯捷的STL源码剖析，主要了解了 STL 中vector，queue，set，map的实现以及对内存的管理。我对STL的应用仅限于做题，一直没有机会在工作上用这些工具。现在学习一下 Java 的工具包java.util，并且会持续更新一段时间。
<!-- more -->  

# java.util.Collection

所谓的集合，有的允许存在重复元素如List, Vector，有的则不行如Set, Map；有的是有序的，有的是无序的。JDK 没有直接实现 Collection 这个interface，而是实现了更具体的 sub-interface 如Set, List等。

每个 Collection 的实现都应该提供两个标准的 constructor，其一是void(no arguments) constructor，其二是constructor with a single argument of type。没办法强制执行这个约定因为 interface 没有 constructor，但Java 库中所有通用的 Collection 都满足这个条件

# java.util.Arrays

封装了一些方法对数组进行排序或者查询，也提供了转换成List的接口

```java
    /*
       This method acts as bridge between array-based and collection-based APIs
    */
    public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
    }
```

```java
    /*
        Using DualPrivotQuicksort for seven primitive types(int, long, short, char,
        byte, float, double)
    */
    public static void sort(int[] a) {
        DualPivotQuicksort.sort(a, 0, a.length - 1, null, 0, 0);
    }

```

# java.util.Concurrent

```
Concurrent和Parallel的异同
维基百科的解释
> In computer science, concurrency refers to the ability of different parts or units of a program, algorithm, or problem to be executed out-of-order or in partial order, without affectig the final outcomen

https://softwareengineering.stackexchange.com/questions/190719/the-difference-between-concurrent-and-parallel-execution

### Reference:
1. https://www.geeksforgeeks.org/java-util-package-java/