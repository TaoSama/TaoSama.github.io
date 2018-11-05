---
title: ZOJ 3933 Team Formation（KM）
categories:
  - 图论
  - 二分图
  - 
tags:
  - 最大权匹配
  - 
date: 2016-04-10 23:42:10
toc: 
---
题意：
>$N\le 500个人，分为X组和Y组，每个人可能是男或者女$
$X和Y要匹配，现要每个人都有厌恶的list，不和list上的人匹配$
$求最大匹配数，以及满足条件下的女生总和最多的方案，输出任意方案$

<!-- more -->

分析：
>$赤果果的最大权匹配模型，500个点费用流显然是跑不过去的(反正我的不行)$
$对于这种求妹子数的也是很经典的模型了$
$然后能连的边权是1，如果有妹子就边权增加$ `妹子数*D`$，D是个比最大匹配数大的数$
$跑出来妹子数就是最大权/D，打印方案即可$
$连边的时候X部还是Y部要注意，打印方案的时候也要注意$
$时间复杂度O(n^3)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-10
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
const int N = 500 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int g[N][N], slack[N];
int lx[N], ly[N];
int match[N], n;
bool vx[N], vy[N];
bool dfs(int u) {
    vx[u] = 1;
    for(int i = 1; i <= n; i++) {
        if(!vy[i]) {
            int t = lx[u] + ly[i] - g[u][i];
            if(t == 0) {
                vy[i] = 1;
                if(match[i] == -1 || dfs(match[i])) {
                    match[i] = u;
                    return 1;
                }
            } else slack[i] = min(slack[i], t);
        }
    }
    return 0;
}

void KM() {
    memset(match, -1, sizeof(match));
    memset(ly, 0, sizeof(ly));
    for(int i = 1; i <= n; i++) {
        lx[i] = -INF;
        for(int j = 1; j <= n; j++)
            lx[i] = max(lx[i], g[i][j]);
    }
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) slack[j] = INF;
        while(1) {
            memset(vx, 0, sizeof(vx));
            memset(vy, 0, sizeof(vy));
            if(dfs(i)) break;
            int d = INF;
            for(int j = 1; j <= n; j++)
                if(!vy[j]) d = min(d, slack[j]);
            for(int j = 1; j <= n; j++)
                if(vx[j]) lx[j] -= d;
            for(int j = 1; j <= n; j++)
                if(vy[j]) ly[j] += d;
        }
    }
}

const int D = 1e6;
bool lft[N], girl[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);

        char buf[N];
        scanf("%s", buf + 1);
        for(int i = 1; i <= n; ++i)
            lft[i] = buf[i] == '0';
        scanf("%s", buf + 1);
        for(int i = 1; i <= n; ++i)
            girl[i] = buf[i] == '0';

        memset(g, 0, sizeof g);
        for(int i = 1; i <= n; ++i) {
            if(!lft[i]) continue;
            for(int j = 1; j <= n; ++j) {
                if(lft[j]) continue;
                g[i][j] = (girl[i] + girl[j]) * D + 1;
            }
        }

        for(int i = 1; i <= n; ++i) {
            int cnt; scanf("%d", &cnt);
            while(cnt--) {
                int x; scanf("%d", &x);
                if(lft[i] && !lft[x]) g[i][x] = 0;
            }
        }

        KM();

        int matches = 0, ans = 0;
        vector<pair<int, int> > path;
        for(int i = 1; i <= n; ++i) {
            if(match[i] == -1 || !g[match[i]][i]) continue;
            ++matches;
            ans += g[match[i]][i];
            path.push_back(make_pair(match[i], i));
        }
        printf("%d %d\n", matches, ans / D);
        for(int i = 0; i < path.size(); ++i)
            printf("%d %d\n", path[i].first, path[i].second);
    }
    return 0;
}

```