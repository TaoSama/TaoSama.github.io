---
title: HDU 4651 Partition（五边形数）
categories:
  - 数学
  - 组合数学
  - 
tags:
  - 五边形数
  - 
date: 2016-03-28 15:52:10
toc: 
---
题意：
>$N\le 10^5，求整数N的划分数是多少，答案对10^9+7取模$

<!-- more -->

分析：
>$这个东西就是五边形数，当然n^{1.5}的dp也可以，太神了不懂$
$所以直接公式搞就好辣，时间复杂度也是O(n^{1.5})$
$f(0)=1，f(n)=\displaystyle\sum_{k=1}(-1)^{k+1}\cdot \left( f(n-{k(3k-1)\over 2}) + f(n-{k(3k+1)\over 2})\right)$


代码：
```cpp
//
//  Created by TaoSama on 2016-03-11
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:1024000000,1024000000")
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

//五边形数: f(n) = sum( (-1)^(k+1) [ P(n-k(3k-1)/2) + P(n-k(3k+1)/2)] )

int n, f[N];
void add(int& x, int y) {
    x += y;
    x %= MOD;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    f[0] = 1;
    for(int i = 1; i <= 1e5; ++i) {
        int sum = 0, delta = 1;
        for(int j = 1; ; ++j, delta = -delta) {
            int x = i - j * (3 * j - 1) / 2;
            int y = i - j * (3 * j + 1) / 2;
            if(x < 0 && y < 0) break;
            if(x >= 0) add(sum, delta * f[x]);
            if(y >= 0) add(sum, delta * f[y]);
        }
        f[i] = (sum % MOD + MOD) % MOD;
    }
    int t; scanf("%d", &t);
    while(t--) {
        int n; scanf("%d", &n);
        printf("%d\n", f[n]);
    }
    return 0;
}

```