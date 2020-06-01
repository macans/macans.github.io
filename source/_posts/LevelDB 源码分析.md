---
title: LevelDB 源码分析
date: 2018-01-07 16:54:55
tags: [分布式, 数据库]
---
[Condition Variable与Mutex的比较](https://www.gnu.org/software/guile/manual/html_node/Mutexes-and-Condition-Variables.html)
Mutexes are low-level primitives used to coordinate concurrent access to mutable data.If one thread has locked a mutex, then another thread attempting to lock that same mutex will wait until the first thread is done
https://en.cppreference.com/w/cpp/thread/condition_variable

<!-- more -->  

[reinterpret_cast](https://en.cppreference.com/w/cpp/language/reinterpret_cast) 

[Features-Not-in-LevelDB](https://github.com/facebook/rocksdb/wiki/Features-Not-in-LevelDB)

[SSTable and Log Structured Storage: LevelDB](https://www.igvita.com/2012/02/06/sstable-and-log-structured-storage-leveldb/)

##### dbformat.h
```cpp
//A helper class useful for DBImpl::Get()
class LookupKey {
...
private:
    const char *start_;     //user key length varint32 
    const char *kstart_;    //user key  char[klength]
    const char *end_;       //sequence number + value type uint64
    char space_[200];       //Avoid allocation for short keys
}
```

```cpp
    struct FileMetaData {
        int refs;                   
        int allowed_seeks;          // Seeks allowed until compaction
        uint64_t number;
        uint64_t file_size;         // File size in bytes
        InternalKey smallest;       // Smallest internal key served by table
        InternalKey largest;        // Largest internal key served by table

        FileMetaData() : refs(0), allowed_seeks(1 << 30), file_size(0) {}
    };
```

