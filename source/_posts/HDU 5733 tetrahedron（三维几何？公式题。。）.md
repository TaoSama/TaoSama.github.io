---
title: HDU 5733 tetrahedron（三维几何？公式题。。）
categories:
  - 计算几何
  - 三维几何
  - 
tags:
  - 三维几何
  - 四面体内心
  - 
date: 2016-07-24 22:04:10
toc: 
---

题意：
>$给定4个点，判断能否构成四面体，能输出内心坐标和内切球半径$

<!-- more -->
分析：
>$混合积判断四面体存在，delta = A\ det\ B\ dot\ C$
$delta不为0四面体就存在，然后内切球半径R=\frac{V}{\sum S_{面}}$
$然后内心坐标：$
![](http://7xru22.com1.z0.glb.clouddn.com/16-7-24/95457175.jpg)


代码:
```cpp
//
//  Created by TaoSama on 2016-07-19
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const double EPS = 1e-8, PI = acos(-1);

int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

struct Point3 {
    double x, y, z;
    bool read() {
        if(scanf("%lf%lf%lf", &x, &y, &z) == 3) return true;
        return false;
    }
    Point3 operator+(const Point3& p) {
        return {x + p.x, y + p.y, z + p.z};
    }
    Point3 operator-(const Point3& p) {
        return {x - p.x, y - p.y, z - p.z};
    }
    double operator*(const Point3& p) {
        return x * p.x + y * p.y + z * p.z;
    }
    Point3 operator^(const Point3& p) {
        return {y* p.z - z * p.y, z* p.x - x * p.z, x* p.y - y * p.x};
    }
    double length() {
        return sqrt(*this * *this);
    }
    void norm() {
        double l = length();
        x /= l; y /= l; z /= l;
    }
    void print() {
        printf("%.4f %.4f %.4f", x, y, z);
    }
} ps[4];

using Vector3 = Point3;

bool isParallel(Vector3 A, Vector3 B) {
    return sgn((A ^ B).length()) <= 0;
}

double point3ToLine(Point3 P, Point3 A, Vector3 v) {
    Vector3 w = P - A;
    return (v ^ w).length() / v.length();
}

double point3ToPlane(Point3 p, Point3 A, Point3 B, Point3 C) {
    Vector3 v = B - A, w = C - A;
    Vector3 o = v ^ w;
    double pToO = point3ToLine(p, A, o);
    double pToA = (p - A).length();
    return sqrt(pToA * pToA - pToO * pToO);
}

bool read() {
    for(int i = 0; i < 4; ++i)
        if(!ps[i].read()) return false;
    return true;
}

bool judge() {
    Vector3 A = ps[1] - ps[0], B = ps[2] - ps[0], C = ps[3] - ps[0];
    Vector3 o = A ^ B;
    if(isParallel(A, B)) return false;
    if(sgn(o * C) == 0) return false;
    return true;
}

double getS(Point3 A, Point3 B, Point3 C) {
    double ab = (A - B).length();
    double bc = (B - C).length();
    double ac = (A - C).length();
    double p = (ab + bc + ac) / 2;
    double S = sqrt(p * (p - ab) * (p - bc) * (p - ac));
    return S;
}

void solve() {
    double S1 = getS(ps[0], ps[1], ps[2]); //3
    double S2 = getS(ps[0], ps[1], ps[3]); //2
    double S3 = getS(ps[0], ps[2], ps[3]); //1
    double S4 = getS(ps[3], ps[1], ps[2]); //0
    double h = calc(ps[3], ps[0], ps[1], ps[2]);

    double V = S1 * h;
    double sum = S1 + S2 + S3 + S4;
    double R = V / (sum);

    double x = (ps[0].x * S4 + ps[1].x * S3 + ps[2].x * S2 + ps[3].x * S1);
    x /= sum;
    double y = (ps[0].y * S4 + ps[1].y * S3 + ps[2].y * S2 + ps[3].y * S1);
    y /= sum;
    double z = (ps[0].z * S4 + ps[1].z * S3 + ps[2].z * S2 + ps[3].z * S1);
    z /= sum;

    printf("%.4f %.4f %.4f %.4f\n", x, y, z, R);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(read()) {
        if(!judge()) {
            puts("O O O O");
            continue;
        }

        solve();
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```