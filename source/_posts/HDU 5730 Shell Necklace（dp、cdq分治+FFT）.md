---
title: HDU 5730 Shell Necklace（dp、cdq分治+FFT）
categories:
  - 数学
  - FFT/NTT/FWT
  - 
tags:
  - cdq分治
  - FFT
  -
date: 2016-07-24 21:32:10
toc: 
---

题意：
>$给定N\le 10^5个贝壳的项链，每连续i\le N个贝壳模式的贡献是a_i$
$对于某种串项链的方式，假设含有模式b_1,\ b_2,\ \cdots,\ b_m，总贡献为\prod_{i=1}^m a_{b_i} $
$求所有串项链方式的贡献和$

<!-- more -->
分析：
>$首先有显然的dp，f[i]:=以i结尾的贝壳的贡献和$
$f[i]=\sum_{j=1}^i f[i-j]\times a_j$
$但是转移是O(n)的，总复杂度是O(n^2)，考虑优化$
$发现转移是个卷积形式，由于模数不是费马素数，所以cdq分治+FFT一波就好了$
$时间复杂度是O(nlog^2n)$


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
#include <complex>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 313;
const double PI = acos(-1);

int n, a[N];
int f[N];

typedef complex<double> Complex;

void rader(Complex* y, int len) {
    for(int i = 1, j = len / 2; i < len - 1; i++) {
        if(i < j) swap(y[i], y[j]);
        int k = len / 2;
        while(j >= k) {j -= k; k /= 2;}
        if(j < k) j += k;
    }
}
void fft(Complex* y, int len, int op) {
    rader(y, len);
    for(int h = 2; h <= len; h <<= 1) {
        double ang = op * 2 * PI / h;
        Complex wn(cos(ang), sin(ang));
        for(int j = 0; j < len; j += h) {
            Complex w(1, 0);
            for(int k = j; k < j + h / 2; k++) {
                Complex u = y[k];
                Complex t = w * y[k + h / 2];
                y[k] = u + t;
                y[k + h / 2] = u - t;
                w = w * wn;
            }
        }
    }
    if(op == -1) for(int i = 0; i < len; i++) y[i] /= len;
}

Complex A[N << 1], B[N << 1];

void cdq(int l, int r) {
    if(l == r) return;
    int mid = (l + r) >> 1;
    cdq(l, mid);

    int len = 1;
    while(len <= r - l + 1) len <<= 1;
    for(int i = 0; i < len; i++) {
        A[i] = Complex(l + i <= mid ? f[l + i] : 0, 0);
        B[i] = Complex(l + i + 1 <= r ? a[i + 1] : 0, 0);
    }
    fft(A, len, 1);
    fft(B, len, 1);
    for(int i = 0; i < len; i++) A[i] *= B[i];
    fft(A, len, -1);

    for(int i = mid + 1; i <= r; i++) {
        f[i] += fmod(A[i - l - 1].real(), MOD) + 0.5;
        if(f[i] >= MOD) f[i] -= MOD;
    }
    cdq(mid + 1, r);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d", &n) == 1 && n) {
        for(int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
            a[i] %= MOD;
        }

        memset(f, 0, sizeof f);
        f[0] = 1;
        cdq(0, n);
        printf("%d\n", f[n]);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```