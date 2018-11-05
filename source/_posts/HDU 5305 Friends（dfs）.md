---
title: HDU 5305 Friends（dfs）
categories:
  - 暴力
  - 搜索
  - dfs/bfs
  - 
tags:
  - dfs
  - 
date: 2016-03-18 18:50:10
toc:
---
题意：
>$给定N\le 8，M\le N(N-1)/2的图$
$现要给边染色，使得每个点的边一半是白，一半是黑$
$求满足条件的方法数$

<!-- more -->
分析：
>$如果枚举边的颜色的话，边数有28，复杂度是2^{28}会T$
$考虑到一半一半，所以所有点的度数必须是偶数(一个剪枝)，边数是24$
$但是没有根本的改变结果，考虑对每个点的所有边进行染色，由于边确定了$
$下一个节点的一些边的颜色会被确定，会使得枚举的边变少，达不到24，现在就很快了$
$至此问题解决，时间复杂度O(1s内可过)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-17
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:1024000000,1024000000")
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

#include <iostream>
#include <algorithm>
using namespace std;

int n, m, ans;
int g[10][10], deg[10];

vector<vector<int> > get(int u, int w) {
    vector<vector<int> > ret;
    vector<int> vec;
    if(w == 0) ret.push_back(vec);
    if(w == 1) {
        for(int i = 1; i <= n; ++i) {
            if(g[u][i] != 1 || i == u) continue;
            vec.push_back(i);
            ret.push_back(vec);
            vec.pop_back();
        }
    }
    if(w == 2) {
        for(int i = 1; i <= n; ++i) {
            if(g[u][i] != 1 || i == u) continue;
            vec.push_back(i);
            for(int j = i + 1; j <= n; ++j) {
                if(g[u][j] != 1 || j == u) continue;
                vec.push_back(j);
                ret.push_back(vec);
                vec.pop_back();
            }
            vec.pop_back();
        }
    }
    if(w == 3) {
        for(int i = 1; i <= n; ++i) {
            if(g[u][i] != 1 || i == u) continue;
            vec.push_back(i);
            for(int j = i + 1; j <= n; ++j) {
                if(g[u][j] != 1 || j == u) continue;
                vec.push_back(j);
                for(int k = j + 1; k <= n; ++k) {
                    if(g[u][k] != 1 || k == u) continue;
                    vec.push_back(k);
                    ret.push_back(vec);
                    vec.pop_back();
                }
                vec.pop_back();
            }
            vec.pop_back();
        }
    }
    return ret;
}

void dfs(int u) {
    if(u == n + 1) {
        ++ans;
        return;
    }
    int w = 0, b = 0;
    for(int v = 1; v <= n; ++v) {
        if(g[u][v] == 2) ++w;
        if(g[u][v] == 3) ++b;
    }
    if(w > deg[u] / 2 || b > deg[u] / 2) return;

    vector<vector<int> > white = get(u, deg[u] / 2 - w);
    for(auto& vec : white) {
        int tmp[10][10];
        memcpy(tmp, g, sizeof g);
        for(int wh : vec) g[u][wh] = g[wh][u] = 2;
        for(int v = 1; v <= n; ++v) if(g[u][v] == 1) g[u][v] = g[v][u] = 3;
        dfs(u + 1);
        memcpy(g, tmp, sizeof tmp);
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        if(!m) {puts("1"); continue;}
        memset(g, 0, sizeof g);
        memset(deg, 0, sizeof deg);
        for(int i = 1; i <= m; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            g[u][v] = g[v][u] = 1;
            ++deg[u]; ++deg[v];
        }
        bool ok = true;
        for(int i = 1; i <= n; ++i)
            if(deg[i] & 1) ok = false;
        if(!ok) {puts("0"); continue;}

        ans = 0;
        dfs(1);
        printf("%d\n", ans);
    }
    return 0;
}

```