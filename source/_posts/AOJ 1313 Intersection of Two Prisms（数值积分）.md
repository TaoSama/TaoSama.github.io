---
title: AOJ 1313 Intersection of Two Prisms（数值积分）
categories:
  - 计算几何
  - 数值积分
  - 
tags:
  - 数值积分
  - 
  - 
date: 2016-08-28 23:53:10
toc: 
---

题意： 
>$三维平面2个棱柱的交体积$
$一个底面在XOY平面，Z轴无限延伸，一个底面在XOZ平面，Y轴无限延伸$
$2个凸多边形顶点数分别为N\le 100，M\le 100$

<!-- more -->
分析：
>$考虑x=a平面对于2个棱柱的切面，切面在YOZ平面的投影是个矩形$
$之后用这个矩形对x积分就得到了交体积$
$直接用Simpson近似一下就好了，具体的话拿出x的每一段就好了$
$由于体积就3次函数不是很高，并且要求精度才3位，就不需要自适应了$
$时间复杂度O(n^2)$

$代码：$
```cpp
//
//  Created by TaoSama on 2016-08-23
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
const int N = 100 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

#define y1 asdadasdasd

int m, n;
double x1[N], y1[N], x2[N], z2[N];

double width(double a, double* x, double* y, int n) {
    double l = INF, r = -INF;
    for(int i = 1; i <= n; ++i) {
        double x1 = x[i], x2 = x[i + 1], y1 = y[i], y2 = y[i + 1];
        if((a - x1) * (a - x2) <= 0) { //intersect
            double b = y1 + (y2 - y1) * (a - x1) / (x2 - x1);
            l = min(l, b);
            r = max(r, b);
        }
    }
    return max(0.0, r - l);
}

double solve() {
    double ret = 0;

    double minX = *min_element(x1 + 1, x1 + 1 + m);
    double maxX = *max_element(x1 + 1, x1 + 1 + m);
    minX = max(minX, *min_element(x2 + 1, x2 + 1 + n));
    maxX = min(maxX, *max_element(x2 + 1, x2 + 1 + n));

    vector<double> xs;
    for(int i = 1; i <= m; ++i)
        if(x1[i] >= minX && x1[i] <= maxX)
            xs.push_back(x1[i]);
    for(int i = 1; i <= n; ++i)
        if(x2[i] >= minX && x2[i] <= maxX)
            xs.push_back(x2[i]);

    sort(xs.begin(), xs.end());
    xs.resize(unique(xs.begin(), xs.end()) - xs.begin());

    for(int i = 0; i + 1 < xs.size(); ++i) {
        double a = xs[i], b = xs[i + 1], c = (a + b) / 2;
        double fa = width(a, x1, y1, m) * width(a, x2, z2, n);
        double fb = width(b, x1, y1, m) * width(b, x2, z2, n);
        double fc = width(c, x1, y1, m) * width(c, x2, z2, n);
        ret += (b - a) / 6 * (fa + 4 * fc + fb);
    }
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &m, &n) == 2 && (m || n)) {
        for(int i = 1; i <= m; ++i) scanf("%lf%lf", x1 + i, y1 + i);
        x1[m + 1] = x1[1], y1[m + 1] = y1[1];
        for(int i = 1; i <= n; ++i) scanf("%lf%lf", x2 + i, z2 + i);
        x2[n + 1] = x2[1], z2[n + 1] = z2[1];

        printf("%.12f\n", solve());
    }

    return 0;
}
```