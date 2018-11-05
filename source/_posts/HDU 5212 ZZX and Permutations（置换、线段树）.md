---
title: HDU 5212 ZZX and Permutations（置换、线段树）
categories:
  - 数据结构
  - 线段树
  - 
tags:
  - 线段树 
  - 
date: 2016-06-04 00:42:10
toc: 
---

题意：
>$给定一个N\le 10^5的置换序列的Cycle\ Notation，然后现在把括号删掉了$
$现在求一个加上括号的Cycle\ Notation的原始置换序列$
$但要求输出最大字典序的$

<!-- more -->
分析：
>$稍微懂点置换的思路的都可以看出这个规律：$
$从Cycle\ Notation中找多个环出来，由于最大字典序$
$找到1的时候，肯定填合法的最大的那个数$
$能填的数就右边第一个，以及左边到自己\ 没使用过的任意一个$
$看看谁大就填谁，如果是一种直接填，第二种就把这堆数直接成环了$
$对于怎么模拟呢，赛上想多了，线段树上二分写不出，无奈写了二分+线段树T了$
$主要是我自己太纠结于这个已经成环和已经使用的区分了。。导致写法爆炸。。$
$然后队友不懂置换不能给我实质性的帮助，我自己也是逗比，深层次的总结写法能力弱$
$事实上只要动态维护最大值就可以了，用过的从线段树中删除就行了。。$
$至于查询区间，也就是第二种情况，只用维护成环的就好了。。$
$这个只要用set来维护那些成环的区间就好了，每次二分查找一下当前可用的区间$
$这样这个题的代码就非常简单了。。$
$时间复杂度线段树和set都是logn每次，总复杂度O(nlogn)$

代码:
```cpp
//
//  Created by TaoSama on 2016-05-26
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
#include <cassert>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, a[N], wh[N], ans[N];

#define MP make_pair

struct SegTree {
    int lazy[N << 2], maxv[N << 2];
    void up(int rt) {
        maxv[rt] = max(maxv[rt << 1], maxv[rt << 1 | 1]);
    }
    void down(int rt) {
        if(lazy[rt]) {
            int ls = rt << 1, rs = ls | 1;
            lazy[ls] = lazy[rs] = lazy[rt];
            maxv[ls] = maxv[rs] = lazy[rt] = 0;
        }
    }
    void build(int l, int r, int rt) {
        lazy[rt] = 0;
        if(l == r) {
            maxv[rt] = a[l];
            return;
        }
        int m = l + r >> 1;
        build(l, m, rt << 1);
        build(m + 1, r, rt << 1 | 1);
        up(rt);
    }
    void del(int L, int R, int l, int r, int rt) {
        if(L <= l && r <= R) {
            lazy[rt] = 1;
            maxv[rt] = 0;
            return ;
        }
        int m = l + r >> 1;
        down(rt);
        if(L <= m) del(L, R, l, m, rt << 1);
        if(R > m) del(L, R, m + 1, r, rt << 1 | 1);
        up(rt);
    }
    int query(int L, int R, int l, int r, int rt) {
        if(L <= l && r <= R) return maxv[rt];
        int m = l + r >> 1;
        down(rt);
        int ret = 0;
        if(L <= m) ret = max(ret, query(L, R, l, m, rt << 1));
        if(R > m) ret = max(ret, query(L, R, m + 1, r, rt << 1 | 1));
        return ret;
    }
} T;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i), wh[a[i]] = i;

        T.build(1, n, 1);
        set<pair<int, int> > done;
        memset(ans, 0, sizeof ans);
        for(int i = 1; i <= n; ++i) {
            if(ans[i]) continue;
            int p = wh[i];

            int l = 1, r = min(n, p + 1);
            auto iter = done.lower_bound({p, p});
            if(iter != done.begin()) l = (--iter)->second + 1;

            int maxv = T.query(l, r, 1, n, 1);
            int from = wh[maxv];

            if(from == p + 1) {
                ans[a[p]] = a[from];
                T.del(from, from, 1, n, 1); //delete
            } else {
                //link
                for(int j = from; j < p; ++j) ans[a[j]] = a[j + 1];
                ans[a[p]] = a[from];

                T.del(from, p, 1, n, 1); //delete
                done.insert({from, p}); //maintain the cycled intervals
            }
        }

        for(int i = 1; i <= n; ++i) printf("%d%c", ans[i], " \n"[i == n]);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}

```