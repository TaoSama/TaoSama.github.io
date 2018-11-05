---
title: HDU 4606 Occupy Cities （计算几何、最短路、最小路径覆盖）
categories:
  - 图论
  - 二分图
tags:
  - 最小路径覆盖
  - null
date: 2016-03-01 20:39:10
toc:
---
题意：
>给出$n\le 100$个城市需要去占领，有$m\le 100$条线段是障碍物，有$p\le 100$个士兵可以用
占领城市有个先后顺序，每个士兵有个背包，占领城市之后，仅能补给一次背包
问背包容量最少是多少，可以用这$p$个士兵完成任务，起点任意

<!-- more -->
分析：
>枚举城市以及障碍的顶点，需要特判是某个障碍的$2$个端点的情况，求一下距离，判断是否与线段相交
然后$floyd$预处理最短路
二分背包容量，根据占领的先后顺序建边，跑二分图最小路径覆盖判断是否$p$个士兵可以占领

代码：
```cpp
//
//  Created by TaoSama on 2016-03-01
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
const int N = 3e2 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const double EPS = 1e-8;

int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

int n, m, p, schedule[N];
double d[N][N];

struct Point {
    double x, y;
    void read() {scanf("%lf%lf", &x, &y);}
    Point operator-(const Point& p) {
        return {x - p.x, y - p.y};
    }
    double operator*(const Point& p) {
        return x * p.x + y * p.y;
    }
    double operator^(const Point& p) {
        return x * p.y - y * p.x;
    }
    double length() {
        return sqrt(*this **this);
    }
} ps[N];

bool segmentProperIntersection(Point a1, Point a2, Point b1, Point b2) {
    double c1 = (a2 - a1) ^ (b1 - a1), c2 = (a2 - a1) ^ (b2 - a1);
    double c3 = (b2 - b1) ^ (a1 - b1), c4 = (b2 - b1) ^ (a2 - b1);
    return sgn(c1) * sgn(c2) < 0 && sgn(c3) * sgn(c4) < 0;
}

bool vis[N];
int match[N];
vector<int> G[N];

bool dfs(int u) {
    for(int v : G[u]) {
        if(vis[v]) continue;
        vis[v] = true;
        if(!match[v] || dfs(match[v])) {
            match[v] = u;
            return true;
        }
    }
    return false;
}

bool check(double x) {
    for(int i = 1; i <= n; ++i) G[i].clear();
    for(int i = 1; i <= n; ++i) {
        int u = schedule[i];
        for(int j = i + 1; j <= n; ++j) {
            int v = schedule[j];
            if(sgn(x - d[u][v]) >= 0) G[u].push_back(v);
        }
    }

    int matches = 0;
    memset(match, 0, sizeof match);
    for(int i = 1; i <= n; ++i) {
        memset(vis, 0, sizeof vis);
        matches += dfs(i);
    }
    return n - matches <= p;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d%d", &n, &m, &p);
        for(int i = 1; i <= n; ++i) ps[i].read();
        for(int i = 1; i <= m; ++i) {
            ps[n + i].read();
            ps[n + m + i].read();
        }
        for(int i = 1; i <= n; ++i) scanf("%d", schedule + i);

        for(int i = 1; i <= n + 2 * m; ++i)
            for(int j = 1; j <= n + 2 * m; ++j)
                d[i][j] = i == j ? 0 : 1e18;
        for(int i = 1; i <= n + 2 * m; ++i) {
            for(int j = i + 1; j <= n + 2 * m; ++j) {
                if(i > n && j - i == m) continue; //same barrier's two ends
                bool can = true;
                for(int k = 1; k <= m; ++k) {
                    if(segmentProperIntersection(ps[i], ps[j], ps[n + k], ps[n + m + k])) {
                        can = false;
                        break;
                    }
                }
                if(can) d[i][j] = d[j][i] = (ps[i] - ps[j]).length();
            }
        }

        //Floyd
        for(int k = 1; k <= n + 2 * m; ++k)
            for(int i = 1; i <= n + 2 * m; ++i)
                for(int j = 1; j <= n + 2 * m; ++j)
                    d[i][j] = min(d[i][j], d[i][k] + d[k][j]);

        double l = 0, r = 1e5;
        for(int i = 1; i <= 100; ++i) {
            double m = (l + r) / 2;
            if(check(m)) r = m;
            else l = m;
        }
        printf("%.2f\n", l);
    }
    return 0;
}
```
