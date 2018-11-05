---
title: HDU 5651 xiaoxin juju needs help（组合数学）
categories:
  - 数学
  - 组合数学
  - 
tags:
  - 逆元
  - 
date: 2016-04-07 17:12:10
toc: 
---
题意：
>$N\le 2\times 10^3的字符串，字符集大小为26$
$现在重排它，问有多少个不同的回文串$

<!-- more -->

分析：
>$这个很显然了，num_{字符出现奇数个数}>1肯定GG$
$没出现奇数字符直接搞就好了，出现了就取出1个放在中间，剩下的都是偶数个数了$
$ans=\frac{ {\frac{n}{2}}!} {c_1 !\cdot c_2!\cdots c_m!}，很简单的组合计数啦$
$就是要求个逆元$

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
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

char s[N];
int f[N], inv[N], finv[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    f[0] = inv[0] = finv[0] = 1;
    f[1] = inv[1] = finv[1] = 1;
    for(int i = 2; i <= 1000; ++i) {
        f[i] = 1LL * i * f[i - 1] % MOD;
        inv[i] = 1LL * (MOD - MOD / i) * inv[MOD % i] % MOD;
        finv[i] = 1LL * inv[i] * finv[i - 1] % MOD;
    }

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%s", s);
        int n = strlen(s);

        vector<int> c(26, 0);
        for(int i = 0; i < n; ++i) c[s[i] - 'a']++;

        int odd = 0;
        for(int i = 0; i < 26; ++i) odd += c[i] & 1;
        if(odd > 1) {puts("0"); continue;}

        int ans = f[n / 2];
        for(int i = 0; i < 26; ++i)
            ans = 1LL * ans * finv[c[i] / 2] % MOD;
        printf("%d\n", ans);
    }
    return 0;
}

```