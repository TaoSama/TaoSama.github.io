---
title: HDU 5727 Necklace（二分图最大匹配）
categories:
  - 图论
  - 连通图
  - 
tags:
  - 二分图最大匹配
  - 
date: 2016-07-24 19:27:10
toc: 
---

题意：
>$给定2\times N个珠子的环，其中N个为yang，N个为yin，N\le 9$
$给定M\le N\times N个限制关系$
$对于每个限制关系a_i,\ b_i，表示yang\ a_i会变暗如果与yin\ b_i相邻$
$求最少的暗淡yang珠子数$

<!-- more -->
分析：
>$由于是环，所以固定第1个珠子为1，然后8！枚举yin珠子的排列$
$之后对于每2个yin珠子的间隔，与yang珠子建图，表示这个yang珠子不会暗淡$
$跑二分图匹配，最大匹配数就是最大的不会暗淡的$
$ans=n-maxMatch$
$实际上匈牙利很快，然后如果都不暗淡提前退出就可以过去了$
$集训队的不带剪枝都过去了，不知道为啥窝自带百倍常数$
$时间复杂度O(8!\times nm)$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-20
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
const int N = 10 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int conflict[N][N];
struct Edge {
    int v, nxt;
} edge[N * N];
int head[N], cnt;

void addEdge(int u, int v) {
    edge[cnt] = {v, head[u]};
    head[u] = cnt++;
}

int match[N], vis[N];
bool dfs(int u) {
    for(int i = head[u]; ~i; i = edge[i].nxt) {
        int v = edge[i].v;
        if(vis[v]) continue;
        vis[v] = true;
        if(match[v] == -1 || dfs(match[v])) {
            match[v] = u;
            return true;
        }
    }
    return false;
}

int hungary() {
    int matches = 0;
    memset(match, -1, sizeof match);
    for(int i = 1; i <= n; ++i) {
        memset(vis, 0, sizeof vis);
        matches += dfs(i);
    }
    return matches;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d%d", &n, &m) == 2) {
        memset(conflict, 0, sizeof conflict);
        for(int i = 1; i <= m; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            conflict[u][v] = 1; //yang yin
        }

        int ans = -INF;

        //yin
        int ord[N]; ord[n + 1] = 1;
        for(int i = 1; i <= n; ++i) ord[i] = i;
        do {
//            for(int i = 1; i <= n + 1; ++i) printf("%d ", ord[i]); puts("");

            cnt = 0; memset(head, -1, sizeof head);
            for(int i = 1; i <= n; ++i) { //gap
                int l = ord[i], r = ord[i + 1];
                for(int j = 1; j <= n; ++j) { //yang
                    if(conflict[j][l] || conflict[j][r]) continue;
//                    printf("%d, %d\n", i, j);
                    addEdge(i, j);
                }
            }
            if(cnt <= ans) continue;
            ans = max(ans, hungary());
            if(ans == n) break;
        } while(next_permutation(ord + 2, ord + n + 1));

        printf("%d\n", n - ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```