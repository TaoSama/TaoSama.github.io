---
title: HDU 5661 Claris and XOR（xor贪心）
categories:
  - 技巧
  - xor
  - 
tags:
  - 贪心
  - 
date: 2016-05-01 20:11:10
toc: 
---
题意：
>$给定a,b,c,d,1\leq a,b,c,d\leq10^{18}$
$现要求找到x\oplus y，x\in [a,\ b]，y\in[c,\ d]的最大值$
<!-- more -->

分析：
>$显然从高位到低位开始贪心，要最大肯定优先要求不同$
$也就是先测试01和10这两种情况，再测试00和11这两种情况，同时判断是不是符合区间范围$
$假设i这位已经放置好了，显然最小值后面全是0，最大值即全是1$
$考虑合法比较麻烦，反向思考非法情况，最小值比右边界大，或最大值比左边界小$
$时间复杂度O(b)，b=log_2 10^{18}$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-25
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long LL;
LL a, b, c, d;

bool ok(LL x, LL y, int i) {
    LL lft = (1LL << i) - 1;
    LL xr = x + lft, yr = y + lft;
    if(x > b || xr < a) return false;
    if(y > d || yr < c) return false;
    return true;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%I64d%I64d%I64d%I64d", &a, &b, &c, &d);
        LL x = 0, y = 0;
        for(int i = 62; ~i; --i) {
            LL delta = 1LL << i;
            if(ok(x + delta, y, i)) x += delta;
            else if(ok(x, y + delta, i)) y += delta;
            else if(ok(x + delta, y + delta, i)) {
                x += delta;
                y += delta;
            }
        }
        printf("%I64d\n", x ^ y);
    }
    return 0;
}
```