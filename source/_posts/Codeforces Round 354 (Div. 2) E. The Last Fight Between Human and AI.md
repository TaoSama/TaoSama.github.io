---
title: Codeforces Round 354 (Div. 2) E. The Last Fight Between Human and AI
categories:
  - 技巧
  - 哈希
  - 
tags:
  - 
  - 
date: 2016-05-26 17:39:10
toc: 
---

题意：
>$给定一个N\le 10^5次多项式P(x)=a_nx^n+a_{n-1}x^{n-1}+\cdots+a_1x+a_0$
$现2个人轮流随意填P(x)的系数，填过的不能再填$
$现有一个多项式Q(x)=x-k，如果最终多项式P(x)可以整除Q(x)，那么后手赢$
$有一部分系数数已经被2个人填了，?表示还没填$
$现2个人最优操作的情况下，问后手是否能赢$

<!-- more -->
分析：
>$假如所有系数a_i都是确定的，如果P(x)能被x-k整除$
$说明x-k是P(x)的一个因子，显然x=k是P(x)=0的一个根，k\neq 0$
$那么讨论一下k=0的情况：$
$如果a[0]=0，那么Q(x)已经整除P(x)，反之则不能整除$
$如果a[0]不确定，那么第一个填a[0]的人胜$
$再仔细看一下k\neq 0的情况：$
$如果系数全部确定的情况上面已经说了，代入x=k判断P(x)=0成立性$
$对于不完全确定的情况，我们先看一下只有1个不确定的情况：$
$令其他的确定系数的项的和为A，不确定的那项为a_ix^i$
$由P(x)=A+a_ix^i=0得，a_i=-{A\over x^i}，由于a_i可以是实数$
$那么最后一步的操作者有必胜策略$
$这样分2种情况讨论，每种情况里又有2种情况讨论这题就做完了$

---
>$对于如何算出P(k)，实际上只有从高位到低位乘起来$
$如果中间大于N\times max\{A_i\}那么就无解了，然后判是不是0就好了$
$当然对于zz选手的我，直接取很多素数对结果取模进行哈希判断也是可以哒$

代码:
```cpp
//
//  Created by TaoSama on 2016-05-26
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

int n, k;
int a[N];

int calc(int mod) {
    int ret = 0;
    for(int i = n; ~i; --i) ret = (1LL * ret * k + a[i]) % mod;
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d%d", &n, &k) == 2) {
        int unknown = 0;
        for(int i = 0; i <= n; ++i) {
            char buf[10]; scanf("%s", buf);
            a[i] = *buf == '?' ? INF : atoi(buf);
            unknown += a[i] == INF;
        }

        int used = n + 1 - unknown;

        if(k == 0) {
            //a[0] is 0 will win
            puts(a[0] == 0 || a[0] == INF && (used & 1) ? "Yes" : "No");
        } else {
            if(!unknown) {
                bool ok = true;
                for(int i = 2e9; ; ++i) {
                    if(calc(i)) {ok = false; break;}
                    if(1.0 * (clock() - _) / CLOCKS_PER_SEC > 0.95) break;
                }
                puts(ok ? "Yes" : "No");
            } else puts(~(n + 1) & 1 ? "Yes" : "No");
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```