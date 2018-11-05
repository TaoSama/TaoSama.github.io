---
title: AOJ 2308 White Bird（计算几何、物理）
categories:
  - 计算几何
  - 
  - 
tags:
  - 物理
  - 极限情况
  - 
date: 2016-08-28 21:39:10
toc: 
---

题意： 
>$愤怒的小鸟，给定白鸟(0,\ 0)，就是可以从抛物线轨迹上任意一点下蛋的鸟$
$给定N\le 50个矩形障碍物，和目标位置$
$问能不能用蛋干掉目标$

<!-- more -->
分析：
>$考虑极限情况就是贴着过去的，但是窝萌可以通过微小的偏移来不撞上$
$所以就暴力枚举所有矩形的左上和右上的抛物线，当然直接到达目标也要枚举$
$之后就是恶心的分类讨论了$
$击打左边界、右边界、下边界、内部(包括了上边界)$
$反正不管三七二十一、多判肯定没问题$
$至于判断方法的话，可以考虑解点在特定位置的坐标，或者根据抛物线形态来判断$
$2点坐标就可以确定形态$
$注意细节、多画图就可以AC了$
$时间复杂度O(n^2)$

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
const int N = 50 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const double EPS = 1e-8, g = 9.8;

int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

int n;
double V, X, Y;
double L[N], B[N], R[N], T[N];

double getY(double vy, double t) {
    return vy * t - 0.5 * g * t * t;
}

bool check(double qx, double qy) {
    if(sgn(qx) == 0) return false;

    //vx^2 + vy^2 = v^2, vx * t = qx,  vy*t -0.5*g*t^2=qy
    //qx^2+qy^2+qy*g*t^2+(0.5*g*t^2)^2-v^2*t=0
    double a = g * g / 4, b = qy * g - V * V, c = qx * qx + qy * qy;

    double delta = b * b - 4 * a * c;
    if(sgn(delta) < 0) return false;
    delta = sqrt(abs(delta));

    for(int d = -1; d <= 1; d += 2) {
        double t = (-b + d * delta) / (2 * a);
        if(sgn(t) < 0) continue;
        t = sqrt(abs(t));

        double vx = qx / t, vy = (qy + 0.5 * g * t * t) / t;

        double yt = getY(vy, X / vx);
        if(sgn(yt - Y) < 0) continue;

        bool ok = true;
        for(int i = 1; i <= n && ok; ++i) {
            if(sgn(L[i] - X) > 0) continue;

            //target is in or below the obstacle
            if(sgn(X - L[i]) > 0 && sgn(X - R[i]) < 0 &&
                    sgn(yt - B[i]) > 0 && sgn(Y - T[i]) < 0) ok = false;

            double yL = getY(vy, L[i] / vx), yR = getY(vy, R[i] / vx);
            //hit left
            if(sgn(yL - B[i]) > 0 && sgn(yL - T[i]) < 0) ok = false;
            //hit right
            if(sgn(yR - B[i]) > 0 && sgn(yR - T[i]) < 0) ok = false;
            //hit down
            if(sgn(yL - B[i]) < 0 && sgn(yR - B[i]) > 0) ok = false;

            //highest point
            double xH = vx * vy / g, yH = getY(vy, vy / g);
            //hit inside, this time highest point is in or above the obstacle
            if(sgn(xH - L[i]) > 0 && sgn(xH - R[i]) < 0 &&
                    sgn(yL - B[i]) < 0 && sgn(yH - B[i]) > 0) ok = false;
        }
        if(ok) return true;
    }
    return false;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%lf%lf%lf", &n, &V, &X, &Y) == 4) {
        for(int i = 1; i <= n; ++i) {
            scanf("%lf%lf%lf%lf", L + i, B + i, R + i, T + i);
            R[i] = min(R[i], X);
        }

        bool ok = check(X, Y);
        for(int i = 1; i <= n && !ok; ++i) {
            ok |= check(L[i], T[i]);
            ok |= check(R[i], T[i]);
        }
        puts(ok ? "Yes" : "No");
    }

    return 0;
}
```