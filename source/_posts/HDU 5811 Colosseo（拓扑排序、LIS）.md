---
title: HDU 5811 Colosseo（拓扑排序、LIS）
categories:
  - 图论
  - 拓扑排序
  - 
tags:
  - 拓扑排序
  - 
  - 
date: 2016-08-10 18:56:10
toc: 
---

题意： 
>$给定N\le 10^3个人，给定拓扑关系，现将他们分为两个集合T1和T2$
$问各自是否存在合法拓扑序，且在保证拓扑序的情况下，T2最多能添加多少人到T1中$

<!-- more -->
分析：
>$先暴力对T1和T2求个拓扑序，之后倒着把T2往T1里尝试插入，记录下位置$
$最后对这个位置求个LIS就是最多能添加的人数$
$时间复杂度O(n^2)$

```cpp
//
//  Created by TaoSama on 2016-08-10
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
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
bool g[N][N];

bool ok(vector<int>& G, vector<int>& seq) {
    seq = vector<int>(G.size(), 0);
    for(int x : G) {
        int s = 0;
        for(int y : G) s += g[x][y];
        if(seq[s]) return false;
        seq[s] = x;
    }
    reverse(seq.begin(), seq.end());
    return true;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt", "w", stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d\n", &n, &m) == 2 && (n || m)) {
        for(int i = 1; i <= n; ++i) {
            char buf[N << 1]; gets(buf + 1);
            for(int j = 1; buf[j]; j += 2) g[i][j / 2 + 1] = buf[j] - '0';
        }

        vector<int> G[2];
        vector<int> vis(n + 1, 0);
        for(int i = 1; i <= m; ++i) {
            int x; scanf("%d", &x);
            G[0].push_back(x);
            vis[x] = true;
        }
        for(int i = 1; i <= n; ++i) if(!vis[i]) G[1].push_back(i);

        vector<int> seq[2];
        if(ok(G[0], seq[0]) && ok(G[1], seq[1])) {

            vector<int> pos(seq[1].size());
            for(int i = 0; i < seq[1].size(); ++i) {
                int x = seq[1][i];
                int p = seq[0].size();
                for(int j = seq[0].size() - 1; ~j; --j) {
                    int y = seq[0][j];
                    if(g[x][y]) p = j;
                    else break;
                }

                bool ok = true;
                for(int j = 0; j < p && ok; ++j) {
                    int y = seq[0][j];
                    if(!g[y][x]) ok = false;
                }
                pos[i] = ok ? p : -1;
            }

            vector<int> dp(pos.size(), 0);
            for(int i = 0; i < pos.size(); ++i) {
                if(pos[i] == -1) continue;
                dp[i] = 1;
                for(int j = 0; j < i; ++j)
                    if(pos[j] != -1 && pos[i] >= pos[j])
                        dp[i] = max(dp[i], dp[j] + 1);
            }

            printf("YES %d\n", *max_element(dp.begin(), dp.end()));

        } else puts("NO");
    }

    return 0;
}

```