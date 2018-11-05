---
title: HDU 4757 Tree（可持久化trie）
categories:
  - 技巧
  - xor
  - 
tags:
  - 可持久trie
  - 
date: 2016-05-01 16:30:10
toc: 
---
题意：
>$N\le 10^5个点的树，点权A_i < 2^{16}，M\le 10^5次询问$
$每次查询u\to v路径上点权与k异或的最大值$
<!-- more -->

分析：
>$由于要取出路径那就只能可持久化trie了$
$每一颗trie都是前缀和，显然根据lca的那个思想，u\to v路径，就是$
$判断u这棵trie以及v这颗trie，以及lca(u,\ v)这颗trie的cnt域$
$即cnt[u]+cnt[v]-2*cnt[lca(u,\ v)]是否为正$
$但是这样别忘记特判lca这点的点权。。$
$其他的就是按照普通的做法贪心的去找这个最大值就好了$

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
    static const int M = 17 * 1e5 + 10, S = 2;
    struct Node {
        int nxt[S], cnt;
    } dat[M];
    int sz, root[N];
    void init() {
        sz = root[0] = 0;
        memset(&dat[0], 0, sizeof dat[0]);
    }
    void insert(int& rt, int fa, int x) {
        int u; u = rt = ++sz;
        dat[u] = dat[fa];
        for(int i = 15; ~i; --i) {
            int c = x >> i & 1;
            int v = ++sz;
            dat[v] = dat[dat[u].nxt[c]]; //copy
            ++dat[v].cnt;
            dat[u].nxt[c] = v; //link
            u = v;
        }
    }
    int query(int u, int v, int z, int x) { //lca没算
        int ret = 0;
        for(int i = 15; ~i; --i) {
            int c = x >> i & 1;
            int have = dat[dat[u].nxt[c ^ 1]].cnt + dat[dat[v].nxt[c ^ 1]].cnt;
            have -= 2 * dat[dat[z].nxt[c ^ 1]].cnt;
            if(have) {
                ret |= 1 << i;
                c ^= 1;
            }
            u = dat[u].nxt[c];
            v = dat[v].nxt[c];
            z = dat[z].nxt[c];
        }
        return ret;
    }
} trie;

int n, q;
struct Edge {
    int v, nxt;
} edge[N << 1];
int head[N], cnt;

void addEdge(int u, int v) {
    edge[cnt] = {v, head[u]};
    head[u] = cnt++;
    edge[cnt] = {u, head[v]};
    head[v] = cnt++;
}

int val[N];
int dep[N], p[17][N];

void dfs(int u, int fa) {
    trie.insert(trie.root[u], trie.root[fa], val[u]);

    p[0][u] = fa;
    for(int i = 1; i < 17; ++i) p[i][u] = p[i - 1][p[i - 1][u]];
    for(int i = head[u]; ~i; i = edge[i].nxt) {
        int v = edge[i].v;
        if(v == fa) continue;
        dep[v] = dep[u] + 1;
        dfs(v, u);
    }
}

int lca(int u, int v) {
    if(dep[u] > dep[v]) swap(u, v);
    for(int i = 0; i < 17; ++i)
        if(dep[v] - dep[u] >> i & 1) v = p[i][v];
    if(u == v) return u;
    for(int i = 16; ~i; --i)
        if(p[i][u] != p[i][v])
            u = p[i][u], v = p[i][v];
    return p[0][u];
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &q) == 2) {
        for(int i = 1; i <= n; ++i) scanf("%d", val + i);

        cnt = 0; memset(head, -1, sizeof head);
        for(int i = 1; i < n; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            addEdge(u, v);
        }

        trie.init();
        dfs(1, 0);

//        for(int i = 0; i < 5; ++i)
//          for(int u = 1; u <= n; ++u)
//              printf("p[%d][%d] = %d\n", i, u, p[i][u]);

        while(q--) {
            int u, v, w; scanf("%d%d%d", &u, &v, &w);
            int z = lca(u, v), ans = w ^ val[z];
            ans = max(ans, trie.query(trie.root[u], trie.root[v], trie.root[z], w));
            printf("%d\n", ans);
        }
    }
    return 0;
}
```