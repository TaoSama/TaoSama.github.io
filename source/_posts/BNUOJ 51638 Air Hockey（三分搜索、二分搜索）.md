---
title: BNUOJ 51638 Air Hockey（三分搜索、二分搜索）
categories:
  - 暴力
  - 搜索
  - 二/三分搜索
  - 
tags:
  - 三分搜索
  - 
date: 2016-05-09 01:31:10
toc: 
---
题意：
>$平面上给定2个球的初始位置，运动向量，以及半径$
$求2个球是否相撞，若撞，输出碰撞的时间，否则输出最近距离$
<!-- more -->

分析：
>$先三分求两圆圆心的最近距离，如果两圆圆心最近距离不大于两圆半径之和，则可以碰撞$
$再二分求出碰撞时间即可，否则直接输出两圆圆心最近距离减去两圆半径之和$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-23
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

const double EPS = 1e-8;
int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

struct Point {
    double x, y;
    void read() {
        scanf("%lf%lf", &x, &y);
    }
    Point operator+(const Point& p) {
        return {x + p.x, y + p.y};
    }
    Point operator-(const Point& p) {
        return {x - p.x, y - p.y};
    }
    Point operator*(const double k) {
        return {k * x, k * y};
    }
    double length() {
        return hypot(x, y);
    }
} x, vx, y, vy;
int rx, ry;

double get(double m) {
    Point p = x + vx * m, q = y + vy * m;
    return (p - q).length();
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        x.read(); scanf("%d", &rx); vx.read();
        y.read(); scanf("%d", &ry); vy.read();
        double l = 0, r = 1e9;
        for(int i = 1; i <= 100; ++i) {
            double ll = (2 * l + r) / 3;
            double rr = (l + 2 * r) / 3;
            if(get(ll) < get(rr)) r = rr;
            else l = ll;
        }
        double dis = get(l) - rx - ry, t = l;
        if(sgn(dis) <= 0) { //hit
            double l = 0, r = t;
            for(int i = 1; i <= 100; ++i) {
                double m = (l + r) / 2;
                if(get(m) < rx + ry) r = m;
                else l = m;
            }
            printf("%.12f\n", l);
        } else printf("%.12f\n", dis);
    }
    return 0;
}
```
