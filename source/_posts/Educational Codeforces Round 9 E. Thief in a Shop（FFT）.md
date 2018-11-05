---
title: Educational Codeforces Round 9 E. Thief in a Shop（FFT）
categories:
  - 数学
  - FFT/NTT/FWT
tags:
  - FFT
  - 
date: 2016-03-06 00:10:42
toc: 
---
题意:
>$给定N,K\le 10^3,N种物品,价值A_i\le 10^3, 必须装K个物品的背包$
>$求所有能装的价值，从小到大输出$

<!--more-->

分析:
>$其实就是长度为1000的物品价值向量的k次幂,存在该价值就为1否则为0$
>$然后用fft求k次卷积就好了$
>$用bool数组可以降低精度误差, 同时不要直接把fft的len设置成10^6， 可以优化下常数$
>$时间复杂度是O(WlogWlogk), W=10^6$

代码:
```cpp
//
//  Created by TaoSama on 2016-03-06
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:1024000000,1024000000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <complex>
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
const int N = (1 << 21) + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const double PI = acos(-1);

typedef complex<double> Complex;

void rader(Complex *y, int len) {
    for(int i = 1, j = len / 2; i < len - 1; i++) {
        if(i < j) swap(y[i], y[j]);
        int k = len / 2;
        while(j >= k) {j -= k; k /= 2;}
        if(j < k) j += k;
    }
}
void fft(Complex *y, int len, int op) {
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

int n, m, k;
Complex A[N], B[N];
bool p[N], q[N];

void multiply(bool *p, int &n, bool *q, int m) {
    int len = 1;
    while(len <= n + m) len <<= 1;
    for(int i = 0; i < len; ++i) A[i] = Complex(i <= n ? p[i] : 0, 0);
    for(int i = 0; i < len; ++i) B[i] = Complex(i <= m ? q[i] : 0, 0);

    fft(A, len, 1);
    fft(B, len, 1);
    for(int i = 0; i < len; ++i) A[i] *= B[i];
    fft(A, len, -1);
    for(int i = 0; i <= n + m; ++i) p[i] = A[i].real() > 0.5;
    n += m;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d", &n, &k);
    for(int i = 1; i <= n; ++i) {
        int x; scanf("%d", &x);
        q[x] = 1;
    }
    m = 1000;
    n = 0; p[0] = 1;
    while(k) {
        if(k & 1) multiply(p, n, q, m);
        if(k > 1) multiply(q, m, q, m);
        k >>= 1;
    }
    for(int i = 1; i <= n; ++i) if(p[i]) printf("%d ", i); puts("");
    return 0;
}
```