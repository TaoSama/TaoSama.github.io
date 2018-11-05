---
title: POJ 2482 Stars in Your Window（扫描线、线段树）
categories:
  - 计算几何
  - 扫描线
  - 
tags:
  - 扫描线
  - 
  - 
date: 2016-08-29 19:50:10
toc: 
---

题意： 
>$给定N\le 10^4个星星，每个有权值1\le w_i\le 100$
$现有W\times H的矩形，问能严格框住的星星最大权值和是多少$

<!-- more -->
分析：
>$考虑把问题转化一下，先不管严格框住，考虑极限情况，一个星星位于左下角$
$那么就可以得到框住它的矩形是(x,\ y)，(x+w,\ y+h)了$
$问题就转化成了类似于矩形面积并的问题，不过这里是求最大值$
$之后扫描线一波、线段树区间更新一波就好了$
$对于严格的情况，闭区间，可以把y+h变成y+h-1，w+h变成w+h-1$
$但是扫描线的时候要注意，因为是闭区间了，所以当重合的时候，要先添加再删除$
$当然扫描线每次都要注意这个的。。$
$嘛，只有查询maxv[1]，写个标记永久化偷懒不用pushDown$
$时间复杂度O(nlogn)$

$代码：$
```cpp
//
//  Created by TaoSama on 2016-08-24
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
const int N = 1e4 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long LL;
LL x[N], y[N], r[N];
LL maxv[N << 3], addv[N << 3];

void update(int L, int R, int v, int l, int r, int rt) {
    if(L <= l && r <= R) {
        maxv[rt] += v;
        addv[rt] += v;
        return ;
    }
    int m = l + r >> 1;
    if(L <= m) update(L, R, v, l, m, rt << 1);
    if(R > m) update(L, R, v, m + 1, r, rt << 1 | 1);
    maxv[rt] = max(maxv[rt << 1], maxv[rt << 1 | 1]) + addv[rt];
}

int n, w, h;

#define y1 fddsfsdfds
struct Line {
    LL x, d, y1, y2;
    Line() {}
    Line(LL x, LL d, LL y1, LL y2): x(x), d(d), y1(y1), y2(y2) {}
    bool operator<(const Line& l) const {
        if(x == l.x) return d < l.d;
        return x < l.x;
    }
};

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d%d", &n, &w, &h) == 3) {
        vector<LL> ys;
        for(int i = 1; i <= n; ++i) {
            scanf("%lld%lld%lld", x + i, y + i, r + i);
            ys.push_back(y[i]);
            ys.push_back(y[i] + h - 1);
        }
        sort(ys.begin(), ys.end());
        ys.resize(unique(ys.begin(), ys.end()) - ys.begin());

        vector<Line> events;
        for(int i = 1; i <= n; ++i) {
            LL y1 = lower_bound(ys.begin(), ys.end(), y[i]) - ys.begin() + 1;
            LL y2 = lower_bound(ys.begin(), ys.end(), y[i] + h - 1) - ys.begin() + 1;
            events.push_back(Line(x[i], -r[i], y1, y2)); //+ first
            events.push_back(Line(x[i] + w - 1, r[i], y1, y2)); 
        }
        sort(events.begin(), events.end());

        memset(maxv, 0, sizeof maxv);
        memset(addv, 0, sizeof addv);

        LL ans = 0;
        for(int i = 0; i < events.size(); ++i) {
            Line& e = events[i];
            LL d = -e.d, y1 = e.y1, y2 = e.y2;
            update(y1, y2, d, 1, ys.size(), 1);
//            pr(e.x); pr(d);pr(y1);pr(y2);prln(maxv[1]);
            ans = max(ans, maxv[1]);
        }
        printf("%lld\n", ans);
    }

    return 0;
}

```