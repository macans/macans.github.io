---
title: 话说Garbage Collection
date: 2018-08-21 23:19:34
tags: [Java]
---
## 背景

Lisp是第一门真正使用内存动态分配和垃圾收集技术的语言，主要思考三个问题
<!-- more -->  

1. 哪些内存需要回收？
2. 什么时候回收？
3. 怎么回收？

程序计数器，虚拟机栈，本地方法栈和线程的生命周期一致，每一个栈帧分配多少内存基本上是类确定时就已知了（运行时编译器会有一些优化），所以这几个区域的内存分配和回收的事件和方式都确定了。GC主要考虑堆和方法区的内存。

## 对象存活判断

### 引用计数

引用计数是最简单的一种自动内存管理机制，实现很简单但是不能解决对象间互相引用的问题。如下面这段代码

```java
class ReferenceCountingGC {
    public Object instance = null;
}

public class Main {
    private static final int _1MB = 1024 * 1024;
    private byte[] bigSize = new byte[2 * _1MB];

    public static void main(String[] args) {
        ReferenceCountingGC a = new ReferenceCountingGC();
        ReferenceCountingGC b = new ReferenceCountingGC();
        a.instance = b; //ref_count(b)=1
        b.instance = a; //ref_count(a)=1
        a = null;
        b = null;
        System.gc();
    }
}
```

执行时注意要打开JVM Option

```java
-XX:+PrintGC
-XX:+PrintGCDetails
-XX:+PrintGCTimeStamps
-XX:+PrintGCDateStamps
-XX:+PrintHeapAtGC
```

执行结果，若采用引用计数，a和b的内存就不会被释放了

```shell
0.216: [GC (System.gc()) [PSYoungGen: 3938K->496K(76288K)] 3938K->504K(251392K), 0.0023479 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
0.218: [Full GC (System.gc()) [PSYoungGen: 496K->0K(76288K)] [ParOldGen: 8K->401K(175104K)] 504K->401K(251392K), [Metaspace: 3243K->3243K(1056768K)], 0.0152900 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
Heap
 PSYoungGen      total 76288K, used 1966K [0x000000076ab00000, 0x0000000770000000, 0x00000007c0000000)
  eden space 65536K, 3% used [0x000000076ab00000,0x000000076aceb9e0,0x000000076eb00000)
  from space 10752K, 0% used [0x000000076eb00000,0x000000076eb00000,0x000000076f580000)
  to   space 10752K, 0% used [0x000000076f580000,0x000000076f580000,0x0000000770000000)
 ParOldGen       total 175104K, used 401K [0x00000006c0000000, 0x00000006cab00000, 0x000000076ab00000)
  object space 175104K, 0% used [0x00000006c0000000,0x00000006c0064410,0x00000006cab00000)
 Metaspace       used 3265K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 359K, capacity 388K, committed 512K, reserved 1048576K
```

引用计数如何实现的伪代码

```c++
New(): //分配内存
    ref <- allocate()
    if ref == null
        error "Out of memory"
    rc(ref) <- 0  //将ref的引用计数(reference counting)设置为0
    return ref

atomic Write(dest, ref) //更新对象的引用
    addReference(ref)
    deleteReference(dest)
    dest <- ref

addReference(ref):
    if ref != null
        rc(ref) <- rc(ref)+1

deleteReference(ref):
    if ref != null
        rc(ref) <- rc(ref) -1
        if rc(ref) == 0 //如果当前ref的引用计数为0，则表明其将要被回收
            for each fld in Pointers(ref)
                deleteReference(*fld)
            free(ref) //释放ref指向的内存空间
```

### 可达性分析

将某些对象作为GC Roots
可以作为GC Roots的对象有：虚拟机栈中引用的对象，方法区中类静态属性和常量引用的对象，本地方法栈中JNI引用的对象
强引用，软引用，若引用，虚引用
