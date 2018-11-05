---
title: Codeforces 85C Petya and Tree（树形dp）
categories:
  - 动态规划
  - 树形dp
  - 
tags:
  - 树形dp
  - 
  - 
date: 2016-08-01 10:03:10
toc: 
---

题意：
>$N\le 10^5的一棵满二叉搜索树，点权1\le A_i\le 10^9$
$满二叉搜索树：每个节点的儿子个数为0或者2$
$给定Q\le 10^5询问，每次查询一个值1\le q\le 10^9，保证值没有在BST中出现过$
$并且查询过程中一定会出错有且仅有一次，即本该去左子树去了右子树，反之亦然$
$求在BST中查询这个值的期望$

<!-- more -->
分析：
>$这是一道分析想法题，首先有个很显然的结论：$
$所有正确查询情况下出现在同一个叶子的期望值必然相等，$
$并且对于最后一层的2个叶子，只会出现在其中1个$
$玩玩样例(看看别人代码)可以发现：$
$如果某个值在查询过程中，这次出错了，假设本应该去左子树$
$但去了右子树，那么显然在右子树中只能不断往左走，即贡献应该是那个最小值$
$反之同理，贡献是个最大值$
$问题解决了一半，树形dp维护子树的最大值和最小值$
$并通过这个再次树形dp计算出每个叶子的贡献累和$
$接下来考虑一下如何找到这个叶子$
$再玩玩样例(看看别人代码)可以发现：$
$找到这个数有3种可能，直接在所有点权upper\_bound$
$1个是直接找到，不然找不到一定是最后那个$
$还有1种情况必然是某个叶子的父亲，只要取它的左儿子就好了$
$因为比这个值大，要往左走$
$至此做完了，时间复杂度为O(n+nlogn)$

代码:
```cpp
//
//  Created by TaoSama on 2016-08-01
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

int n, val[N];
int rt, ls[N], rs[N];
int minv[N], maxv[N], dep[N];
double f[N];

void dfs1(int u) {
    if(!ls[u]) {
        minv[u] = maxv[u] = val[u];
        return;
    }

    int v1 = ls[u], v2 = rs[u];
    dfs1(v1);
    dfs1(v2);

    minv[u] = minv[v1];
    maxv[u] = maxv[v2];
}

void dfs2(int u) {
    if(!ls[u]) return;

    int v1 = ls[u], v2 = rs[u];
    dep[v1] = dep[v2] = dep[u] + 1;
    f[v1] = f[u] + minv[v2];
    f[v2] = f[u] + maxv[v1];

    dfs2(v1);
    dfs2(v2);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d", &n);

    vector<pair<int, int> > keys;
    for(int i = 1; i <= n; ++i) {
        int fa; scanf("%d%d", &fa, val + i);
        if(fa == -1) rt = i;
        else if(!ls[fa]) ls[fa] = i;
        else rs[fa] = i;
        if(val[ls[fa]] > val[rs[fa]]) swap(ls[fa], rs[fa]);
        keys.push_back({val[i], i});
    }
    sort(keys.begin(), keys.end());

    dfs1(rt);
    dfs2(rt);

    int q; scanf("%d", &q);
    while(q--) {
        int x; scanf("%d", &x);
        auto iter = upper_bound(keys.begin(), keys.end(), make_pair(x, -INF));
        if(iter == keys.end()) --iter;
        if(ls[iter->second]) --iter; //must be leaves
        int y = iter->second;
        printf("%.10f\n", f[y] / dep[y]);
    }


    return 0;
}
```