---
title: HDU 5014 Number Sequence（xor贪心）
categories:
  - 技巧
  - xor
  - 
tags:
  - 贪心
  - 
date: 2016-05-01 20:06:10
toc: 
---
题意：
>$给定n+1个数，a_i\in [0, n]，并且a_i\neq a_j$
$现要求构造n+1个b_i，构造方式同a_i，并且使得\sum a_i\oplus b_i最大$
$输出这个sum，以及n+1个对应的b_i$
<!-- more -->

分析：
>$手玩一下发现，如果n+1个数是奇数，那么0是多余的，剩余的可以两两配对$
$如果n+1是偶数直接配对即可$
$配对方式通过手玩可以发现，从大的开始，贪心补全所有的0就可以了$
$时间复杂度O(n)$

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

int n, a[N], mp[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        for(int i = 0; i <= n; ++i) scanf("%d", a + i);
        memset(mp, -1, sizeof mp);
        if(~n & 1) mp[0] = 0;

        long long ans = 0;
        for(int i = n; ~i; --i) {
            if(mp[i] == -1) {
                int b = 32 - __builtin_clz(i);
                int all = (1 << b) - 1;
                mp[i] = all ^ i;
                mp[all ^ i] = i;
            }
            ans += i ^ mp[i];
        }
        printf("%I64d\n", ans);
        for(int i = 0; i <= n; ++i)
            printf("%d%c", mp[a[i]], " \n"[i == n]);
    }
    return 0;
}
```