---
title: 51nod 1532 带可选字符的多字符串匹配（dp、bitset优化）
categories:
  - 动态规划
  - bitset优化
  - 
tags:
  - bitset
  - 
  - 
date: 2016-07-24 23:14:10
toc: 
---

题意：
>$N\le 2\times 10^6的母串，M\le 500的模式串$
$模式串的每个字符c_i有cnt_i\le 62个可选字符$
$求母串哪些位置可以匹配模式串$

<!-- more -->
分析：
>$f[i][j]:=母串匹配到i，模式串匹配到j，能否匹配$
$把所有可选字符压起来成LL，复杂度是O(nm)的，并不行$
$f[i][j]=f[i-1][j-1]，t[j]>>s[i]\ and\ 1为真$
$由于dp状态是bool，并且状态只与i-1有关，考虑bitset压位$
$预处理出模式串字符集在模式串的位置到bitset中$
$每次转移只要左移一次然后与母串字符的状态即可$
$这样就能一次直接转移整个模式串的状态$
$时间复杂度为O(nm/64)$
$表示百毒之星的那个数据其实是有毒的，贴个两边都能过的$
$(参考了百毒之星ranklist的那个人的如何在坑爹数据下的过题办法。。$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-23
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
#include <bitset>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 2e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const int M = 500 + 10;

int n;
char s[N], t[M];
typedef bitset<M> Sta;
Sta f[2], g[256]; //f[M][N]

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(gets(s + 1)) {
        scanf(" %d", &n);
        for(int i = 0; i < 256; ++i) g[i].reset();
        for(int i = 1; i <= n; ++i) {
            int cnt; scanf("%d%s", &cnt, t + 1);
            for(int j = 1; j <= cnt; ++j) g[t[j]][i] = 1;
        }

        bool ok = false;

        int p = 0;
        f[p].reset();
        f[p][0] = 1;
        for(int i = 1; s[i]; ++i) {
            f[!p] = (f[p] << 1) & g[s[i]];
            f[!p][0] = 1;
            if(f[!p][n]) {
                ok = true;
                printf("%d\n", i - n + 1);
            }
            p = !p;
        }
        if(!ok) puts("NULL");
        scanf("%*c");
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```