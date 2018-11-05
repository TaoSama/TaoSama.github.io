---
title: HDU 4631 Sad Love Story（离线思想、分治）
categories:
  - 思维
  - 分治
  - 
tags:
  - 分治
  - 
date: 2016-03-23 23:20:10
toc: 
---
题意：
>$N\le 5\times 10^5，给定二维平面上N个点，定义距离为欧氏距离的平方$
$挨个加入每个点，对于i>1的所有点，求[1,i]的最近点对距离，输出这些距离和$

<!-- more -->

分析：
>$考虑离线，倒着做，求一次最近点对，假设得到的点为(p,\ q)，p < q$
$那么显然[q,n]的答案都是dis(p,q)$
$去掉[q,n]这些点，做[1,q-1]的部分，成为子问题$
$由于是随机数据，每次问题规模下降1/3，所以总体复杂度大概是O(nlog^2n)$

----
>$其实还可以直接暴力，挨个添加点到set，设当前答案为d$
$对于每个点，设坐标为(x,y)，暴力查询横坐标在[x-d,\ x+d]范围的点$
$由于随机数据，答案下降的很快，所以跑的飞快$
$时间复杂度大概在O(nlogn)$


第一种解法代码：
```cpp
//
//  Created by TaoSama on 2016-03-18
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:1024000000,1024000000")
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
const int N = 5e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
typedef pair<int, int> P;
typedef long long LL;
P a[N]; int id[N];

pair<LL, P> dfs(int l, int r) {
    if(l == r) return {~0ull >> 2, { -1, -1}};
    int m = l + r >> 1;
    LL x = a[id[m]].first;

    auto ret = min(dfs(l, m), dfs(m + 1, r));
    inplace_merge(id + l, id + m + 1, id + r + 1, [](int x, int y) {
        return a[x].second < a[y].second;
    });

    vector<int> v;
    for(int i = l; i <= r; ++i) {
        int p = id[i];
        if((a[p].first - x) * (a[p].first - x) >= ret.first) continue;
        for(int j = 0; j < v.size(); ++j) {
            int q = v[v.size() - 1 - j];
            LL dy = a[p].second - a[q].second;
            if(dy * dy >= ret.first) break;
            LL dx = a[p].first - a[q].first;
            LL d = dx * dx + dy * dy;
            if(d < ret.first) ret = {d, {p, q}};
        }
        v.push_back(p);
    }
    return ret;
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
        int a1, b1, c1, a2, b2, c2;
        scanf("%d%d%d", &a1, &b1, &c1);
        scanf("%d%d%d", &a2, &b2, &c2);
        for(int i = 1; i <= n; ++i) {
            a[i].first = (1LL * a1 * a[i - 1].first + b1) % c1;
            a[i].second = (1LL * a2 * a[i - 1].second + b2) % c2;
        }

        LL ans = 0;
        for(int r = n; r > 1;) {
            for(int i = 1; i <= r; ++i) id[i] = i;
            sort(id + 1, id + 1 + r, [](int x, int y) {
                return a[x] < a[y];
            });
            auto cp = dfs(1, r);
            int nxt = max(cp.second.first, cp.second.second);

            ans += cp.first * (r - nxt + 1);
            r = nxt - 1;
        }
        printf("%I64d\n", ans);
    }
    return 0;
}


```