---
title: HDU 5653 Bomber Man wants to bomb an Array（dp）
categories:
  - 动态规划
  - 
  - 
tags:
  - dp
  - 
date: 2016-04-07 20:12:10
toc: 
---
题意：
>$N\le 2\times 10^3，N个格子，M\le N个炸弹$
$每个炸弹可以向左向右炸任意距离，假设为L，R，那么贡献E_i=L+R+1$
$每个格子只能炸1次，总贡献为\Pi_{i=1}^mE_i$
$求最大的总贡献$

<!-- more -->

分析：
>$dp，f[i]:=前i个格子被炸掉的最大贡献$
$转移就枚举炸弹，枚举左右炸的距离，然后这个看起来的三方的dp是二方的$
$f[0]=1，f[r] = max(f[r], f[l-1]*(r-l+1))，log2一下就变成+了$
$只看左右端点，都只是枚举了n$
$时间复杂度O(n^2)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-07
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
const int N = 2e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int a[N];
double v[N], f[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    for(int i = 1; i <= 2000; ++i) v[i] = log2(i);
    while(t--) {
        scanf("%d%d", &n, &m);
        a[0] = 0; a[m + 1] = n + 1;
        for(int i = 1; i <= m; ++i) scanf("%d", a + i), ++a[i];
        sort(a + 1, a + 1 + m);

        memset(f, 0, sizeof f);
        f[0] = 0;
        for(int i = 1; i <= m; ++i)
            for(int l = a[i - 1] + 1; l <= a[i]; ++l)
                for(int r = a[i]; r <= a[i + 1] - 1; ++r)
                    f[r] = max(f[r], f[l - 1] + v[r - l + 1]);

        long long ans = 1e6 * f[n];
        printf("%I64d\n", ans);
    }
    return 0;
}
```