---
title: HDU 5737 Differencia（归并树）
categories:
  - 数据结构
  - 线段树
  - 
tags:
  - 归并树
  - 
  - 
date: 2016-07-25 23:47:10
toc: 
---

题意：
>$N\le 10^5长度的A，B两个数组，A_i，B_i\le 10^9$
$Q\le 3\times 10^6次查询，2种查询$
$+\ l\ r\ x:把A数组的[l,\ r]区间数变为x$
$?\ l\ r:查询[l,\ r]区间A_i\ge B_i的下标个数$

<!-- more -->
分析：
>$首先O(qlog^2)的归并树做法很显然，每个节点维护B数组的有序表$
$之后对于每次查询只需要在每个节点二分一下即可$
$由于Q巨大，所以这么做要T，事实上由于不会操作B数组，对于查询可以提前维护一点东西$
$维护有序表第i个数进入左右子树时的位置（即有多少数\le 第i个数）$ $那么查询在线段树上就可以O(1)得到这个数在左右子树的rank变化$ $这个对线段树往下push\ lazy标记也是适用的，就去掉了1个log$
$时间复杂度O(qlogn)就可以草过去了$



代码:
```cpp
//
//  Created by TaoSama on 2016-07-25
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

int n, q, A, B;
int a[N], b[N];

int rnd(int& last, int& a, int& b) {
    int C = ~(1 << 31), M = (1 << 16) - 1;
    a = (36969 + (last >> 3)) * (a & M) + (a >> 16);
    b = (18000 + (last >> 3)) * (b & M) + (b >> 16);
    return (C & ((a << 16) + b)) % 1000000000;
}

namespace Discretization {
    vector<int> xs;
    void init() {
        xs = vector<int>(b + 1, b + 1 + n);
        sort(xs.begin(), xs.end());
        for(int i = 1; i <= n; ++i) {
            b[i] = lower_bound(xs.begin(), xs.end(), b[i]) - xs.begin() + 1;
            a[i] = upper_bound(xs.begin(), xs.end(), a[i]) - xs.begin();
        }
    }
    int get(int x) {
        return upper_bound(xs.begin(), xs.end(), x) - xs.begin();
    }
}

namespace Allocator {
    int data[N << 6], *p;
    void init() {
        p = data;
    }
    int* allocate(int len) {
        p += len;
        return p - len;
    }
}

struct Node {
    int* indexLeft, *indexRight;
    int tag, sum;

    void setTag(int v) {
        tag = sum = v;
    }
    int goLeft(int v) {
        if(v) return indexLeft[v];
        return 0;
    }
    int goRight(int v) {
        if(v) return indexRight[v];
        return 0;
    }

} tree[N << 2];

void pushUp(int rt) {
    tree[rt].sum = tree[rt << 1].sum + tree[rt << 1 | 1].sum;
}

void pushDown(int rt) {
    if(~tree[rt].tag) {
        int v = tree[rt].tag;
        int ls = rt << 1, rs = ls | 1;
        tree[ls].setTag(tree[rt].goLeft(v));
        tree[rs].setTag(tree[rt].goRight(v));
        tree[rt].tag = -1;
    }
}

void merge(int rt, int l, int r) {
    static int tmp[N];
    int m = l + r >> 1;
    int* vl = b + l - 1, *vr = b + m;
    int sl = m - l + 1, sr = r - m;
    int i = 1, j = 1, k = 1;
    while(i <= sl && j <= sr) {
        if(vl[i] < vr[j]) {
            tree[rt].indexLeft[k] = i;
            tree[rt].indexRight[k] = j - 1;
            tmp[k++] = vl[i++];
        } else {
            tree[rt].indexLeft[k] = i - 1;
            tree[rt].indexRight[k] = j;
            tmp[k++] = vr[j++];
        }
    }
    while(i <= sl) {
        tree[rt].indexLeft[k] = i;
        tree[rt].indexRight[k] = j - 1;
        tmp[k++] = vl[i++];
    }
    while(j <= sr) {
        tree[rt].indexLeft[k] = i - 1;
        tree[rt].indexRight[k] = j;
        tmp[k++] = vr[j++];
    }
    memcpy(b + l, tmp + 1, r - l + 1 << 2);
}

void build(int l, int r, int rt) {
    tree[rt].tag = -1;
    tree[rt].indexLeft = Allocator::allocate(r - l + 1);
    tree[rt].indexRight = Allocator::allocate(r - l + 1);
    if(l == r) {
        tree[rt].sum = a[l] >= b[l];
        return;
    }
    int m = l + r >> 1;
    build(l, m, rt << 1);
    build(m + 1, r, rt << 1 | 1);
    pushUp(rt);
    merge(rt, l, r);
}

void update(int L, int R, int v, int l, int r, int rt) {
    if(L <= l && r <= R) {
        tree[rt].setTag(v);
        return;
    }
    int m = l + r >> 1;
    pushDown(rt);
    if(L <= m) update(L, R, tree[rt].goLeft(v), l, m, rt << 1);
    if(R > m) update(L, R, tree[rt].goRight(v), m + 1, r, rt << 1 | 1);
    pushUp(rt);
}

int query(int L, int R, int l, int r, int rt) {
    if(L <= l && r <= R) return tree[rt].sum;
    int m = l + r >> 1, ret = 0;
    pushDown(rt);
    if(L <= m) ret += query(L, R, l, m, rt << 1);
    if(R > m) ret += query(L, R, m + 1, r, rt << 1 | 1);
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d%d%d", &n, &q, &A, &B);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);
        for(int i = 1; i <= n; ++i) scanf("%d", b + i);

        Allocator::init();
        Discretization::init();
        build(1, n, 1);

        int ans = 0, last = 0;
        for(int i = 1; i <= q; ++i) {
            int l = rnd(last, A, B) % n + 1;
            int r = rnd(last, A, B) % n + 1;
            int x = rnd(last, A, B) + 1;
            if(l > r) swap(l, r);
            if(l + r + x & 1) {
                x = Discretization::get(x);
//                printf("+ %d %d %d\n", l, r, x);
                update(l, r, x, 1, n, 1);
            } else {
                last = query(l, r, 1, n, 1);
//                printf("? %d %d ans = %d\n", l, r, last);
                ans += 1LL * i * last % MOD;
                if(ans >= MOD) ans -= MOD;
            }
        }
        printf("%d\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}

/*
+ 2 3 5
? 2 2 ans = 1
? 1 4 ans = 3
? 2 4 ans = 2
? 3 4 ans = 1
+ 1 1 5
+ 1 5 5
? 5 5 ans = 1
? 5 5 ans = 1
? 1 4 ans = 4
81
+ 1 4 5
? 4 5 ans = 1
? 2 4 ans = 3
? 3 4 ans = 2
+ 1 5 5
? 2 5 ans = 4
+ 1 4 5
? 4 4 ans = 1
? 1 3 ans = 3
? 2 2 ans = 1
88
? 1 5 ans = 3
+ 4 4 5
+ 2 4 5
+ 3 4 5
? 3 4 ans = 2
? 1 4 ans = 4
+ 1 1 5
+ 3 5 5
+ 1 3 5
? 1 5 ans = 5
87
*/
```