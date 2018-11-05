---
title: HDU 5745 La Vie en rose（dp、bitset优化）
categories:
  - 动态规划
  - bitset优化
  - 
tags:
  - bitset
  - 
  - 
date: 2016-07-24 23:01:10
toc: 
---

题意：
>$N\le 10^5的母串，M\le 5000的模式串$
$对于模式串，不相邻的2个字符可以和相邻的交换$
$即abcd，ab换和cd也可以换，但bc换了cd就不能换了$
$求每个位置是否能匹配模式串$

<!-- more -->
分析：
>$f[i][j][3]:=母串匹配到i，模式串匹配到j，0和前面换，1正好匹配，2和后面换$
$转移：f[i][j][0]=f[i-1][j-1][2]，s_i=t_{j-1}$
$f[i][j][1]=f[i-1][j-1][0]\ |\ f[i-1][j-1][1]，s_i=t_j$
$f[i][j][2]=f[i-1][j-1][0]\ |\ f[i-1][j-1][1]，s_i=t_{j+1}$
$由于dp维护的都是bool，且只从i-1转移过来$
$所以我们可以预处理母串字符集在母串中的位置到bitset中$
$对于每次转移只要左移一次并且与上匹配的模式串的字符的状态就好$
$即整体转移母串所有字符，左移一次相当于对上了当前状态的位置$
$时间复杂度为O(nm/64)，bitset标准库里压ULL$
$事实上搞模式串的状态也是可以的$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-22
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef bitset<N> Sta;

int n, m;
char s[N], t[N];

Sta f[2][3]; //f[N][2][3] 0->pre 1->cur 2->nxt
Sta g[26];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int T; scanf("%d", &T);
    while(T--) {
        scanf("%d%d", &n, &m);
        scanf("%s%s", s + 1, t + 1);

        for(int i = 0; i < 26; ++i) g[i].reset();
        for(int i = 1; i <= n; ++i) g[s[i] - 'a'][i] = 1;

        int p = 0;
        for(int i = 0; i < 3; ++i) f[p][i].reset();
        f[p][1].set();  //f[x][0][1] = 1
        for(int i = 1; i <= m; ++i) {
            int pre = t[i - 1] - 'a', cur = t[i] - 'a', nxt = t[i + 1] - 'a';
            if(i > 1) f[!p][0] = (f[p][2] << 1) & g[pre];
            else f[!p][0].reset();

            f[!p][1] = (f[p][1] | f[p][0]) << 1 & g[cur];

            if(i < m) f[!p][2] = (f[p][1] | f[p][0]) << 1 & g[nxt];
            else f[!p][2].reset();

            p = !p;
        }

        for(int i = 1; i <= n - m + 1; ++i) {
            bool ans = f[p][0][i + m - 1] | f[p][1][i + m - 1];
            putchar("01"[ans]);
        }
        for(int i = n - m + 2; i <= n; ++i) putchar('0');
        puts("");
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```