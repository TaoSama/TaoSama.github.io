---
title: HDU 5803 Zhu’s Math Problem（数位dp）
categories:
  - 动态规划
  - 数位dp
  - 
tags:
  - 数位dp
  - 
  - 
date: 2016-08-08 21:00:10
toc: 
---

题意： 
>$给定A，B，C，D\in[1,\ 10^{18} ]$
$求满足a+c>b+d \cup a+d\ge b+c且a\in[0,\ A],\ b\in [0,\ B],\ c\in[0,\ C],\ d\in[0,\ D]$
$的不同的四元组(a,\ b,\ c,\ d)个数$

<!-- more -->
分析：
>$数位dp，每次转移4个数，开1个msk表示每个数被限制的情况$
$加状态表示a+c>b+d和a+d\ge b+c$
$f[i][msk][f1][f2]:=从高到低第i位，状态如上的满足四元组个数$
$分解成二进制来转移，仔细想想可以发现f1，f2\in[-2,\ 2]$
$如果越界一定满足/不满足，所以就可以做了$
$时间复杂度O(60\times 2^4\times 5\times 5\times 2^4)$

```cpp
//
//  Created by TaoSama on 2016-08-08
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
LL a[4];
int f[61][1 << 4][5][5]; //[-2, 2]

inline void add(int& x, int y) {
    if((x += y) >= MOD) x -= MOD;
}

//a+c-b-d>0 a+d-b-c>=0
const int O = 2; //offset
int dfs(int x, int msk, int f1, int f2) {
    if(x == -1) return f1 > 0 && f2 >= 0;
    int& ret = f[x][msk][f1 + O][f2 + O];
    if(~ret) return ret;

    ret = 0;
    int to[4];
    for(int i = 0; i < 4; ++i) to[i] = (msk >> i & 1) ? 1 : a[i] >> x & 1;
    for(int a = 0; a <= to[0]; ++a) {
        for(int b = 0; b <= to[1]; ++b) {
            for(int c = 0; c <= to[2]; ++c) {
                for(int d = 0; d <= to[3]; ++d) {
                    int newF1 = f1 * 2 + a + c - b - d;
                    int newF2 = f2 * 2 + a + d - b - c;
                    if(newF1 < -2 || newF2 < -2) continue;
                    newF1 = min(newF1, 2);
                    newF2 = min(newF2, 2);

                    int newMsk = msk;
                    if(a != to[0]) newMsk |= 1 << 0;
                    if(b != to[1]) newMsk |= 1 << 1;
                    if(c != to[2]) newMsk |= 1 << 2;
                    if(d != to[3]) newMsk |= 1 << 3;

                    add(ret, dfs(x - 1, newMsk, newF1, newF2));
                }
            }
        }
    }
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        for(int i = 0; i < 4; ++i) scanf("%I64d", a + i);
        memset(f, -1, sizeof f);
        printf("%d\n", dfs(60, 0, 0, 0));
    }

    return 0;
}
```