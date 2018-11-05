---
title: HDU 5790 Prefix（字典树、主席树）
categories:
  - 数据结构
  - 主席树
  - 
tags:
  - 字典树
  - 主席树
  - 
date: 2016-08-05 18:39:10
toc: 
---

题意：
>$N\le 10^5个字符串，保证\sum |L_i|\le 10^5，Q\le 10^5次询问$
$在线查询[L,\ R]区间有多少个不同的前缀$

<!-- more -->
分析：
>$- - 求区间有多少个不同的数的套路$
$考虑离线的做法，询问右端点排序$
$字典树插入每个字符串i，记录每个前缀上次哪里出现$
$然后上次-1，这次+1，对于每个以i为右端点的询问答案就是sum(L,\ i)$
$这题强制在线，只要可持久化一下就好了$
$时间复杂度O(\sum |L|+qlogn)$

代码:
```cpp
//
//  Created by TaoSama on 2016-08-03
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

int n, q;
char s[N];

int root[N];
struct PersistentSegTree {
    static const int M = 1e5 * 40;
    int sz;
    struct Node {
        int ls, rs, sum;
    } tree[M];
    void init() {
        sz = 0;
        root[0] = 0;
    }
    int newNode(int rt) {
        tree[++sz] = tree[rt];
        return sz;
    }
    void update(int o, int v, int l, int r, int& rt) {
        rt = newNode(rt);
        tree[rt].sum += v;
        if(l == r) return;
        int m = l + r >> 1;
        if(o <= m) update(o, v, l, m, tree[rt].ls);
        else update(o, v, m + 1, r, tree[rt].rs);
    }
    int query(int L, int R, int l, int r, int rt) {
        if(L <= l && r <= R) return tree[rt].sum;
        int m = l + r >> 1, ret = 0;
        if(L <= m) ret += query(L, R, l, m, tree[rt].ls);
        if(R > m) ret += query(L, R, m + 1, r, tree[rt].rs);
        return ret;
    }
} T;

struct Trie {
    static const int M = 1e5 + 10, S = 26;
    int sz, rt, nxt[M][S];
    int val[M];
    int newNode() {
        val[sz] = 0;
        memset(nxt[sz], 0, sizeof nxt[sz]);
        return sz++;
    }
    void init() {
        sz = 0;
        rt = newNode();
    }
    void insert(char* s, int id) {
        int u = rt;
        for(int i = 0; s[i]; ++i) {
            int& v = nxt[u][s[i] - 'a'];
            if(!v) v = newNode();
            if(val[v]) T.update(val[v], -1, 1, n, root[id]);
            val[v] = id;
            T.update(val[v], 1, 1, n, root[id]);
            u = v;
        }
    }
} trie;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        T.init();
        trie.init();

        for(int i = 1; i <= n; ++i) {
            scanf("%s", s);
            root[i] = root[i - 1];
            trie.insert(s, i);
        }

        int z = 0;
        scanf("%d", &q);
        while(q--) {
            int l, r; scanf("%d%d", &l, &r);
            l = (z + l) % n + 1;
            r = (z + r) % n + 1;
            if(l > r) swap(l, r);
            z = T.query(l, n, 1, n, root[r]);
            printf("%d\n", z);
        }
    }

    return 0;
}
```