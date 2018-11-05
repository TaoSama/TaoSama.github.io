---
title: Educational Codeforces Round 10 D. Nested Segments（离线思想、BIT）
categories:
  - 思维
  - 离线思想
  - 
tags:
  - BIT
  - 离线
date: 2016-03-28 22:00:10
toc: 
---
题意：
>$N \le 2\times 10^5个线段，问第i个线段包含多少个其它线段$

<!-- more -->

分析：
>$经典离线套路题了，询问右端点排序$
$从左往右扫描每个数i（相当于固定了右端点）$
$然后对于所有[L,\ i]的询问，查询sum(L,\ i)就是答案了$
$这里其实把BIT倒过来就可以完成了，向前更新，向后查询$
$之后把每个询问（线段）的左端点添加进线段树中$
$时间复杂度O(nlogn)$


代码：
```cpp
//
//  Created by TaoSama on 2016-03-25
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
const int N = 4e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, ans[N];
pair<int, int> tmp[N];
vector<pair<int, int> > qs[N];

struct BIT {
    int n, b[N];
    void init(int _n) {
        n = _n;
        memset(b, 0, sizeof b);
    }
    void add(int i, int v) {
        for(; i; i -= i & -i) b[i] += v;
    }
    int sum(int i) {
        int ret = 0;
        for(; i <= n; i += i & -i) ret += b[i];
        return ret;
    }
} bit;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d", &n);
    vector<int> xs;
    for(int i = 1; i <= n; ++i) {
        int l, r; scanf("%d%d", &l, &r);
        xs.push_back(l);
        xs.push_back(r);
        tmp[i] = {l, r};
    }
    sort(xs.begin(), xs.end());
    xs.resize(unique(xs.begin(), xs.end()) - xs.begin());

    for(int i = 1; i <= n; ++i) {
        int l = tmp[i].first, r = tmp[i].second;
        l = lower_bound(xs.begin(), xs.end(), l) - xs.begin() + 1;
        r = lower_bound(xs.begin(), xs.end(), r) - xs.begin() + 1;
        qs[r].push_back({l, i});
    }

    bit.init(xs.size());
    for(int i = 1; i <= xs.size(); ++i) {
        for(auto& p : qs[i]) {
            ans[p.second] = bit.sum(p.first);
            bit.add(p.first, 1);
        }
    }
    for(int i = 1; i <= n; ++i) printf("%d\n", ans[i]);
    return 0;
}


```