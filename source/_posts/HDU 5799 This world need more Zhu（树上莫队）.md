---
title: HDU 5799 This world need more Zhu（树上莫队）
categories:
  - 数据结构
  - 树上莫队
  - 
tags:
  - 树上莫队
  - 
  - 
date: 2016-08-08 19:30:10
toc: 
---

题意：
>$给定一颗N\le 10^5个点的树，点权A_i\le 10^9，Q\le 10^5，询问形如op\ u\ v\ a\ b$
$op=1时，u=v，查询子树u中，gcd(\sum_{cnt[x]=a} x,\ \sum_{cnt[y]=b} y)$
$op=2时，查询路径(u,\ v)中，gcd(\sum_{cnt[x]=a} x,\ \sum_{cnt[y]=b} y)$

<!-- more -->
分析：
>$op=1，dfs序搞成序列上的莫队，op=2树上莫队$
$板题\times 2$

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
const int B = 250;

typedef long long LL;
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
    int l, r, block, a, b, id;
};

bool cmpSeq(const Query& a, const Query& b) {
    return a.block < b.block ||
           a.block == b.block && a.r < b.r;
}
bool cmpTree(const Query& a, const Query& b) {
    return id[a.l] < id[b.l] ||
           id[a.l] == id[b.l] && L[a.r] < L[b.r];
}

int cnt[N];
LL sum[N], ans[N];

void add(int x) {
    sum[cnt[b[x]]] -= a[x];
    ++cnt[b[x]];
    sum[cnt[b[x]]] += a[x];
}

void del(int x) {
    sum[cnt[b[x]]] -= a[x];
    --cnt[b[x]];
    sum[cnt[b[x]]] += a[x];
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

    int t; scanf("%d", &t);
    while(t--) {
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

        vector<Query> qs, qt;
        for(int i = 1; i <= m; ++i) {
            int op, u, v, a, b;
            scanf("%d%d%d%d%d", &op, &u, &v, &a, &b);
            if(op == 1) {
                int p = L[u] / B;
                qs.push_back({L[u], R[u], p, a, b, i});
            } else {
                if(id[u] > id[v]) swap(u, v);
                qt.push_back({u, v, -1, a, b, i});
            }
        }

        {
            //subtree
            sort(qs.begin(), qs.end(), cmpSeq);
            memset(cnt, 0, sizeof cnt);
            memset(sum, 0, sizeof sum);

            int l = 1, r = 0;
            for(auto& q : qs) {
                while(r < q.r) add(vs[++r]);
                while(l < q.l) del(vs[l++]);
                while(r > q.r) del(vs[r--]);
                while(l > q.l) add(vs[--l]);

                ans[q.id] = __gcd(sum[q.a], sum[q.b]);
            }
        }

        {
            //path
            sort(qt.begin(), qt.end(), cmpTree);
            memset(cnt, 0, sizeof cnt);
            memset(sum, 0, sizeof sum);
            memset(in, 0, sizeof in);

            add(1); in[1] = true;
            int l = 1, r = 0;
            for(auto& q : qt) {
                move(l, q.l);
                move(r, q.r);
                l = q.l; r = q.r;

                ans[q.id] = __gcd(sum[q.a], sum[q.b]);
            }
        }

        static int kase = 0;
        printf("Case #%d:\n", ++kase);
        for(int i = 1; i <= m; ++i) printf("%I64d\n", ans[i]);
    }

    return 0;
}
```