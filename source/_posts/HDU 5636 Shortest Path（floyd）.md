---
title: HDU 5636 Shortest Path（floyd）
categories:
  - 图论
  - 最短路
  - 
tags:
  - floyd
  - 
date: 2016-04-07 16:27:10
toc: 
---
题意：
>$N，M\le 10^5，N个点M条边的形成一条链的无向图$
$即只有(i,i+1,1)这样的边，i\in[1,N)$
$现在添加3条长度为1的边，Q次询问dis(a,b)$

<!-- more -->

分析：
>$把这6个点floyd一下，然后暴力枚举经过这6个点中的2个点，或者不经过$
$时间复杂度为O(6^2\cdot m)$
$当然建图跑spfa也可以，求dis[6][N]，枚举经过这6个点中的1个点，或者不经过$
$时间复杂度为O(6m)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-06
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

int n, m, a[4], b[4];
int g[7][7];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        vector<int> v;
        for(int i = 1; i <= 3; ++i) {
            scanf("%d%d", a + i, b + i);
            v.push_back(a[i]);
            v.push_back(b[i]);
        }
        sort(v.begin(), v.end());
        v.resize(unique(v.begin(), v.end()) - v.begin());

        int sz = v.size();
        for(int i = 1; i <= sz; ++i)
            for(int j = 1; j <= sz; ++j)
                g[i][j] = abs(v[i - 1] - v[j - 1]);
        for(int i = 1; i <= 3; ++i) {
            int x = lower_bound(v.begin(), v.end(), a[i]) - v.begin() + 1;
            int y = lower_bound(v.begin(), v.end(), b[i]) - v.begin() + 1;
            g[x][y] = min(g[x][y], 1);
            g[y][x] = min(g[y][x], 1);
        }
        for(int k = 1; k <= sz; ++k)
            for(int i = 1; i <= sz; ++i)
                for(int j = 1; j <= sz; ++j)
                    g[i][j] = min(g[i][j], g[i][k] + g[k][j]);

        int ans = 0;
        for(int i = 1; i <= m; ++i) {
            int x, y; scanf("%d%d", &x, &y);

            int cur = abs(x - y);
            for(int j = 1; j <= sz; ++j)
                for(int k = 1; k <= sz; ++k)
                    cur = min(cur, abs(x - v[j - 1]) + g[j][k] + abs(y - v[k - 1]));

            ans += 1LL * i * cur % MOD;
            if(ans >= MOD) ans -= MOD;
        }
        printf("%d\n", ans);
    }
    return 0;
}

```