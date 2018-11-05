---
title: HDU 5323 Solve this interesting problem（dfs）
categories:
  - 暴力
  - 搜索
  - dfs/bfs
  - 
tags:
  - dfs
  - 
date: 2016-03-28 16:10:10
toc: 
---
题意：
>$给定总区间为[0,\ N]的线段树的一个区间[L,\ R]，0\le L，R\le10^9，\frac{L}{R-L+1} \leq 2015$
$求最小的包含这个[L,\ R]的线段树的N$

<!-- more -->

分析：
>$由于线段树每向上合并一次，区间大小就加倍，显然当L=0时的最小R就是答案$
$设合并次数为x，即\frac{L}{2^x(R-L+1)}=0\le 1，2^x \ge \frac{L}{R-L+1}$
$由\frac{L}{R-L+1} \leq 2015近似知：2^x \le 2015\Rightarrow x\le 11$
$由于要枚举在左子树、右子树的左儿子和右儿子，枚举量是O(4^{11})$
$加上最优性剪枝，显然这个复杂度是可以通过的$
$看图可以发现答案在R'\le 2R内$


代码：
```cpp
//
//  Created by TaoSama on 2016-03-22
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

int L, R;

void dfs(int l, int r, int& ans) {
    if(l < 0 || r >= ans || r > 2 * R) return;
    if(!l) {
        ans = min(ans, r);
        return;
    }
    dfs(2 * l - r - 1, r, ans);
    dfs(2 * l - r - 2, r, ans);
    dfs(l, -l + 2 * r, ans);
    dfs(l, -l + 2 * r + 1, ans);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &L, &R) == 2) {
        int ans = INF;
        dfs(L, R, ans);
        if(ans == INF) ans = -1;
        printf("%d\n", ans);
    }
    return 0;
}

```