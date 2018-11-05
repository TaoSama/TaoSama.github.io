---
title: HDU 4638 Group（离线思想、BIT）
categories:
  - 思维
  - 离线思想
  - 
tags:
  - 离线
  - BIT
date: 2016-03-26 02:30:10
toc: 
---
题意：
>$N,\ Q\le 10^5，1\sim N的序列，Q次询问$
$现有分组要求：组内的人id必须连续，假设人数为k，则价值为k^2$
$询问区间[L,R]，问区间能获得的最大价值的组数是多少个$

<!-- more -->

分析：
>$由于价值是平方，显然让每组人数达到最多，从大到小来贪心$
$问题就转化成了，区间最少能分几组$
$思考一下对于x这个数，显然只能向x-1，x+1连边，我们发现组数=区间大小-边数$
$经典离线套路题了，询问右端点排序$
$从左往右扫描每个数A_i（相当于固定了右端点）$
$把A_i-1和A_i+1的位置添加到BIT中$
$发现所有以i为右端点的询问[L,i]只要查询[L,i]的和就好了$
$然后这个和就是我们想要的，ans(L,R)=R-L+1-sum(L,i)$
$这里其实把BIT倒过来就可以完成了，向前更新，向后查询$
$时间复杂度为O(nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-25
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

struct BIT {
    int n, b[N];
    void init(int _n) {
        n = _n;
        memset(b, 0, sizeof b);
    }
    void add(int i, int v) {
        for(; i; i -= i & -i) b[i] += v;
    }
    int sum(int i) {
        int ret = 0;
        for(; i <= n; i += i & -i) ret += b[i];
        return ret;
    }
} bit;

int n, q;
int a[N], wh[N], ans[N];
vector<pair<int, int> > qs[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &q);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);
        for(int i = 1; i <= n; ++i) qs[i].clear();
        for(int i = 1; i <= q; ++i) {
            int l, r; scanf("%d%d", &l, &r);
            qs[r].push_back({l, i});
        }

        bit.init(n);
        memset(wh, 0, sizeof wh);
        for(int i = 1; i <= n; ++i) {
            int x = a[i];
            if(wh[x - 1]) bit.add(wh[x - 1], 1);
            if(wh[x + 1]) bit.add(wh[x + 1], 1);
            wh[x] = i;
            for(auto& q : qs[i]) ans[q.second] = i - q.first + 1 - bit.sum(q.first);
        }
        for(int i = 1; i <= q; ++i) printf("%d\n", ans[i]);
    }
    return 0;
}

```