---
title: HDU 5738 Eureka（贡献、极角排序）
categories:
  - 数学
  - 贡献
  - 
tags:
  - 贡献
  - 极角排序
  - 
date: 2016-07-24 22:19:10
toc: 
---

题意：
>$N\le 10^3个点，求有多少子集共线$

<!-- more -->
分析：
>$首先合并重点，然后把点字典序排序，直线向量x就恒正了$
$枚举1个端点，对于所有字典序比它大的点极角排序一波$
$对于所有共线的点，就是左端点选一个非空子集，右端点选一个非空子集$
$所以贡献E_1=(2^{cnt_1}-1)\times (2^{cnt_2}-1)$
$当然重点自己也要算下贡献，E_2=2^{cnt}-1-cnt$
$这个就是选取2个端点（外面空0个点），然后外面空1个，2个点...里面随便选$
$E_2=2^{cnt-2}+2\times 2^{cnt-3}+3\times 2^{cnt-4}+\cdots+(cnt-2)\times 2^1+(cnt-1)$
$乘2错位一下，2E_2=2^{cnt-1}+2\times 2^{cnt-2}+3\times 2^{cnt-3}+(cnt-1)\times 2^1$
$一减，E_2=2^{cnt-1}+2^{cnt-2}+2^{cnt-3}+\cdots+2^1-(cnt-1)$
$等比数列求和一下，E_2=\frac{2\times (1-2^{cnt-1})}{1-2}-(cnt-1)=2^{cnt}-2-cnt+1=2^{cnt}-1-cnt$
$然后就做完了，时间复杂度，常数很小的O(n^2logn)$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-22
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
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
struct Point {
    int x, y;
    void read() {scanf("%d%d", &x, &y);}
    Point() {}
    Point(int x, int y): x(x), y(y) {}
    bool operator<(const Point& rhs) const {
        return make_pair(x, y) < make_pair(rhs.x, rhs.y);
    }
    bool operator==(const Point& rhs) const {
        return make_pair(x, y) == make_pair(rhs.x, rhs.y);
    }
    Point operator-(const Point& rhs) const {
        return Point(x - rhs.x, y - rhs.y);
    }
    void reduce() {
        int g = __gcd(abs(x), abs(y));
        if(g) x /= g, y /= g;
    }
} p[N];

int two[N] = {1};

void add(int& x, int y) {
    if((x += y) >= MOD) x -= MOD;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    for(int i = 1; i < N; ++i) two[i] = 2 * two[i - 1] % MOD;

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        map<Point, int> mp;
        for(int i = 1; i <= n; ++i) {
            p[i].read();
            ++mp[p[i]];
        }

        vector<pair<Point, int> > cnt(mp.begin(), mp.end());

        int ans = 0;
        for(int i = 0; i < cnt.size(); ++i) {
            Point p; int c1;
            tie(p, c1) = cnt[i];
            vector<pair<Point, int> > v;
            for(int j = i + 1; j < cnt.size(); ++j) {
                Point np; int c2;
                tie(np, c2) = cnt[j];
                Point o = np - p; o.reduce();
                v.push_back({o, c2});
            }
            sort(v.begin(), v.end());

            add(ans, two[c1] - 1 - c1); //x points is collinear
            for(int x = 0, y; x < v.size(); x = y) {
                int c2 = 0;
                for(y = x; y < v.size(); ++y) {
                    if(v[x].first == v[y].first) c2 += v[y].second;
                    else break;
                }
                int e = 1LL * (two[c1] - 1) * (two[c2] - 1) % MOD;
                add(ans, e);
            }
        }
        printf("%d\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```