---
title: ZOJ 3929 Deque and Balls（数学、BIT）
categories:
  - 数学
  - 贡献
  - 
tags:
  - BIT
  - 逆序数
date: 2016-04-10 23:08:10
toc: 
---
题意：
>$N\le 10^5的序列，A_i\le N，将这个序列按顺序装入一个deque$
$每次装在deque的首尾概率均等，问所有序列相邻逆序个数的期望$
$相邻逆序个数：=\sum_{i=1}^{N-1} A_i > A_{i+1}$

<!-- more -->

分析：
>$首先对于deque我们可以发现，任意1个中轴左右的数的下标都是递增的$
$假设a_1为中轴，那么a_2,\cdots,a_n只能按顺序放右或者不放右（即放左）$
$那么总的合法序列的个数即为2^{n-1}$
$考虑任意一个逆序对答案的贡献：$
$WLOG，(a_i,\ a_j),\ j > i，显然只能放在放在右边，由前面的性质可知a_i和a_j间是没有数的$
$a_i对a_j的贡献，E_{ij}=2^{i-2}\cdot2^{n-j}，a_1已经放了，左边还有i-2个，右边n-j个$
$我们用BIT来维护a_i对a_j的贡献，显然只统计a_i>a_j的$
$同理，(a_j,\ a_i),\ j>i，显然只能放在左边，贡献同理，E_{ij}=2^{i-2}\cdot2^{n-j}$
$同理，只统计a_j < a_i的$
$注意答案是乘2^i\ MOD\ 10^9+7哦，所以约分后还要乘2$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-10
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

void add(int& x, int y) {
    if((x += y) >= MOD) x -= MOD;
}

struct BIT {
    int n, dir, b[N];
    void init(int _n, int _dir) {
        n = _n; dir = _dir;
        memset(b, 0, sizeof b);
    }
    void update(int i, int v) {
        for(; i && i <= n; i += dir * (i & -i))
            add(b[i], v);
    }
    int query(int i) {
        int ret = 0;
        for(; i && i <= n; i -= dir * (i & -i))
            add(ret, b[i]);
        return ret;
    }
} small, large;

int n, a[N];
int two[N] = {1};

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    for(int i = 1; i < N; ++i) add(two[i], two[i - 1] * 2);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);

        small.init(n, 1);
        large.init(n, -1);

        int ans = 0;
        small.update(a[1], 1); //中轴
        large.update(a[1], 1);
        for(int i = 2; i <= n; ++i) {
            add(ans, 1LL * two[n - i] * large.query(a[i] + 1) % MOD); //放右边
            add(ans, 1LL * two[n - i] * small.query(a[i] - 1) % MOD); //放左边

            small.update(a[i], two[i - 2]); //except 1
            large.update(a[i], two[i - 2]); //except 1
        }
        add(ans, ans);
        printf("%d\n", ans);
    }
    return 0;
}

```