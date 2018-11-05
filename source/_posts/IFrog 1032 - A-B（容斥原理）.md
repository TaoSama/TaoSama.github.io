---
title: IFrog 1032 - A-B（容斥）
categories:
  - 数学
  - 容斥
  - 
tags:
  - 容斥
  - 
  - 
date: 2016-09-22 20:45:10
toc: 
---

题意： 
>$n\le 500个球，需要把他们放到m\le 500个盒子里，盒子不同，可以为空$
$要求拥有最多球的盒子唯一，问方案数，答案模998244353$

<!-- more -->
分析：
>$容斥原理套路题，枚举最多球的个数x，令事件A_i:=i号盒子\ge x的解的方法数$
$显然E_x=$![](http://7xru22.com1.z0.glb.clouddn.com/16-9-21/99036938.jpg)
$所以容斥一波就好了，注意这里是有序的，所以容斥的时候要乘C(m-1,\ i)$
$因为选出1个盒子放最大的，最后插到m-1个盒子里有m种可能$
$对于n个球放进m个不同的盒子可以为空用隔板法来求，即calc(n,\ m)=C(n+m-1,\ m-1)$
$ans=m\sum_{x} E_x =m\sum_{x}\sum_{i=0}^{m-1} (-1)^i \cdot C(m-1,\ i) \cdot calc(m-1-x-i\times x,\ m-1)$
$阶乘预处理一下组合数，时间复杂度O(nm)$

$代码：$
```cpp
//
//  Created by TaoSama on 2016-09-22
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
const int N = 500 + 10, INF = 0x3f3f3f3f, MOD = 998244353;

int n, m;

typedef long long LL;

LL quick(LL x, LL n) {
    LL ret = 1;
    for(; n; n >>= 1) {
        if(n & 1) ret = ret * x % MOD;
        x = x * x % MOD;
    }
    return ret;
}

LL fact[N], invf[N];
LL C(int n, int m) {
    if(n < m) return 0;
    return fact[n] * invf[m] % MOD * invf[n - m] % MOD;
}

LL calc(int n, int m) {
    return C(n + m - 1, m - 1);
}

LL solve(int lft, int x, int m) {
    LL ret = 0;
    for(int i = 0; i <= m; ++i) {
        if(lft - i * x < 0) continue;
        if(i & 1) ret -= C(m, i) * calc(lft - i * x, m) % MOD;
        else ret += C(m, i) * calc(lft - i * x, m) % MOD;
        ret %= MOD;
    }
    return (ret + MOD) % MOD;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    fact[0] = invf[0] = 1;
    for(int i = 1; i < N; ++i) {
        fact[i] = fact[i - 1] * i % MOD;
        invf[i] = quick(fact[i], MOD - 2);
    }

    while(cin >> n >> m) {
        if(m == 1) {cout << "1\n"; continue;}

        LL ans = 0;
        for(int i = n / m + 1; i <= n; ++i) {
            ans += solve(n - i, i, m - 1);
        }
        ans = ans * m % MOD;
        cout << ans << endl;
    }

    return 0;
}
```