---
title: HDU 5299 Circles Game (圆的扫描线、树上sg)
categories:
  - 计算几何
  - 扫描线
  - 
tags:
  - 扫描线
  - 树上sg
date: 2016-04-27 19:44:10
toc: 
---
题意：
>$平面上有N\le 5\times 10^4个两两不交的圆，现在有两个人轮流选取圆$
$每选到一个圆就要把这个圆及其内部的所有圆都删去，最后不能操作的人输$
$问谁有必胜策略$

<!-- more -->

分析：
>$由于圆两两不交，如果根据圆的包含关系建个图（即每个圆向最近包含它的圆连边），可以得到一个森林$
$问题转化为树上的SG博弈，时间复杂度O(nlogn)$

---
(以下转载自[http://csgrandeur.com/hdu3511-prison-break-guan-yu-yuan-de-sao-miao-xian/](http://csgrandeur.com/hdu3511-prison-break-guan-yu-yuan-de-sao-miao-xian/)

>$建图需要用到圆的扫描线，具体看下图：$
![](http://7xru22.com1.z0.glb.clouddn.com/16-4-27/68350085.jpg) ![](http://7xru22.com1.z0.glb.clouddn.com/16-4-27/49884970.jpg)
$首先和传统扫描线的方法一样先把每个圆左右侧x坐标排个序作为事件，然后开始扫描$
$圆就麻烦在没有像矩形那样可以离散化的规则上下界，便无法用预处理好的离散编号来构建线段树$
>$但是我们可以注意到对于扫描线扫描的过程中从上到下穿过各个圆的顺序是不会变的$
$所以可以利用二叉树，把扫描线经过的(边)有序地插入（这里用set就很方便高效了）$
$对于圆来说，这个边就是与上半圆交点纵坐标和与下半圆交点纵坐标$
$即使扫描线位置的变化，插入时用来比较的代表(边)的纵坐标会变化$
$但是扫描线穿过圆的顺序是不会变的，所以新的(边)依然会插入到正确的位置，其他(边)的相对位置不会改变$
$这样set二叉树的结构不会变，就可以修改比较函数里的全局变量$
$一共有4种情况，见代码吧$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-27
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

double timeLine;
struct Circle {
    double x, y, r;
    void read() {
        scanf("%lf%lf%lf", &x, &y, &r);
    }
    double getY(int up) {
        return y + up * (sqrt(r * r - (x - timeLine) * (x - timeLine)));
    }
} c[N];

typedef pair<double, int> Event; //x
Event e[N << 1];

struct Node {
    int id, d;
    bool operator<(const Node& r) const {
        double y1 = c[id].getY(d), y2 = c[r.id].getY(r.d); //y
        return sgn(y1 - y2) ? y1 < y2 : d < r.d;
    }
};

int n, dep[N], p[N];
vector<int> G[N];

int dfs(int u) {
    int sg = 0;
    for(int v : G[u]) sg ^= dfs(v) + 1;
    return sg;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 0; i <= n; ++i) G[i].clear();
        for(int i = 1; i <= n; ++i) {
            c[i].read();
            e[2 * i - 1] = {c[i].x - c[i].r, i};
            e[2 * i] = {c[i].x + c[i].r, -i};
        }
        sort(e + 1, e + 2 * n + 1);

        set<Node> s;
        memset(dep, 0, sizeof dep);
        for(int i = 1; i <= 2 * n; ++i) {
            timeLine = e[i].first;
            int id = e[i].second;

            if(id < 0) {
                s.erase({ -id, 1});
                s.erase({ -id, -1});
                continue;
            }

            auto up = s.lower_bound({id, 1}), dw = up;
            if(up == s.end() || dw == s.begin()) { //无包含
                p[id] = 0;
                dep[id] = 1;
                G[0].push_back(id);
            } else if((--dw)->id == up->id) { //直接被包含
                p[id] = dw->id;
                dep[id] = dep[dw->id] + 1;
                G[dw->id].push_back(id);
            } else {
                if(dep[dw->id] == dep[up->id]) { //三圆是兄弟
                    p[id] = p[dw->id];
                    dep[id] = dep[dw->id];
                    G[p[dw->id]].push_back(id);
                } else { //两圆是兄弟，另一圆（深度小的）包两圆
                    if(dep[dw->id] > dep[up->id]) swap(up, dw);
                    p[id] = dw->id;
                    dep[id] = dep[dw->id] + 1;
                    G[dw->id].push_back(id);
                }
            }

            s.insert({id, 1});
            s.insert({id, -1});
        }

//      for(int i = 1; i <= n; ++i) printf("%d->%d\n", p[i], i);

        puts(dfs(0) ? "Alice" : "Bob");
    }
    return 0;
}

```