---
title: HDU 5732 Subway（树哈希）
categories:
  - 技巧
  - 哈希
  - 
tags:
  - 树哈希
  - 
  - 
date: 2016-07-24 21:48:10
toc: 
---

题意：
>$给定N\le 10^5个节点的2棵树，保证2棵树同构$
$输出一种2棵树的节点映射方式$

<!-- more -->
分析：
>$树同构，想一个和节点顺序无关的哈希函数将树表示$
$一种方法：首先找树的中点，然后将中点作为根$
$叶子节点哈希值为1，对于每一颗树，把它的所有子树的哈希 值排序，然后算出新的哈希值作为总体的哈希值$
$有2个中点的树2个中点都试一下。为了保险可以检查下哈希值有没有重的$
$双hash速度跟标程一样。。所以还不如。$
$直接$`map<vector<int>, int>`$搞都行。。10^5的数据只有2个$
$时间复杂度O(nlogn)$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-21
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

typedef long long LL;
const LL seed[2] = {999999937, 999999929};
const LL mod[2] = {1000000007, 1000000009}; //1e9+7/+9
struct Hash {
    LL h[2];
    Hash() {}
    Hash(const LL& first) {
        for(int i = 0; i < 2; ++i) h[i] = first;
    }
    Hash operator+(const Hash& rhs) const {
        Hash ret = *this;
        for(int i = 0; i < 2; ++i) {
            ret.h[i] = ret.h[i] * seed[i] % mod[i];
            if((ret.h[i] += rhs.h[i]) >= mod[i]) ret.h[i] -= mod[i];
        }
        return ret;
    }
    bool operator<(const Hash& rhs) const {
        return make_pair(h[0], h[1]) < make_pair(rhs.h[0], rhs.h[1]);
    }
    bool operator==(const Hash& rhs) const {
        return make_pair(h[0], h[1]) == make_pair(rhs.h[0], rhs.h[1]);
    }

    void see() {
        printf("(%llu %llu)\n", h[0], h[1]);
    }
};

int n;
vector<int> G[2][N];

void getDiameter(int u, int f, int d, int idx, pair<int, int>& diameter) {
    diameter = max(diameter, make_pair(d, u));
    vector<int>& cur = G[idx][u];
    for(auto& v : cur) {
        if(v == f) continue;
        getDiameter(v, u, d + 1, idx, diameter);
    }
}

bool getPath(int u, int f, int t, int idx, vector<int>& path) {
    path.push_back(u);
    if(u == t) return true;
    vector<int>& cur = G[idx][u];
    for(auto& v : cur) {
        if(v == f) continue;
        if(getPath(v, u, t, idx, path)) return true;
    }
    path.pop_back();
    return false;
}

vector<pair<Hash, int> > treeHash[2][N];

Hash getHash(int u, int f, int idx) {
    vector<int>& cur = G[idx][u];
    vector<pair<Hash, int> > sons;
    for(auto& v : cur) {
        if(v == f) continue;
        sons.push_back({getHash(v, u, idx), v});
    }

    Hash h(1);
    sort(sons.begin(), sons.end());
    for(auto& v : sons) h = h + v.first;
    treeHash[idx][u].swap(sons);
    return h;
}

map<string, int> mp[2];
vector<string> names[2];
int ID(string s, int idx) {
    if(mp[idx].count(s)) return mp[idx][s];
    names[idx].push_back(s);
    mp[idx][s] = names[idx].size();
    return mp[idx][s];
}

void init(int idx) {
    mp[idx].clear(); names[idx].clear();
    for(int i = 1; i <= n; ++i) G[idx][i].clear();
}

void OLE(string s) {
    while(1)
        puts(s.c_str());
}

void RE() {
    printf("%d\n", *((int*)0));
}

bool match(vector<pair<Hash, int> >& cur, vector<pair<Hash, int> >& nxt,
           vector<pair<int, int> >& ans) {
    if(cur.size() != nxt.size()) return false;
    int cnt = 0;
    for(int i = 0; i < cur.size(); ++i) {
        bool ok = false;
        for(int j = i; j < nxt.size() && cur[i].first == nxt[j].first; ++j) {
            swap(nxt[i], nxt[j]);
            if(match(treeHash[0][cur[i].second], treeHash[1][nxt[i].second], ans)) {
                ++cnt;
                ok = true;
                ans.push_back({cur[i].second, nxt[i].second});
                break;
            }
        }
        if(!ok) {
            while(cnt--) ans.pop_back();
            return false;
        }
    }
    return true;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\1010.in", "r", stdin);
//    freopen("C:\\Users\\TaoSama\\Desktop\\out.txt", "w", stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d", &n) == 1) {
        for(int i = 0; i < 2; ++i) {
            init(i);

            for(int j = 1; j < n; ++j) {
                char a[20], b[20]; scanf("%s%s", a, b);
                int u = ID(a, i), v = ID(b, i);
//                printf("%d->%d\n", u, v);
                G[i][u].push_back(v);
                G[i][v].push_back(u);
            }
//            puts("");
        }

        vector<pair<Hash, int> > hashes[2];
        for(int i = 0; i < 2; ++i) {
            pair<int, int> diameter = { -INF, -INF};
            getDiameter(1, -1, 0, i, diameter);
            int u = diameter.second;
            diameter = { -INF, -INF};
            getDiameter(u, -1, 0, i, diameter);
            int v = diameter.second;

//            printf("diameter %d -> %d\n", u, v);

            vector<int> path;
            getPath(u, -1, v, i, path);
            int sz = path.size();
            int x = path[sz >> 1], y = -1;
            if(~path.size() & 1) y = path[sz / 2 - 1];
            hashes[i].push_back({getHash(x, y, i), x});
            if(~y) hashes[i].push_back({getHash(y, x, i), y});

//            printf("start %d %d\n", x, y);

            sort(hashes[i].begin(), hashes[i].end());
        }

        vector<pair<int, int> > ans;
        if(!match(hashes[0], hashes[1], ans)) RE();

//        prln(ans.size());
        if(ans.size() != n) OLE("WA");

        for(int i = 0; i < ans.size(); ++i) {
            int u, v; tie(u, v) = ans[i];
            printf("%s %s\n", names[0][u - 1].c_str(), names[1][v - 1].c_str());
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```