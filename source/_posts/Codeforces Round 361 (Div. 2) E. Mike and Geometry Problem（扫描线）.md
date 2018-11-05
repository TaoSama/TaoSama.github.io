---
title: Codeforces Round 361 (Div. 2) E. Mike and Geometry Problem（扫描线）
categories:
  - 数学
  - 贡献
  - 
tags:
  -  
  - 
date: 2016-06-20 18:42:10
toc: 
---

题意：
>$给定N\le 2\times 10^5个线段，现任意选出K\le N个线段，求任意K个线段交点个数和$

<!-- more -->
分析：
>$考虑每个点的贡献，问题就转换成了扫描线统计覆盖\ge K次的线段$
$对于每个这样的线段，对答案的贡献是C_n^k$
$时间复杂度O(nlogn)$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-20
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
const int N = 2e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, k;
int a[N];
typedef long long LL;
LL fact[N], finv[N];

LL quick(LL x, LL n) {
    LL ret = 1;
    for(; n; n >>= 1) {
        if(n & 1) ret = ret * x % MOD;
        x = x * x % MOD;
    }
    return ret;
}

LL C(int n, int m) {
    return fact[n] * finv[m] % MOD * finv[n - m] % MOD;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    fact[0] = finv[0] = 1;
    for(int i = 1; i < N; ++i) {
        fact[i] = fact[i - 1] * i % MOD;
        finv[i] = quick(fact[i], MOD - 2);
    }

    while(scanf("%d%d", &n, &k) == 2) {
        map<int, int> mp;
        for(int i = 1; i <= n; ++i) {
            int x, y; scanf("%d%d", &x, &y);
            ++mp[x];
            --mp[y + 1];
        }

        int sum = 0, ans = 0;
        int last = mp.begin()->first;
        for(auto& p : mp) {
            if(sum >= k) {
                ans += C(sum, k) * (p.first - last) % MOD;
                if(ans >= MOD) ans -= MOD;
            }
            sum += p.second;
            last = p.first;
        }
        printf("%d\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```