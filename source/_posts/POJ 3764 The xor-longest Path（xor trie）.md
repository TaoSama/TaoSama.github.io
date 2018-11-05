---
title: POJ 3764 The xor-longest Path（xor trie）
categories:
  - 技巧
  - xor
  - 
tags:
  - xor trie
  - 
date: 2016-05-01 15:23:10
toc: 
---
题意：
>$N\le 10^5个点的树，A_i < 2^{31}$
$路径异或和：=路径上所有边权的异或和，求最大的路径异或和$
<!-- more -->

分析：
>$xor\ trie配合简单dp思想$
$我们发现这个是个连续异或和，经典的技巧$
$设prefix[i]:=A_1\oplus A_2\oplus\cdots\oplus A_i$
$那么显然我们可以得到xor(l,\ r)=prefix[r]\oplus prefix[l-1]$
$同理树上也是一样的，把树搜一遍，也就是相当于求f[i]:=以i这个点结尾的路径的最大异或和$
$最终答案ans = max\{f[i]\}$
$时间复杂度O(nb)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-22
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:102400000,102400000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iomanip>
#include <iostream>
#include <map>
#include <queue>
#include <string>
#include <set>
#include <vector>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

struct Trie {
    static const int M = 32 * 1e5 + 10, S = 2;
    int root, sz;
    int nxt[M][S];
    int newNode() {
        memset(nxt[sz], -1, sizeof nxt[sz]);
        return sz++;
    }
    void init() {
        sz = 0;
        root = newNode();
    }
    void insert(int x) {
        int u = root;
        for(int i = 31; ~i; --i) {
            int c = x >> i & 1, &v = nxt[u][c];
            if(v == -1) v = newNode();
            u = v;
        }
    }
    int query(int x) {
        int u = root, ret = 0;
        for(int i = 31; ~i; --i) {
            int c = x >> i & 1;
            if(~nxt[u][c ^ 1]) {
                ret |= 1 << i;
                u = nxt[u][c ^ 1];
            } else u = nxt[u][c];
        }
        return ret;
    }
} trie;

struct Edge {
    int v, nxt, c;
} edge[N << 1];
int head[N], cnt;

void addEdge(int u, int v, int c) {
    edge[cnt] = {v, head[u], c};
    head[u] = cnt++;
    edge[cnt] = {u, head[v], c};
    head[v] = cnt++;
}

void dfs(int u, int f, int sum, int& ans) {
    for(int i = head[u]; ~i; i = edge[i].nxt) {
        int v = edge[i].v;
        if(v == f) continue;
        ans = max(ans, trie.query(sum ^ edge[i].c));
        trie.insert(sum ^ edge[i].c);
        dfs(v, u, sum ^ edge[i].c, ans);
    }
}

int n;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        cnt = 0; memset(head, -1, sizeof head);
        for(int i = 1; i < n; ++i) {
            int u, v, c;  scanf("%d%d%d", &u, &v, &c);
            addEdge(u, v, c);
        }
        int ans = 0;
        trie.init(); trie.insert(0);
        dfs(0, -1, 0, ans);
        printf("%d\n", ans);
    }
    return 0;
}
```