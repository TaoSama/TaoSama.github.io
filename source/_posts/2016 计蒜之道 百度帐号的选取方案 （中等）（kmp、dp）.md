---
title: 2016 计蒜之道 百度帐号的选取方案 （中等）（kmp、dp）
categories:
  - 动态规划
  - 
  - 
tags:
  - kmp 
  - 
date: 2016-06-12 17:42:10
toc: 
---

题意：
>$字符串的循环次数：由某个子串形成字符串的最多重复次数$
$给定L\le 10^3的字符串，现从中选取2个不相交的子串，使得2个子串循环次数相同$
$问方法数$

<!-- more -->
分析：
>$根据kmp的nxt数组，字符串的最小Cycle = l-nxt[l]$
$字符串的最大循环次数Times=l\ \%\ Cycle\ ?\ 1:l/Cycle$
$直接做l次求nxt就可以得到p[l][r]:=s[l\cdots r]子串的循环次数$
$预处理f[i][p]:=以i开头，且循环次数为p的子串个数$
$然后我们枚举左边的这个子串s[l][r]，累加右边suf[r+1][p[l][r]]就可以了$
$预处理复杂度O(n^2)，dp也是O(n^2)，总复杂度O(n^2)$

代码:
```cpp
//
//  Created by TaoSama on 2016-06-05
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
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

char s[N];
int n, nxt[N], p[N][N];
typedef long long LL;
LL suf[N][N];

void getNxt(int st) {
    nxt[st] = st - 1;
    for(int i = st, j = st - 1; i < n;) {
        if(j == st - 1 || s[i] == s[j]) nxt[++i] = ++j;
        else j = nxt[j];
    }
}

int getTimes(int l, int r) {
    int len = r - l, cycle = len - (nxt[r] - l);
    return len % cycle ? 1 : len / cycle;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%s", s) == 1) {
        n = strlen(s);
        memset(suf, 0, sizeof suf);
        for(int i = 0; i < n; ++i) {
            getNxt(i);
            for(int j = i; j < n; ++j) {
                p[i][j] = getTimes(i, j + 1);
                ++suf[i][p[i][j]];
            }
        }
        for(int i = n - 1; ~i; --i)
            for(int j = 1; j <= n; ++j)
                suf[i][j] += suf[i + 1][j];

        LL ans = 0;
        for(int i = 0; i < n; ++i)
            for(int j = i; j < n; ++j)
                ans += suf[j + 1][p[i][j]];
        printf("%lld\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```