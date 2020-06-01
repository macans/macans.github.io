---
title: Codeforces 701E Connecting Universities 贪心
date: 2016-07-24 16:32
tags: [Codeforces, Greedy]
---

### 链接
> [Codeforces 701E Connecting Universities ](http://codeforces.com/contest/701/problem/E)

<!-- more -->  

### 题意
> n个点的树，给你2*K个点，分成K对，使得两两之间的距离和最大

### 思路
> 贪心，思路挺巧妙的。首先dfs一遍记录每个点的子树中(包括自己)有多少点是这K个中间的，第二遍dfs时对于每一条边，取两端包含较少的值，这样就保证树中间的点不会被取到，留下的就是相隔更远的点了。方法确实想不到啊。

### 代码
```cpp
#include <iostream>
#include <cstdio>
#include <vector>
#include <stack>
#include <queue>
#include <algorithm>
#include <map>
#include <set>
#include <cmath>
#include <cstring>
#include <string>

#define LL long long
#define INF 0x3f3f3f3f
#define eps 1e-8

using namespace std;
struct edge{
	int v, next;
}edges[400005];
int n, k;
int head[200005], tot = 0, vis[200005];
int sz[200005];
void Add(int u, int v){
	edges[tot].v = v;
	edges[tot].next = head[u];
	head[u] = tot++;
}
void dfs(int u, int fa){
	sz[u] = vis[u];
	for (int i = head[u]; i != -1; i = edges[i].next){
		int v = edges[i].v;
		if (v == fa) continue;
		dfs(v, u);
		sz[u] += sz[v];
	}
}
LL ans = 0;
void dfs2(int u, int fa){
	for (int i = head[u]; i != -1; i = edges[i].next){
		int v = edges[i].v;
		if (v == fa) continue;
		dfs2(v, u);
		ans += min(sz[v], 2 * k - sz[v]);
	}
}
int main(){
#ifndef ONLINE_JUDGE
	freopen("in.txt", "r", stdin);
	//freopen("out.txt", "w", stdout);
#endif // ONLINE_JUDGE
	memset(head, -1, sizeof(head));
	scanf("%d%d", &n, &k);
	for (int i = 1; i <= 2 * k; i++){
		int x;
		scanf("%d", &x);
		vis[x] = 1;
	}
	for (int i = 1; i < n; i++){
		int u, v;
		scanf("%d%d", &u, &v);
		Add(u, v);
		Add(v, u);
	}
	dfs(1, 0);
	dfs2(1, 0);
	printf("%I64d\n", ans);
	return 0;
}
```