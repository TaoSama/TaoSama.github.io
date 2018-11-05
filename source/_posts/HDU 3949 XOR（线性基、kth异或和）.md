---
title: HDU 3949 XOR（线性基、kth异或和）
categories:
  - 数学
  - 高斯消元
  - 
tags:
  - 线性基
  - 
  - 
date: 2016-08-06 16:04:10
toc: 
---

题意：
>$给定N\le 10^5个数，1\le A_i\le 10^{18}，Q\le 10^5询问$
$选择一个非空子集可以得到一个异或和，对于所有的不同的异或和$
$每次询问第1\le K\le 10^{18}小的是多少$

<!-- more -->
分析：
>$先求出线性基，并且同时也得到了矩阵的秩r，即r个线性基$
$那么r个线性基，能表示的数的个数有2^r-1个（非空）$
$如果r\neq n，说明异或出0了，那么最小值必然为0，特判这种情况$
$对于其他情况直接将k二进制分解，求出这个数即可$

代码:
```cpp
//
//  Created by TaoSama on 2016-07-27
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

int n, q;
typedef long long LL;
LL a[N];

int xorGauss() {
    int r = 0, c = 63;
    for(; r < n && ~c; ++r, --c) {
        int p = r;
        for(; p < n; ++p) if(a[p] >> c & 1) break;
        if(p == n) {--r; continue;}

        swap(a[p], a[r]);
        for(int i = 0; i < n; ++i) {
            if(i != r) {
                if(a[i] >> c & 1) a[i] ^= a[r];
            }
        }
    }
    return r; //rank
}

LL kth(LL k, int r) {
    if(r != n) --k;  //
    if(k >= 1LL << r) return -1;

    LL ret = 0;
    for(int i = 0; i < 64; ++i)
        if(k >> i & 1) ret ^= a[r - i - 1];
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 0; i < n; ++i) scanf("%I64d", a + i);

        int r = xorGauss();

        static int kase = 0;
        printf("Case #%d:\n", ++kase);

        scanf("%d", &q);
        while(q--) {
            LL k; scanf("%I64d", &k);
            printf("%I64d\n", kth(k, r));
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```