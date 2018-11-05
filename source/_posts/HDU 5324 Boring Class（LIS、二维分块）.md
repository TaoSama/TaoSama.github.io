---
title: HDU 5324 Boring Class（LIS、二维分块）
categories:
  - 动态规划
  - 最长上升子序列
  - 
tags:
  - LIS
  - 二维分块
date: 2016-03-28 20:31:10
toc: 
---
题意：
>$N\le 5\times 10^4，给定2个长度为N的序列，A_i，B_i$
$现要选出对于2个序列同样的子序列，假设下标为p_1\le p_2\le \dots\le p_m$
$满足A_{p_1}\ge A_{p_2}\dots\ge A_{p_m},\ 且B_{p_1}\ge B_{p_2}\dots\ge B_{p_m}$
$求最长的这样的子序列，打印下标，多解输出字典序最小解$

<!-- more -->

分析：
>$如果直接按照LIS的dp方程转移是n^2的，考虑优化一下转移$
$由于要最小字典序的，所以得倒着搞，因为状态是以i结尾的嘛$
$倒着搞的话，对于一个点(up_i, down_i)看成(x,y)来说$
$每次转移就是找左上角的点，即x要小，y要大的点$
$这个我们可以用二维分块来搞，以\sqrt n的代价来转移$
$cdq分治，树套树都可以$
$时间复杂度为O(n\sqrt n)或者O(nlog^2n)$


二维分块代码：
```cpp
//
//  Created by TaoSama on 2016-03-22
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
const int B = 250;

int n;
int x[N], y[N];

typedef pair<int, int> P;

P pre[B][B]; //x's prefix max
struct Node {
    int first, second;
    int id;
} X[N], Y[N];

void compress(int *x, int delta) {
    vector<pair<int, int> > xs;
    for(int i = 1; i <= n; ++i) xs.push_back({x[i], delta * i});
    sort(xs.begin(), xs.end());
    for(int i = 1; i <= n; ++i)
        x[i] = lower_bound(xs.begin(), xs.end(), make_pair(x[i],
                           delta * i)) - xs.begin() + 1;
}

void getMax(P& x, P y) {
    if(x.first < y.first) x = y;
    else if(x.first == y.first && x.second > y.second) x = y;
}

void update(int x, int y, int v, int id) {
    X[y] = {x, v, id}, Y[x] = {y, v, id};
    for(int i = 0; i <= y / B; ++i) getMax(pre[x / B][i], {v, id});
}

pair<int, int> query(int x, int y) {
    pair<int, int> ret = {0, 0};
    int tx = x / B, ty = y / B;
    for(int i = tx * B; i < x; ++i)
        if(Y[i].first > y) getMax(ret, {Y[i].second, Y[i].id});
    for(int i = y + 1; i < (ty + 1) * B; ++i)
        if(X[i].first < x) getMax(ret, {X[i].second, X[i].id});
    for(int i = 0; i < tx; ++i) getMax(ret, pre[i][ty + 1]);
    return ret;
}

int prevv[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    while(scanf("%d", &n) == 1) {
        for(int i = 1; i <= n; ++i) scanf("%d", x + i);
        for(int i = 1; i <= n; ++i) scanf("%d", y + i);
        compress(x, -1);
        compress(y, 1);

        memset(pre, 0, sizeof pre);
        memset(Y, 0xc0, sizeof Y);
        memset(X, 0x3f, sizeof X);

        P ans = {0, 0};
        memset(prevv, 0, sizeof prevv);
        for(int i = n; i >= 1; --i) {
            P ret = query(x[i], y[i]);
            prevv[i] = ret.second;

            update(x[i], y[i], ret.first + 1, i);
            getMax(ans, {ret.first + 1, i});
        }

        printf("%d\n", ans.first);
        vector<int> path;
        int u = ans.second;
        for(int i = 1; i <= ans.first; ++i) {
            path.push_back(u);
            u = prevv[u];
        }
        for(int i = 0; i < path.size(); ++i)
            printf("%d%c", path[i], " \n"[i == path.size() - 1]);
    }
    return 0;
}

```