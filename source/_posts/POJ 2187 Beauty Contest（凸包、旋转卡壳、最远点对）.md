---
title: POJ 2187 Beauty Contest（凸包、旋转卡壳、最远点对）
categories:
  - 计算几何
  - 旋转卡壳
  - 
tags:
  - 旋转卡壳
  - 最远点对
  - 
date: 2016-08-28 23:21:10
toc: 
---

题意： 
>$N\le 5\times 10^4个点，求最远点的距离的平方$

<!-- more -->
分析：
>$凸包，然后旋转卡壳辣$
$类似two\ pointers的做法，一图流$
![](http://7xru22.com1.z0.glb.clouddn.com/16-8-28/14749437.jpg)

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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

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
    bool operator<(const Point& p) const {
        return x == p.x && y < p.y || x < p.x;
    }
    int length() {
        return *this * *this;
    }
} ps[N];

//输入不能有重点，函数执行完后输入顺序被破坏
Point ch[N];
int convexHull(Point* p, int n, Point* ch) {
    sort(p, p + n);

    int m = 0;
    for(int i = 0; i < n; ++i) {
        while(m > 1 && ((ch[m - 1] - ch[m - 2]) ^ (p[i] - ch[m - 2])) <= 0) --m;
        ch[m++] = p[i];
    }
    for(int i = n - 2, t = m; ~i; --i) {
        while(m > t && ((ch[m - 1] - ch[m - 2]) ^ (p[i] - ch[m - 2])) <= 0) --m;
        ch[m++] = p[i];
    }
    if(n > 1) --m;
    return m;
}

int rotatingCalipers(Point* ch, int n) {
    if(n == 2) return (ch[1] - ch[0]).length();

    int ret = 0;
    ch[n] = ch[0];
    for(int i = 0, j = 2; i < n; ++i) {
        while(((ch[i + 1] - ch[i]) ^ (ch[j + 1] - ch[i]))
                > ((ch[i + 1] - ch[i]) ^ (ch[j] - ch[i])))
            j = (j + 1) % n;
        ret = max(ret, max((ch[j] - ch[i + 1]).length(), (ch[j] - ch[i]).length()));
    }
    return ret;
}

int n;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        for(int i = 0; i < n; ++i) ps[i].read();
        int chSz = convexHull(ps, n, ch);
        int ans = rotatingCalipers(ch, chSz);
        printf("%d\n", ans);
    }

    return 0;
}
```