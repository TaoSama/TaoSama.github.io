---
title: Codeforces Round 351 E. Levels and Regions（斜率优化dp）
categories:
  - 动态规划
  - 斜率优化
  - 
tags:
  - 斜率优化
  - 
date: 2016-05-11 04:00:10
toc: 
---

题意：
>$将N\le 2\times 10^5个关卡划分成M\le min(50,\ n)个组，组内关卡连续，t_i\le 10^5$
$定义游戏规则，每次选择第一个未全部完成的组，假设组处于关卡区间[l,\ r]$
$选择到组内第一个未完成的关卡的概率p_i=\frac{t_i}{\sum_{i=l}^i t_i}$
$求怎样划分组使得通过所有组的期望打关卡次数最小，求这个次数，误差小于10^{-4}$

<!-- more -->

分析：
>$重复选择某个东西，选到的概率是p_i，那么期望次数就是{1\over p_i}$
$f[j][i]:=前i个关卡分成j组的最小期望$
$f[j][i]=min\{\ f[j-1][k]+cost(k+1,\ i)\ \}，然后看到就知道是斜率优化了。。$
$然后如何O(1)计算cost(k+1,\ i)呢$
$cost(k+1,\ i)=\sum_{i=k+1}^i {1\over p_i}=\sum_{i=k+1}^i\frac{\sum_{i=k+1}^i t_i}{t_i}$
$=\sum_{i=k+1}^i\frac{\sum_{i=1}^i t_i-\sum_{i=1}^k t_i}{t_i}$
$令sum_i=\sum_{i=1}^i t_i$
$则上式=\sum_{i=k+1}^i\frac{sum_i-sum_k}{t_i}$
$=\sum_{i=k+1}^i(\frac{sum_i}{t_i}-{1\over t_i}\cdot sum_k)$
$=\sum_{i=k+1}^i\frac{sum_i}{t_i}-sum_k\cdot \sum_{i=k+1}^i {1\over t_i}$
$令pre_i=\sum_{i=1}^i \frac{sum_i}{t_i}，rev_i=\sum_{i=1}^i {1\over t_i}$
$则上式=pre_i-pre_k-sum_i\cdot(rev_i-rev_k)$
$然后就是sb题了，随便推推套进去就ok了$
$时间复杂度O(mn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-10
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
const int N = 2e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m, q[N];
double sum[N], rev[N], pre[N];
double f[55][N];

double up(int p, int k, int j) {
    return (f[p][j] - pre[j] + rev[j] * sum[j]) -
           (f[p][k] - pre[k] + rev[k] * sum[k]);
}

double dw(int k, int j) {
    return sum[j] - sum[k];
}

bool check(int p, int k, int j, int i) {
    return up(p, k, j) * dw(j, i) >= up(p, j, i) * dw(k, j);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d%d", &n, &m) == 2) {
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            sum[i] = sum[i - 1] + x;
            rev[i] = rev[i - 1] + 1. / x;
            pre[i] = pre[i - 1] + sum[i] / x;
            f[1][i] = pre[i];
        }

        for(int j = 2; j <= m; ++j) {
            int L = 0, R = 0;
            q[R++] = 0;
            for(int i = 1; i <= n; ++i) {
                while(L + 1 < R && up(j - 1, q[L], q[L + 1]) <=
                        rev[i] * dw(q[L], q[L + 1])) ++L;
                int k = q[L];
                f[j][i] = f[j - 1][k] + pre[i] - pre[k] - sum[k] * (rev[i] - rev[k]);
                while(L + 1 < R && check(j - 1, q[R - 2], q[R - 1], i)) --R;
                q[R++] = i;
            }
        }
        printf("%.12f\n", f[m][n]);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```
