---
title: '[译] Reviewing LevelDB'
date: 2018-05-12 15:04:39
tags: [LevelDB, C/C++]
---
# Reviewing LevelDB

## I. What is this all about

https://ayende.com/blog/161410/reviewing-leveldb-part-i-what-is-this-all-about

LevelDB是
> 是Google开发的高速键值对存储库，提供有序的任意字符串之前的键值映射。

这是项目自身给出的定义。LevelDB为用户提供了一种高效存储数据的方式，不是基于SQL的数据库，实际上连真正的数据库也算不上，而只是构建数据库时的一些模块，用来处理对磁盘的读写操作，支持原子性操作。其他的方面则取决于研发人员自己（不管是事务管理还是更复杂的功能）
<!-- more -->  
对于我们需要做的事情而言，LevelDB很完美。我决定开始了解项目的代码库，尤其是现在我都不能编译成功。

项目用C++实现，研发人员以C++为生可能是项目使用C++的一个原因。我感觉源码的质量将会很高，所以我不妨在学习源码的时候同时提升我的C++水平。

![Methods](https://ayende.com/blog/Images/Windows-Live-Writer/Reviewing-LevelDB-Part-I-What-is-this-al_87BA/image_4.png)

显示出来的只是很小的一部分，你可能也感觉到了，这些是我感兴趣的。它使得我们理解起来更容易，我们随后就来看一下背后复杂的逻辑实现。

## II. Put some data on the disk

我认为我们首先要做的是分析LevelDB到底怎么把数据写到磁盘中的。为此，我们决定追踪方法Put的调用过程。

我们从客户端代码开始

```cpp
leveldb::DB *db;
leveldb::Options options;
options.create_if_missing = true;
leveldb::DB::Open(options, "test_db", &db);
leveldb::Status status = db->Put(leveldb::WriteOptions(), "key1", "value1");
std::cout << status.ok() << std::endl;
```
DB::Put的实现
```cpp
// Default implementations of convenience methods that subclasses of DB
// can call if they wish
Status DB::Put(const WriteOptions& opt, const Slice& key, const Slice& value) {
  WriteBatch batch;
  batch.Put(key, value);
  return Write(opt, &batch);
}

Status DB::Delete(const WriteOptions& opt, const Slice& key) {
  WriteBatch batch;
  batch.Delete(key);
  return Write(opt, &batch);
}
```
上面也包括了Delete方法，旨在说明重要的一点，就是所有的修改都会用到WriteBatch
```cpp
Status DBImpl::Write(const WriteOptions& options, WriteBatch* my_batch) {
  Writer w(&mutex_);
  w.batch = my_batch;
  w.sync = options.sync;
  w.done = false;

  MutexLock l(&mutex_);
  writers_.push_back(&w);
  while (!w.done && &w != writers_.front()) {
    w.cv.Wait();
  }
  if (w.done) {
    return w.status;
  }

  // May temporarily unlock and wait.
  Status status = MakeRoomForWrite(my_batch == nullptr);
  uint64_t last_sequence = versions_->LastSequence();
  Writer* last_writer = &w;
  if (status.ok() && my_batch != nullptr) {  // nullptr batch is for compactions
    WriteBatch* updates = BuildBatchGroup(&last_writer);
    WriteBatchInternal::SetSequence(updates, last_sequence + 1);
    last_sequence += WriteBatchInternal::Count(updates);

    // Add to log and apply to memtable.  We can release the lock
    // during this phase since &w is currently responsible for logging
    // and protects against concurrent loggers and concurrent writes
    // into mem_.
    {
      mutex_.Unlock();
      status = log_->AddRecord(WriteBatchInternal::Contents(updates));
      bool sync_error = false;
      if (status.ok() && options.sync) {
        status = logfile_->Sync();
        if (!status.ok()) {
          sync_error = true;
        }
      }
      if (status.ok()) {
        status = WriteBatchInternal::InsertInto(updates, mem_);
      }
      mutex_.Lock();
      if (sync_error) {
        // The state of the log file is indeterminate: the log record we
        // just added may or may not show up when the DB is re-opened.
        // So we force the DB into a mode where all future writes fail.
        RecordBackgroundError(status);
      }
    }
    if (updates == tmp_batch_) tmp_batch_->Clear();

    versions_->SetLastSequence(last_sequence);
  }

  while (true) {
    Writer* ready = writers_.front();
    writers_.pop_front();
    if (ready != &w) {
      ready->status = status;
      ready->done = true;
      ready->cv.Signal();
    }
    if (ready == last_writer) break;
  }

  // Notify new head of write queue
  if (!writers_.empty()) {
    writers_.front()->cv.Signal();
  }

  return status;
}
```
再看下Writer的定义
```cpp
// Information kept for every waiting writer
struct DBImpl::Writer {
  Status status;
  WriteBatch* batch;
  bool sync;
  bool done;
  port::CondVar cv;

  explicit Writer(port::Mutex* mu) : cv(mu) { }
};
```
