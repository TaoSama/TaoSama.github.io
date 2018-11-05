---
title: CDOJ 1051 Eggs broken（期望dp）
categories:
  - 动态规划
  - 概率/期望dp
  - 
tags:
  - 期望dp
  - 
date: 2016-03-28 13:52:10
toc: 
---
题意：
>$1\le N\le 1000层楼，1\le K\le 15个鸡蛋，选择楼投鸡蛋，已知N层楼必碎$
$假设鸡蛋在[1,\ N]碎均匀分布，问知道在哪层碎的最小期望投掷次数$

<!-- more -->

分析：
>$期望dp，f[n][k]:=n层楼，i个鸡蛋的最小期望次数$
$显然边界是f[1][k]=0，1个的时候只能试蛋，f[n][1]=((1+n-1)(n-1)/2+(n-1))/n$
$我们知道鸡蛋的多的时候，肯定二分嘛，少的时候先二分最后一个试蛋嘛（从下往上挨个来）$
$这样太难写了，反正求最小期望次数，直接暴力枚举在哪一层投，取最小的$
$如果当前投i碎了，那么就变成了子问题1\sim i，k-1个蛋投，即f[i][k-1]$
$没碎，继续在上面投，看成子问题，1\sim n-i，k个蛋，即f[n-i][k]$
$再乘上各自的概率，i/n和(n-i)/n$
$这样复杂度是可以接受的，为O(n^2k)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-28
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
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

double f[N][20];
bool vis[N][20];

double dfs(int n, int k) {
    double& ret = f[n][k];
    if(vis[n][k]) return ret;
    vis[n][k] = 1;
    if(n == 1) return ret = 0;
    if(k == 1) return ret = (1. * n * (n - 1) / 2 + n - 1) / n;
    ret = INF;
    for(int i = 1; i < n; ++i)
        ret = min(ret, dfs(i, k - 1) * i / n + dfs(n - i, k) * (n - i) / n + 1);
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int n, k; scanf("%d%d", &n, &k);
    printf("%.5f\n", dfs(n, k));
    return 0;
}
```