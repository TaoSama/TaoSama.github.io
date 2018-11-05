---
title: HDU 4609 3-idiots（FFT）
categories:
  - 数学
  - FFT/NTT/FWT
tags:
  - FFT
  - 
date: 2016-03-07 20:39:10
toc:
---
题意：
>$n\le 10^5条线段，每条长度A_i \le 10^5，问随机取3个，可以组成三角形的概率$

<!-- more -->
分析：
>$cnt_i:=长度为i的线段有几个，然后卷积一下就是2个线段之和为i的方法数有多少$
$之后枚举最长边，累加答案就好了$
$要减去一些不符合的，首先自己+自己的减去，还要除2，去掉重复的$
$枚举的是最长边，所以累加答案的时候要减掉一些非法的情况$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-07
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
const int N = 4e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

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

Complex A[N];
typedef long long LL;
int n, a[N];
LL cnt[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        memset(cnt, 0, sizeof cnt);
        for(int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
            ++cnt[a[i]];
        }
        sort(a + 1, a + 1 + n);

        int len = 1;
        while(len <= a[n] << 1) len <<= 1;
        for(int i = 0; i < len; ++i) A[i] = Complex(cnt[i], 0);
        fft(A, len, 1);
        for(int i = 0; i < len; ++i) A[i] *= A[i];
        fft(A, len, -1);
        for(int i = 0; i < len; ++i) cnt[i] = A[i].real() + 0.5;

        for(int i = 1; i <= n; ++i) --cnt[a[i] << 1];
        len = a[n] << 1;
        for(int i = 1; i <= len; ++i) cnt[i] >>= 1;
        for(int i = 1; i <= len; ++i) cnt[i] += cnt[i - 1];

        LL sum = 0;
        for(int i = 1; i <= n; ++i) {
            sum += cnt[len] - cnt[a[i]];
            sum -= n - 1; //{self, other} > self
            sum -= 1LL * (i - 1) * (n - i); // {small, large} > self
            sum -= 1LL * (n - i) * (n - i - 1) / 2; //{large, large} > self;
        }
        double ans = 1. * sum / (1. * n * (n - 1) * (n - 2) / 6);
        printf("%.7f\n", ans);
    }
    return 0;
}
```