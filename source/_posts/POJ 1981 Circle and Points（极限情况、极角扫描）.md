---
title: POJ 1981 Circle and Points（极限情况、极角扫描）
categories:
  - 计算几何
  - 极角扫描
  - 
tags:
  - 极角扫描
  - 
  - 
date: 2016-08-28 23:31:10
toc: 
---

题意： 
>$N\le 300个点，问1个单位圆最多能包住几个点，在边界上也可以$
$保证最多只有2个点在圆上$

<!-- more -->
分析：
>$极限情况辣，肯定是有2个点在圆上的，暴力枚举2个点是n^2$
$然后几何姿势找到圆心，转转向量啥的，之后再看有多个在圆内，O(n^3)$
$蓝儿其实可以不这么想，考虑能包住每个点的圆是啥样的$
$也就是说每个点作为圆心的单位圆，对于任意2个点能共同包住的部分显然是相交的那部分$
$这是一个圆弧，我们可以用极角来表示这些圆弧，进入角度和出去角度$
$事实上对于每个点，和其他点的形成的圆交的圆弧，求一个并，那么最大的并就是答案了$
$至于求并，极角排序以后，+1-1极角扫描合并就可以了$
$时间复杂度O(n^2logn)$

$代码O(n^3)：$
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
const int N = 300 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const double EPS = 1e-8;

int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

int n;
struct Point {
    double x, y;
    Point() {}
    Point(double x, double y): x(x), y(y) {}
    inline void read() {
        scanf("%lf%lf", &x, &y);
    }
    inline Point operator+(const Point& p) const {
        return Point(x + p.x, y + p.y);
    }
    inline Point operator-(const Point& p) const {
        return Point(x - p.x, y - p.y);
    }
    inline Point operator*(const double& k) const {
        return Point(k * x, k * y);
    }
    inline double operator*(const Point& p) const {
        return x * p.x + y * p.y;
    }
    inline double operator^(const Point& p) const {
        return x * p.y - y * p.x;
    }
    inline Point rotate(double rad) {
        return Point(x * cos(rad) - y * sin(rad), x * sin(rad) + y * cos(rad));
    }
    inline double length() {
        return sqrt(x * x + y * y);
    }
    inline void norm() {
        double l = length();
        x /= l, y /= l;
    }
    void see() {
        printf("%.4f %.4f\n", x, y);
    }
} ps[N];

typedef Point Vector;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
//    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1 && n) {
        for(int i = 1; i <= n; ++i) ps[i].read();

        int ans = 1;
        for(int i = 1; i <= n; ++i) {
            for(int j = i + 1; j <= n; ++j) {
                if(i == j) continue;

                Vector AB = ps[j] - ps[i]; //B-A=AB
                double c = AB.length();
                if(sgn(c - 2) > 0) continue;

                double cosTheta = (c * c + 1 - 1) / (2 * c);
                double theta = acos(cosTheta);
                Vector AO = AB.rotate(theta); AO.norm();
                Point O = ps[i] + AO;

                int cnt = 0;
                for(int k = 1; k <= n; ++k)
                    if(sgn((ps[k] - O).length() - 1) <= 0) ++cnt;
                ans = max(ans, cnt);
            }
        }
        printf("%d\n", ans);
    }

    return 0;
}
```

$O(n^2logn)：$
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
const int N = 300 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const double EPS = 1e-8;

int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

int n;
struct Point {
    double x, y;
    Point() {}
    Point(double x, double y): x(x), y(y) {}
    inline void read() {
        scanf("%lf%lf", &x, &y);
    }
    inline Point operator+(const Point& p) const {
        return Point(x + p.x, y + p.y);
    }
    inline Point operator-(const Point& p) const {
        return Point(x - p.x, y - p.y);
    }
    inline Point operator*(const double& k) const {
        return Point(k * x, k * y);
    }
    inline double operator*(const Point& p) const {
        return x * p.x + y * p.y;
    }
    inline double operator^(const Point& p) const {
        return x * p.y - y * p.x;
    }
    inline Point rotate(double rad) {
        return Point(x * cos(rad) - y * sin(rad), x * sin(rad) + y * cos(rad));
    }
    inline double length() {
        return sqrt(x * x + y * y);
    }
    inline double angle() {
        return atan2(y, x);
    }
    inline void norm() {
        double l = length();
        x /= l, y /= l;
    }
    void see() {
        printf("%.4f %.4f\n", x, y);
    }
} ps[N];

typedef Point Vector;
pair<double, int> angles[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
//    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1 && n) {
        for(int i = 1; i <= n; ++i) ps[i].read();

        int ans = 1;
        for(int i = 1; i <= n; ++i) {
            int m = 0;
            for(int j = 1; j <= n; ++j) {
                if(i == j) continue;
                Vector AB = ps[j] - ps[i];
                double c = AB.length();
                if(sgn(c - 2) > 0) continue;
                double angle = AB.angle();
                double delta = acos(c / 2);
                angles[m++] = make_pair(angle - delta, 1);
                angles[m++] = make_pair(angle + delta, -1);
            }
            sort(angles, angles + m);

            int cnt = 1;
            for(int j = 0; j < m; ++j) {
                cnt += angles[j].second;
                ans = max(ans, cnt);
            }
        }
        printf("%d\n", ans);
    }

    return 0;
}

```