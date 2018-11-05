---
title: HDU 4348 To the moon （主席树、标记永久化）
categories:
  - 数据结构
  - 主席树
  - 
tags:
  - 主席树
  - 标记永久化
  - 
date: 2016-08-09 10:42:10
toc: 
---

题意： 
>$给定N\le 10^5个数，Q\le 10^5询问，初始时间戳Timestamp=0$
$C\ l\ r\ v:Timestamp+1，将[l,\ r]区间的数都+v$
$Q\ l\ r:查询当前Timestamp的[l,\ r]区间和$
$H\ l\ r\ t:查询历史Timestamp=t的[l,\ r]区间和，保证合法$
$B\ t:回到历史Timestamp=t的时刻，保证合法，保证不会回到将来$

<!-- more -->
分析：
>$卡空间所以打永久化lazy标记$
$对于主席树继承更新的时候还是不要pushUp了，不然还要算lazy的$
$对于查询的话，一路上把标记都加上，（一句话理解标记永久化系列$
$话说好久没有画主席树来debug了，(大雾$

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

int n, q;

typedef long long LL;

int root[N];
struct PersistentSegTree {
    static const int M = 1e5 * 30;
    int sz;
    struct Node {
        int ls, rs, add;
        LL sum;
    } tree[M];
    void init() {
        sz = 0;
        memset(&tree[0], 0, sizeof tree[0]);
    }
    int newNode(int rt) {
        tree[++sz] = tree[rt];
        return sz;
    }
    void up(int rt) {
        tree[rt].sum = tree[tree[rt].ls].sum + tree[tree[rt].rs].sum;
    }
    void build(int l, int r, int& rt) {
        rt = newNode(0);
        if(l == r) {
            scanf("%I64d", &tree[rt].sum);
            return;
        }
        int m = l + r >> 1;
        build(l, m, tree[rt].ls);
        build(m + 1, r, tree[rt].rs);
        up(rt);
    }
    void show(int l, int r, int rt) {
        pr("show"); pr(rt); pr(l); pr(r); pr(tree[rt].add); prln(tree[rt].sum);
        if(l == r) return ;
        int m = l + r >> 1;
        show(l, m, tree[rt].ls);
        show(m + 1, r, tree[rt].rs);
    }
    void update(int L, int R, int v, int l, int r, int& rt) {
        rt = newNode(rt);
        tree[rt].sum += v * (min(r, R) - max(l, L) + 1);
        if(L <= l && r <= R) {
            tree[rt].add += v;
            return;
        }
        int m = l + r >> 1;
        if(L <= m) update(L, R, v, l, m, tree[rt].ls);
        if(R > m) update(L, R, v, m + 1, r, tree[rt].rs);
    }
    LL query(int L, int R, int l, int r, int rt) {
        if(L <= l && r <= R) return tree[rt].sum;
        LL ret = 1LL * tree[rt].add * (min(r, R) - max(l, L) + 1);
        int m = l + r >> 1;
        if(L <= m) ret += query(L, R, l, m, tree[rt].ls);
        if(R > m) ret += query(L, R, m + 1, r, tree[rt].rs);
        return ret;
    }
} T;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &q) == 2) {
        T.init();
        T.build(1, n, root[0]);

        int timStp = 0;
        while(q--) {
            char op[2]; scanf("%s", op);
            if(*op == 'C') {
                int l, r, d; scanf("%d%d%d", &l, &r, &d);
                ++timStp;
                root[timStp] = root[timStp - 1];
//                T.show(1, n, root[timStp]);
                T.update(l, r, d, 1, n, root[timStp]);
            } else if(*op == 'Q') {
                int l, r; scanf("%d%d", &l, &r);
                printf("%I64d\n", T.query(l, r, 1, n, root[timStp]));
            } else if(*op == 'H') {
                int l, r, t; scanf("%d%d%d", &l, &r, &t);
                printf("%I64d\n", T.query(l, r, 1, n, root[t]));
            } else {
                scanf("%d", &timStp);
            }
        }
    }

    return 0;
}
```