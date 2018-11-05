---
title: Codeforces 451E. Devu and Flowers（容斥）
categories:
  - 数学
  - 容斥
  - 
tags:
  - 容斥
  - 
  - 
date: 2016-09-21 20:09:10
toc: 
---

题意： 
>$求x_1+x_2+...+x_n\le s,\ x_1\le f1,\ x_2\le f_2,...,x_n\le f_n的方法数，答案模10^9 + 7$
$n\le 20,\ f_i\le 10^{12},\ s\le 10^{14}$

<!-- more -->
分析：
>$容斥原理套路题，令事件A_i:=至少x_i>f_i的解的方法数$
$显然ans=$![](http://7xru22.com1.z0.glb.clouddn.com/16-9-21/99036938.jpg)
$所以容斥一波就好了，计算每个的时候，先把超过的部分减掉，剩下的就转化成了$
$left个物品装进n个盒子，盒子可以为空的问题，这个用隔板法就可以了$
$大组合数取模用lucas，组合数由于1个很小，直接暴力就可以了$

$代码：$
```cpp
//
//  Created by TaoSama on 2016-09-17
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

typedef long long LL;

LL quick(LL x, LL n) {
    LL ret = 1;
    for(; n; n >>= 1) {
        if(n & 1) ret = ret * x % MOD;
        x = x * x % MOD;
    }
    return ret;
}

LL C(LL n, LL m) {
    if(n < m) return 0;
    m = min(m, n - m);

    LL up = 1, dw = 1;
    for(int i = 0; i < m; ++i) {
        up = up * (n - i) % MOD;
        dw = dw * (i + 1) % MOD;
    }
    return up * quick(dw, MOD - 2) % MOD;
}

LL lucas(LL n, LL m) {
    if(m == 0) return 1;
    return C(n % MOD, m % MOD) * lucas(n / MOD, m / MOD) % MOD;
}

LL calc(LL n, LL m) {
    return lucas(n + m - 1, m - 1);
}

int n;
LL s, f[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(cin >> n >> s) {
        for(int i = 0; i < n; ++i) cin >> f[i];

        LL ans = 0;
        for(int i = 0; i < 1 << n; ++i) {
            LL lft = s, cnt = 0;
            for(int j = 0; j < n; ++j) {
                if(i >> j & 1) {
                    lft -= f[j] + 1;
                    ++cnt;
                }
            }
            if(lft < 0) continue;

            if(cnt & 1) ans -= calc(lft, n);
            else ans += calc(lft, n);
            ans %= MOD;
        }
        ans = (ans + MOD) % MOD;
        cout << ans << endl;
    }

    return 0;
}
```