---
title: HDU 5375 Gray code（线性dp）
categories:
  - 动态规划
  - 线性dp
tags:
  - dp
  - 
date: 2016-05-09 16:42:10
toc: 
---
题意：
>$N\le 2\times 10^5长度的二进制数，由0、1、?组成，?代表01都可$
$每位有个权值，w_i\le 1000$
$如果将这个二进制数转化成格雷码，1获得这个权值，0不获得$
$求怎样确定这个二进制数才能获得最大权值，输出这个权值$
<!-- more -->

分析：
>$百度得到公式：G_i=B_i\oplus B_{i+1}，i\in [0, n-1]，B_n=0，这个是从低位到高位的$
$有了这个公式直接dp就可以了$
$f[i][2]:=从高位到低位，前i位二进制位，且第i位为0/1的最大权值$
$转移显然就f[i][b]=max(f[i-1][b],\ f[i-1][b\oplus 1]+w[i])$
$ans=max(f[n][0],\ f[n][1])$
$时间复杂度O(n)$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-09
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
const int N = 2e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
char s[N];
int f[N][2]; //binary from high bit to low bit

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%s", s + 1);
        n = strlen(s + 1);

        memset(f, 0xc0, sizeof f);
        f[0][0] = 0;
        for(int i = 1; i <= n; ++i) {
            int w; scanf("%d", &w);
            if(s[i] == '0') f[i][0] = max(f[i - 1][0], f[i - 1][1] + w);
            else if(s[i] == '1') f[i][1] = max(f[i - 1][1], f[i - 1][0] + w);
            else {
                f[i][0] = max(f[i - 1][0], f[i - 1][1] + w);
                f[i][1] = max(f[i - 1][1], f[i - 1][0] + w);
            }
        }
        int ans = max(f[n][0], f[n][1]);
        static int kase = 0;
        printf("Case #%d: %d\n", ++kase, ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}

```