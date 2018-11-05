---
title: Codeforces Round 358 (Div. 2) E. Alyona and Triangles
categories:
  - 计算几何
  - 旋转卡壳
  - 
tags:
  - 
  - 
date: 2017-04-12 02:34:10
toc: false
---

题意：
$N\le 5000个点，保证任意形成的三角形的面积\le S\le 10^{18}$
$现在构成出一个三角形面积不超过4S，使得包含这个N个点$

<!-- more -->

分析：
$求个凸包，然后n^2枚举2个点，two\ pointers旋转卡壳搞出第三个点$
$求出最大三角形，之后把每条边作为对角线搞出大三角形就好了$
$即原来三角形的每个点是新三角形每条边的中点$
$证明方式就反证一下，如果有点在大三角形外$
$那么他作为新的三角形的顶点，拥有更大的高，面积更大，矛盾$
$所以原来的构造方法正确$
$时间复杂度O(n^2)$
![](http://7xru22.com1.z0.glb.clouddn.com/17-4-12/82888166-file_1491935866495_b4d6.png)

题外话：
$我觉得点积叉积还是写函数比较好，不重载运算符比较好$
$避免更多的括号，省得出事，而且好看，算是更新了一下板子$

代码：

```cpp
//
//  Created by TaoSama on 2017-04-10
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
#define pr(x) cerr << #x << " = " << x << "  "
#define prln(x) cerr << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long Type;
struct Point {
    Type x, y;
    Point() {}
    Point(Type x, Type y) : x(x), y(y) {}
    void read() {scanf("%lld%lld", &x, &y);}
    void write() {printf("%lld %lld\n", x, y);}
    Point operator+(const Point& p) const {
        return Point(x + p.x, y + p.y);
    }
    Point operator-(const Point& p) const {
        return Point(x - p.x, y - p.y);
    }
    bool operator<(const Point& p) const {
        return x != p.x ? x < p.x : y < p.y;
    }
};

Type dot(const Point& A, const Point& B) {
    return A.x * B.x + A.y * B.y;
}
Type det(const Point& A, const Point& B) {
    return A.x * B.y - A.y * B.x;
}

//输入不能有重点，函数执行完后输入顺序被破坏
Point ps[N], ch[N];
int convexHull(Point* p, int n, Point* ch) {
    sort(p, p + n);

    int m = 0;
    for(int i = 0; i < n; ++i) {
        while(m > 1 && det(ch[m - 1] - ch[m - 2], p[i] - ch[m - 2]) <= 0) --m;
        ch[m++] = p[i];
    }
    for(int i = n - 2, t = m; ~i; --i) {
        while(m > t && det(ch[m - 1] - ch[m - 2], p[i] - ch[m - 2]) <= 0) --m;
        ch[m++] = p[i];
    }
    if(n > 1) --m;
    return m;
}

vector<int> rotatingCalipers(Point* ch, int n) {
    if(n < 3) return vector<int>();

    Type ans = 0;
    vector<int> ret(3);

    ch[n] = ch[0];
    for(int i = 0; i < n; ++i) {
        for(int j = i + 1, k = j; j < n; ++j) {
            while(det(ch[j] - ch[i], ch[k + 1] - ch[i])
                    > det(ch[j] - ch[i], ch[k] - ch[i]))
                k = (k + 1) % n;
            if(det(ch[j] - ch[i], ch[k] - ch[i]) > ans) {
                ans = det(ch[j] - ch[i], ch[k] - ch[i]);
                ret = {i, j, k};
            }
        }
    }
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int n; long long s;
    scanf("%d%lld", &n, &s);
    for(int i = 0; i < n; ++i) ps[i].read();
    int m = convexHull(ps, n, ch);

    vector<int> triangle = rotatingCalipers(ch, m);
    for(int i = 0; i < 3; ++i) {
        Point p = ch[triangle[i]] + ch[triangle[(i + 1) % 3]] - ch[triangle[(i + 2) % 3]];
        p.write();
    }


    return 0;
}
```