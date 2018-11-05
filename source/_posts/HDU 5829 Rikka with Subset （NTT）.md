---
title: HDU 5829 Rikka with Subset （NTT）
categories:
  - 数学
  - FFT/NTT/FWT
  - 
tags:
  - NTT
  - 
  - 
date: 2016-08-12 18:56:10
toc: 
---

题意： 
>$给定N\le 10^5个数，对于一个给定的1\le K\le N，设数集全集为U$
$\forall S\in U，val(S):=S中前min(K,\ |S|)大数的和$
$val(U)_{k}=\sum_{S\in U} val(S)$
$输出每个val(U)_{k}$

<!-- more -->
分析：
>$首先将数列从大到小排序，考虑每个数a_i作为第k大数的贡献f(k)$
$f(k)=\sum_{i=k}^n C_{i-1}^{k-1} \times 2^{n-i} \times a_i$
$窝萌来化简一下这个式子：$
$f(k)=\sum_{i=k}^n \frac{(i-1)!}{(k-1)! \times (i-k)!} \times 2^{n-i} \times a_i$
$令i'=i-k，f(k)=\sum_{i'=0}^{n-k} \frac{(i'+k-1)!}{(k-1)! \times i'!} \times 2^{n-i'-k} \times a_{i'+k}$
$=\frac{1}{2^{k}(k-1)!} \sum_{i'=0}^{n-k} \frac{(i'+k-1)!}{i'!} \times 2^{n-i'} \times a_{i'+k}$
$=\frac{1}{2^{k}(k-1)!} \sum_{i'=0}^{n-k} \frac{2^{n-i'}}{i'!} \times (i'+k-1)! \times a_{i'+k}$
$令b_{i'}=\frac{2^{n-i'}}{i'!},\ c_{i'}=(i'-1)! \times a_{i'}$
$则f(k)=\frac{1}{2^{k}(k-1)!} \sum_{i'=0}^{n-k} b_{i'} \times c_{i'+k}$
$设f(k)=f'(n-k)=\frac{1}{2^{k}(k-1)!} \sum_{i'=0}^{n-k} g(i') \times h(n-k-i')，这是个卷积形式$
$与上式对比，发现只需要把c_i\ reverse一下就好了$
$此时c_i=c_{n-k+1-i}，多偏移了1，所以最后f(k)=f'(n-k+1)$
$给的是费马素数，然后只需要NTT卷积一下b_i和reversed\ c_i就好了$
$最后的答案只要前缀和一下f(k)$
$时间复杂度O(nlogn)$

```cpp
//
//  Created by TaoSama on 2016-08-12
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
const int G = 3, P = 998244353;

LL quick(LL x, LL n) {
    LL ret = 1;
    for(; n; n >>= 1) {
        if(n & 1) ret = ret * x % P;
        x = x * x % P;
    }
    return ret;
}

LL A[N << 2], B[N << 2];
void rader(LL* y, int len) {
    for(int i = 1, j = len / 2; i < len - 1; i++) {
        if(i < j) swap(y[i], y[j]);
        int k = len / 2;
        while(j >= k) {j -= k; k /= 2;}
        if(j < k) j += k;
    }
}
void ntt(LL* y, int len, int op) {
    rader(y, len);
    for(int h = 2; h <= len; h <<= 1) {
        LL wn = quick(G, (P - 1) / h);
        if(op == -1) wn = quick(wn, P - 2);
        for(int j = 0; j < len; j += h) {
            LL w = 1;
            for(int k = j; k < j + h / 2; k++) {
                LL u = y[k];
                LL t = w * y[k + h / 2] % P;
                y[k] = (u + t) % P;
                y[k + h / 2] = (u - t + P) % P;
                w = w * wn % P;
            }
        }
    }
    if(op == -1) {
        LL inv = quick(len, P - 2);
        for(int i = 0; i < len; i++) y[i] = y[i] * inv % P;
    }
}

int n, a[N], f[N];
LL fact[N], invf[N], two[N], invt[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    fact[0] = two[0] = invf[0] = invt[0] = 1;
    for(int i = 1; i < N; ++i) {
        fact[i] = fact[i - 1] * i % P;
        two[i] = two[i - 1] * 2 % P;
        invf[i] = quick(fact[i], P - 2);
        invt[i] = quick(two[i], P - 2);
    }

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);
        sort(a + 1, a + n + 1, greater<int>());

        int len = 1;
        while(len <= n << 1) len <<= 1;
        for(int i = 0; i < len; ++i) {
            A[i] = i <= n ? two[n - i] * invf[i] % P : 0;
            B[i] = i >= 1 && i <= n ? a[i] * fact[i - 1] % P : 0;
        }
        reverse(B + 1, B + n + 1);

        ntt(A, len, 1);
        ntt(B, len, 1);
        for(int i = 0; i < len; ++i) A[i] = A[i] * B[i] % P;
        ntt(A, len, -1);

        for(int i = 1; i <= n; ++i) f[i] = invt[i] * invf[i - 1] % P * A[n - i + 1] % P;
        for(int i = 1; i <= n; ++i) if((f[i] += f[i - 1]) >= P) f[i] -= P;
        for(int i = 1; i <= n; ++i) printf("%d ", f[i]);
        puts("");
    }

    return 0;
}
```