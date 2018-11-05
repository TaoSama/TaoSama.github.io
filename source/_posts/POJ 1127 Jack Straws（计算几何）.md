---
title: POJ 1127 Jack Straws（计算几何）
categories:
  - 计算几何
  - 
  - 
tags:
  - 
  - 
  - 
date: 2016-08-28 21:36:10
toc: 
---

题意： 
>$N<13条线段，不规范相交认为是相连，求2条线段是不是直接或者间接相连$

<!-- more -->
分析：
>$不规范相交直接在规范相交的基础上再判断是不是端点在另一条线段上即可$
$间接相连求个传递闭包就好了$
$时间复杂度O(n^3)$

```cpp
//
//  Created by TaoSama on 2016-08-22
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
const int N = 12 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

struct Point {
    int x, y;
    Point() {}
    Point(int x, int y): x(x), y(y) {}
    void read() {
        scanf("%d%d", &x, &y);
    }
    Point operator-(const Point& p) const {
        return Point(x - p.x, y - p.y);
    }
    int operator*(const Point& p) const {
        return x * p.x + y * p.y;
    }
    int operator^(const Point& p) const {
        return x * p.y - y * p.x;
    }
} ps[N][2];

bool segmentProperIntersection(Point a1, Point a2, Point b1, Point b2) {
    int c1 = (a2 - a1) ^ (b1 - a1), c2 = (a2 - a1) ^ (b2 - a1);
    int c3 = (b2 - b1) ^ (a1 - b1), c4 = (b2 - b1) ^ (a2 - b1);
    return c1 * c2 < 0 && c3 * c4 < 0;
}

bool onSegment(Point p, Point a1, Point a2) {
    return ((a1 - p) ^ (a2 - p)) == 0 && (a1 - p) * (a2 - p) <= 0;
}

int n;
bool f[N][N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1 && n) {
        for(int i = 1; i <= n; ++i)
            for(int j = 0; j < 2; ++j)
                ps[i][j].read();

        for(int i = 1; i <= n; ++i) {
            f[i][i] = true;
            for(int j = i + 1; j <= n; ++j) {
                int ok = segmentProperIntersection(ps[i][0], ps[i][1], ps[j][0], ps[j][1]);
                ok |= onSegment(ps[i][0], ps[j][0], ps[j][1])
                      || onSegment(ps[i][1], ps[j][0], ps[j][1])
                      || onSegment(ps[j][0], ps[i][0], ps[i][1])
                      || onSegment(ps[j][1], ps[i][0], ps[i][1]);
                f[i][j] = f[j][i] = ok;
            }
        }

        for(int k = 1; k <= n; ++k)
            for(int i = 1; i <= n; ++i)
                for(int j = 1; j <= n; ++j)
                    f[i][j] |= f[i][k] && f[k][j];

        int a, b;
        while(scanf("%d%d", &a, &b) == 2 && (a || b)) {
            puts(f[a][b] ? "CONNECTED" : "NOT CONNECTED");
        }
    }

    return 0;
}

```