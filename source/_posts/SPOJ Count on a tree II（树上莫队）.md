---
title: SPOJ Count on a tree II（树上莫队）
categories:
  - 数据结构
  - 树上莫队
  - 
tags:
  - 树上莫队
  - 
  - 
date: 2016-08-08 19:20:10
toc: 
---

题意：
>$给定一颗N\le 40000个点的树，点权A_i\le 10^9$
$Q\le 10^5，询问(u,\ v)路径上有多少不同的点权$

<!-- more -->
分析：
>$树上莫队板子题$
$具体看集训队2013论文，《浅谈分块思想在一类数据处理问题中的应用》$
$板子来自多校6的卿学姐博客题解代码$

```cpp
//
//  Created by TaoSama on 2016-08-08
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:102400000,102400000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>
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
const int B = 200;

int n, m, a[N], b[N];

struct Edge {
    int v, nxt;
} edge[N << 1];
int head[N], eCnt;

void addEdge(int u, int v) {
    edge[eCnt] = {v, head[u]};
    head[u] = eCnt++;
}

int L[N], R[N], dep[N], fa[N], vs[N], dfsNum;
int id[N], blocks;
int stk[N], top;

void dfs(int u, int f) {
    L[u] = ++dfsNum;
    vs[dfsNum] = u;
    fa[u] = f;

    int btm = top;
    for(int i = head[u]; ~i; i = edge[i].nxt) {
        int v = edge[i].v;
        if(v == f) continue;

        dep[v] = dep[u] + 1;
        dfs(v, u);

        if(top - btm >= B) {
            ++blocks;
            while(top != btm) {
                int v = stk[top--];
                id[v] = blocks;
            }
        }
    }
    R[u] = dfsNum;
    stk[++top] = u;
}

struct Query {
    int l, r, id;
};

bool cmpTree(const Query& a, const Query& b) {
    return id[a.l] < id[b.l] ||
           id[a.l] == id[b.l] && L[a.r] < L[b.r];
}

int cnt[N];
int sum, ans[N];

void add(int x) {
    if(cnt[b[x]]++ == 0) ++sum;
}

void del(int x) {
    if(--cnt[b[x]] == 0) --sum;
}

bool in[N];
int cross;
void reverse(int x) {
    if(in[x]) {
        in[x] = false;
        del(x);
    } else {
        in[x] = true;
        add(x);
    }
}

void moveUp(int& x) {
    if(!cross) {
        if(in[x] && !in[fa[x]]) cross = x;
        else if(in[fa[x]] && !in[x]) cross = fa[x];
    }
    reverse(x); x = fa[x];
}

void move(int a, int b) {
    if(a == b) return;
    cross = 0;
    if(in[b]) cross = b;
    while(dep[a] > dep[b]) moveUp(a);
    while(dep[b] > dep[a]) moveUp(b);
    while(a != b) moveUp(a), moveUp(b);
    reverse(a); reverse(cross);
}

void gao() {
    dfsNum = blocks = 0;
    dfs(1, 0);
    while(top) id[stk[top--]] = blocks;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d", &n, &m);

    vector<int> xs(n);
    for(int i = 1; i <= n; ++i) {
        scanf("%d", a + i);
        xs[i - 1] = a[i];
    }
    sort(xs.begin(), xs.end());
    xs.resize(unique(xs.begin(), xs.end()) - xs.begin());
    for(int i = 1; i <= n; ++i)
        b[i] = lower_bound(xs.begin(), xs.end(), a[i]) - xs.begin() + 1;

    eCnt = 0; memset(head, -1, sizeof head);
    for(int i = 1; i < n; ++i) {
        int u, v; scanf("%d%d", &u, &v);
        addEdge(u, v);
        addEdge(v, u);
    }

    gao();

    vector<Query> qt(m);
    for(int i = 1; i <= m; ++i) {
        int u, v; scanf("%d%d", &u, &v);
        if(id[u] > id[v]) swap(u, v);
        qt[i - 1] = {u, v, i};
    }

    sort(qt.begin(), qt.end(), cmpTree);

    sum = 0;
    memset(cnt, 0, sizeof cnt);
    memset(in, 0, sizeof in);

    add(1); in[1] = true;
    int l = 1, r = 0;
    for(auto& q : qt) {
        move(l, q.l);
        move(r, q.r);
        l = q.l; r = q.r;

        ans[q.id] = sum;
    }
    for(int i = 1; i <= m; ++i) printf("%d\n", ans[i]);

    return 0;
}
```