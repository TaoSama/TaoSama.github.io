---
title: HDU 5638 Toposort（拓扑排序）
categories:
  - 图论
  - 拓扑排序
  - 
tags:
  - 拓扑排序
  - 
date: 2016-04-07 16:55:10
toc: 
---
题意：
>$N\le 10^5个点，M\le 2\times 10^5条边的无向图，现删去K\le M条边$
$要使得最小拓扑序最小，求这个拓扑序$

<!-- more -->

分析：
>$参考下普通的用堆维护求字典序最小拓扑序$ $用某种数据结构维护入度小于等于k的所有点$
$每次找出编号最小的i，强制删掉它的所有入边，表现为k-=inDegree_i$
$可以用线段树维护全局入度，每次线段树上二分找\le k的最小的i$
$其实也可以用堆来机智的做，反正每个只会用一次，复杂度是保证了的$
$时间复杂度为O(nlogn)$

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

int n, m, k;
vector<int> G[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d%d", &n, &m, &k);
        for(int i = 1; i <= n; ++i) G[i].clear();

        vector<int> in(N, 0);
        for(int i = 1; i <= m; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            G[u].push_back(v);
            ++in[v];
        }

        vector<bool> used(N, false);
        priority_queue<int, vector<int>, greater<int> > q;
        for(int i = 1; i <= n; ++i) if(in[i] <= k) q.push(i);

        int ans = 0;
        for(int i = 1; i <= n; ++i) {
            int u;
            while(true) {
                u = q.top(); q.pop();
                if(!used[u] && in[u] <= k) break;
            }
            k -= in[u];
            used[u] = true;

            ans += 1LL * i * u % MOD;
            if(ans >= MOD) ans -= MOD;

            for(int v : G[u])
                if(--in[v] <= k) q.push(v);
        }
        printf("%d\n", ans);
    }
    return 0;
}
```