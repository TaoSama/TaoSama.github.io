---
title: Codeforces Round 345 (Div. 2) E. Table Compression（拓扑排序）
categories:
  - 图论
  - 拓扑排序
tags:
  - 拓扑排序
  - 
date: 2016-03-08 20:45:10
toc:
---
题意：
>$n*m \le 10^6的矩阵，现在要压缩矩阵里的数字的大小，使得最大的数字尽量小$
$压缩的要求是，保证每行或者每列的相对数字大小不变，并且每行或者每列的相等的数字压缩后还相等$

<!-- more -->
分析：
>$大小关系是一种拓扑关系，所以我们可以建图拓扑排序$
$由于每行或者每列的相等数字大小不变，所以先把它们用并查集缩点$
$如果暴力向比它大的数字连边的话，边数将是n^3级别的，会爆炸$
$考虑拓扑关系具有传递性，只需要向第一个比它大的连边即可，这样边数就是n^2级别了$
$我们只要将每行或者每列排序，二分查找这个数就可以了$
$时间复杂度O(nmlognm)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-08
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
const int N = 1e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;

struct DSU {
    int n, p[N];
    void init(int _n) {
        n = _n;
        for(int i = 0; i < n; ++i) p[i] = i;
    }
    int find(int x) {
        return p[x] = p[x] == x ? x : find(p[x]);
    }
    void unite(int x, int y) {
        x = find(x), y = find(y);
        if(x == y) return;
        p[x] = y;
    }
} dsu;

typedef pair<int, int> P;

void merge(vector<P>& tmp) {
    sort(tmp.begin(), tmp.end());
    int sz = tmp.size();
    for(int j = 0; j < sz; ++j) {
        int p = j;
        while(j + 1 < sz && tmp[j + 1].first == tmp[p].first) {
            dsu.unite(tmp[j + 1].second, tmp[p].second);
            ++j;
        }
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d", &n, &m);
    vector<vector<P> >  a(n, vector<P>(m)), b(m, vector<P>(n));
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < m; ++j) {
            int x; scanf("%d", &x);
            a[i][j] = {x, i * m + j};
            b[j][i] = {x, i * m + j};
        }
    }

    dsu.init(n * m);
    //merge row equal values
    for(int i = 0; i < n; ++i) merge(a[i]);
    //merge column equal values
    for(int i = 0; i < m; ++i) merge(b[i]);

    //new graph
    vector<int> in(n * m, 0);
    vector<vector<int> > g(n * m);
    for(int i = 0; i < n; ++i) {
        vector<P>& cur = a[i];
        for(int j = 0; j < m; ++j) {
            auto iter = upper_bound(cur.begin(), cur.end(), P(cur[j].first, INF));
            if(iter == cur.end()) continue;
            int u = dsu.find(cur[j].second), v = dsu.find(iter->second);
            g[u].push_back(v); ++in[v];
            for(int p = j; j < m && cur[j + 1].first == cur[p].first; ++j);
        }
    }
    for(int i = 0; i < m; ++i) {
        vector<P>& cur = b[i];
        for(int j = 0; j < n; ++j) {
            auto iter = upper_bound(cur.begin(), cur.end(), P(cur[j].first, INF));
            if(iter == cur.end()) continue;
            int u = dsu.find(cur[j].second), v = dsu.find(iter->second);
            g[u].push_back(v); ++in[v];
            for(int p = j; j < n && cur[j + 1].first == cur[p].first; ++j);
        }
    }

    queue<int> q;
    vector<int> ans(n * m, 0);
    for(int i = 0; i < n * m; ++i) {
        if(in[i]) continue;
        q.push(i);
        ans[i] = 1;
    }
    while(q.size()) {
        int u = q.front(); q.pop();
        for(int v : g[u]) {
            ans[v] = max(ans[v], ans[u] + 1);
            if(--in[v] == 0) q.push(v);
        }
    }

    for(int i = 0; i < n; ++i)
        for(int j = 0; j < m; ++j)
            printf("%d%c", ans[dsu.find(i * m + j)], " \n"[j == m - 1]);
    return 0;
}
```