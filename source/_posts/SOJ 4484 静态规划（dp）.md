---
title: SOJ 4484 静态规划（dp）
categories:
  - 动态规划
  - 
  - 
tags:
  - dp
  - 
date: 2016-04-11 01:44:10
toc: 
---
题意：
>$N\le 10^5个沙堆，|A_i|\le10^9，可以任意加减每个沙堆的高度$
$现要使得修改后的沙堆高度A_i'\le A_{i+1}'，i\in [1,\ N)$
$求最小修改的高度和$

<!-- more -->

分析：
>$如果后一个元素比前一个小，那么最少改变的情况就是让他和前一个
元素相等$
$如果比前一个元素大或者相等，则不需做出改变$
$仔细想想就可以发现其实最后的序列就是由原始数组的元素组成的$
$那么我们先对原始数组排个序$
$f[i][j]=1\sim i，且第i个元素更改为排序后的第j个元素的最小修改高度和$
$然后暴力转移是O(n^3)，前缀和优化一下就O(n^2)了，再滚动数组一波$

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
const int N = 3e3 + 10, INF = 0x3f3f3f3f, MOD = 19970303;

int n, a[N], s[N];
long long f[2][N];
void add(int& x, int y) {
    if((x += y) >= MOD) x -= MOD;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        for(int i = 1; i <= n; ++i) scanf("%d", a + i), s[i] = a[i];
        sort(s + 1, s + 1 + n);

        int p = 0;
        memset(f[p], 0, sizeof f[p]);
        for(int i = 1; i <= n; ++i) {
            memset(f[!p], 0, sizeof f[!p]);
            for(int j = 1; j <= n; ++j)
                f[!p][j] = f[p][j] + abs(a[i] - s[j]);
            for(int j = 2; j <= n; ++j)
                f[!p][j] = min(f[!p][j], f[!p][j - 1]);
            p = !p;
        }
        printf("%lld\n", f[p][n] % MOD);
    }
    return 0;
}

```