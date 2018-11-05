---
title: HDU 5735 Born Slippy（dp、分块、可持久化）
categories:
  - 动态规划
  - 
  - 
tags:
  - 分块优化
  - 
  - 
date: 2016-07-25 22:54:10
toc: 
---

题意：
>$N\le 2^{16}个节点的一棵树，点权w_i < 2^{16}，现从树上抓出来一条链序列$
$对于起点s\in [1,\ N]，找出1个序列，v_1=s,\ v_2,\ \cdots,\ v_m$
$使得f(s)=w_{v_1}+\sum\limits_{i=2}^{m}w_{v_i} \text{ opt } w_{v_{i-1}}最大，opt可以是AND，OR，XOR$
$求每个f(i)$

<!-- more -->
分析：
>$考虑序列上的情况，以and为例，显然有dp，f[i]=ans[i]-w_s=\max\limits_{j < i} \{ f[j]+w_j\text{ and }w_i\ \}$
$蓝儿转移O(n)，总复杂度O(n^2)是不行的，考虑维护东西降低转移复杂度$
$观察w_i<2^{16}，我们把转移拆一下$
$f[i]=\max\limits_{j < i} \{ f[j]+w_j[后8位]\text{ and }w_i[后8位]+w_j[前8位]\text{ and }w_i[前8位] \text{<<} 8\ \}$
$之后我们维护1个，ds[x][y]:=表示w_j前8位为x，w_i后8位为y时， f[j]+w_j[后8位]\text{ and }w_i[后8位]的最值$
$令w_i=a\text{ << }8\text{ | }b，更新f[i]只要枚举x，f[i]=\max\{\ ds[x][b]+x\text{ and }a\text{ << }8\ \}$
$转移复杂度就变成了O(\sqrt{n})，同理，用f[i]更新ds[x][y]$
$推广到树上，从根往下转移的时候，备份被修改的，之后再拷贝回去（即可持久化一下）$
$总时间复杂度为O(n\sqrt{n})$



代码:
```cpp
//
//  Created by TaoSama on 2016-07-25
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
const int N = (1 << 16) + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const int S = 1 << 8;

typedef unsigned int UINT;
int n, w[N];
vector<int> G[N];
UINT f[N], backup[N][S], ds[S][S]; //w_fa prefix8, w_u suffix8 -> max suffix

char op[10];
UINT opt(UINT a, UINT b) {
    if(*op == 'A') return a & b;
    else if(*op == 'O') return a | b;
    return a ^ b;
}

void getMax(UINT& x, UINT y) {
    if(x == -1 || x < y) x = y;
}

void dfs(int u) {
    UINT a = w[u] >> 8, b = w[u] & 255;

    UINT tmp = 0;
    for(int i = 0; i < S; ++i)
        if(ds[i][b] != -1)
            getMax(tmp, ds[i][b] + (opt(i, a) << 8));

    f[u] = w[u] + tmp;
    memcpy(backup[u], ds[a], S << 2);
    for(int i = 0; i < S; ++i)
        getMax(ds[a][i], tmp + opt(i, b));

    for(int v : G[u]) dfs(v);
    memcpy(ds[a], backup[u], S << 2);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%s", &n, op);
        for(int i = 1; i <= n; ++i) scanf("%d", w + i);
        for(int i = 1; i <= n; ++i) G[i].clear();
        for(int i = 2; i <= n; ++i) {
            int fa; scanf("%d", &fa);
            G[fa].push_back(i);
        }

        memset(ds, -1, sizeof ds);
        dfs(1);

        int ans = 0;
        for(int i = 1; i <= n; ++i) {
            ans += 1LL * i * f[i] % MOD;
            if(ans >= MOD) ans -= MOD;
        }
        printf("%d\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```