---
title: Codeforces 662C. Binary Table（FWT）
categories:
  - 数学
  - FFT/NTT/FWT
  - 
tags:
  - FWT
  - 
  - 
date: 2016-09-21 16:22:10
toc: 
---

题意： 
>$给定N\times M的01矩阵，N\le 20，M\le 10^5，每次可以选择flip一行或者一列$
$求最后最少能有几个1$

<!-- more -->
分析：
>$显然每行每列的决定都是翻还是不翻，所以一个很暴力的想法就是二进制枚举其中1个$
$我们得到了一个O(2^nm)的暴力做法，我们来表示一下这个做法$
$f[msk]=\sum_{i=1}^m min(Ones_{col_i \oplus msk},\ n - Ones_{col_i \oplus msk} ),\ msk \in [0,\ 2^n)$
$事实上我们其实并不关心每个col_i \oplus msk是多少，只关心这些值有哪些，各有多少个$
$换句话说(其实就是往fwt凑)，令cnt_k为col_i=k的i有多少，式子变一下$
$f[msk]=\sum_{k \in [0,\ 2^n) }cnt_k\times min(Ones_{msk\oplus k},\ n-Ones_{msk\oplus k})，然后这很卷积。。$
$然后就做完了，时间复杂度O(n 2^n)$

$代码：$
```cpp
//
//  Created by TaoSama on 2016-09-21
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

void fwtXor(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    fwtXor(a, h);
    fwtXor(a + h, h);
    for(int i = 0; i < h; ++i) {
        LL x1 = a[i];
        LL x2 = a[i + h];
        a[i] = (x1 + x2);
        a[i + h] = (x1 - x2);
    }
}

void ifwtXor(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    for(int i = 0; i < h; ++i) {
        // y1=x1+x2
        // y2=x1-x2
        LL y1 = a[i];
        LL y2 = a[i + h];
        a[i] = (y1 + y2) / 2;
        a[i + h] = (y1 - y2) / 2;
    }
    ifwtXor(a, h);
    ifwtXor(a + h, h);
}

const int C = 1 << 20;
int n, m, a[N];
char buf[N];
LL cnt[C], f[C];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d", &n, &m);
    for(int i = 0; i < n; ++i) {
        scanf("%s", buf);
        for(int j = 0; j < m; ++j)
            a[j] |= (buf[j] - '0') << i;
    }
    for(int i = 0; i < m; ++i) ++cnt[a[i]];
    for(int i = 0; i < 1 << n; ++i) {
        int b = __builtin_popcount(i);
        f[i] = min(b, n - b);
    }
    fwtXor(cnt, 1 << n);
    fwtXor(f, 1 << n);
    for(int i = 0; i < C; ++i) f[i] *= cnt[i];
    ifwtXor(f, 1 << n);

    LL ans = INF;
    for(int i = 0; i < 1 << n; ++i) ans = min(ans, f[i]);
    printf("%I64d\n", ans);

    return 0;
}

```