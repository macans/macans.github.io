---
title: Draft
date: 2021-08-05 11:45:54
tags:
hidden: true
---

expireAfterWrite 和 refreshAfterWrite 的 区别。

我们先看一下官方文档的解释

> ## Timed Eviction
> ```CacheBuilder``` provides two approaches to timed eviction:
> - ```expireAfterAccess(long, TimeUnit)``` Only expire entries after the specified duration has passed since the entry was last accessed by a read or a write. Note that the order in which entries are evicted will be similar to that of size-based eviction.
> - ```expireAfterWrite(long, TimeUnit)``` Expire entries after the specified duration has passed since the entry was created, or the most recent replacement of the value. This could be desirable if cached data grows stale after a certain amount of time.
>
> Timed expiration is performed with periodic maintenance during writes and occasionally during reads, as discussed below.
>
> ### Testing Timed Eviction
> Testing timed eviction doesn't have to be painful...and doesn't actually have to take you two seconds to test a two-second expiration. Use the Ticker interface and the ```CacheBuilder.ticker(Ticker)``` method to specify a time source in your cache builder, rather than having to wait for the system clock.

