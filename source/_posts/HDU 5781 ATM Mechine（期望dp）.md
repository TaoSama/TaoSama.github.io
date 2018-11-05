---
title: HDU 5781 ATM Mechine（期望dp）
categories:
  - 动态规划
  - 概率/期望dp
  - 
tags:
  - 期望dp
  - 
  - 
date: 2016-08-03 23:30:10
toc: 
---

题意：
>$给定1\le K\le 2000的钱的上界，即钱x\in[0,\ K]，1\le W\le 2000次警告次数$
$>会被警告，\le 可以直接取走钱，警告次数超过W会被警察带走$
$人采取最优策略的情况下，问取完所有钱的期望次数$

<!-- more -->
分析：
>$期望dp，f[k][w]:=上界为k，警告次数为w的取完钱的期望次数$
$因为要考虑x\in [0,\ k]的所有情况：$
$显然有k=1时，f[1][w]=1$
$0要取一次，warn一下就知道没钱，1取1，因为是上界知道取完了，(1+1)/2=1$
$w=1时，只能每次取1，\forall x\in [0,\ k)都是x+1次，k因为是上界不用多一次知道没钱，所以是k$
$f[k][1] = \frac{ { (1+k)\times k\over 2 }+k } { k+1 }$
$其他情况暴力枚举所有能取的钱即i\in[1,\ k]$
$显然被警告的概率是p(i>x)={ i\over k+1 }，即x\in[0,\ i)$
$不被警告的概率p(i\le x)={ k-i+1\over k+1 }，即x\in[i,\ k]$
$转移即为f[k][w] =\displaystyle \min_{ i\in[1,\ k] } \{ { i\over k+1 }\times f[i-1][w-1]+{ k-i+1\over k+1 } \times f[k-i][w]+1 \}$
$当前这个是O(n^3)的，过不了$
$人最优策略的话，显然他可以算出来这些期望，然后采取最优策略$
$我们来估测一下，二分仅仅是不太好的策略，但显然可以知道W的上界是O(log_2n)的$
$然后复杂度就变成了O(n^2logn)了，就可以过了$

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
const int N = 2e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int k, w;
double f[N][20];
bool vis[N][20];

double dfs(int k, int w) {
    double& ret = f[k][w];
    if(vis[k][w]) return ret;
    if(k == 0) ret = 0;
    else if(w == 1) ret = (1.0 * (1 + k) * k / 2 + k) / (k + 1); //1+..+k + k
    else {
        ret = INF;
        for(int i = 1; i <= k; ++i) {
            ret = min(ret, 1. * (k - i + 1) / (k + 1) * dfs(k - i, w)
                      + 1. * i / (k + 1) * dfs(i - 1, w - 1) + 1);
        }
    }
    vis[k][w] = true;
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    memset(vis, 0, sizeof vis);
    for(int i = 0; i <= 2000; ++i)
        for(int j = 1; j <= 15; ++j)
            dfs(i, j);
    while(scanf("%d%d", &k, &w) == 2) {
        w = min(w, 15);
        printf("%.6f\n", f[k][w]);
    }
    return 0;
}
```