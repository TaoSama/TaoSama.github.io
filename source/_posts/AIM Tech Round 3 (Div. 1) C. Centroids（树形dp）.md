---
title: AIM Tech Round 3 (Div. 1) C. Centroids（树形dp）
categories:
  - 动态规划
  - 树形dp
  - 
tags:
  - 树形dp
  - 
  - 
date: 2016-08-26 15:36:10
toc: 
---

题意： 
>$给定N\le 4\times 10^5的一棵树，询问每个点是否能通过删掉1条边再添加1条边成为重心$
$重心：删除这个点，所有连通分量的最大大小\le n/2$

<!-- more -->
分析：
>$首先考虑重心是怎么求的，显然树dp算所有的与之相连的子树的大小$
$对于1个点u，如果要让它成为重心，显然要让跟它所有相连的子树的大小都变成\le n/2的$
$而题目中说的删边其实就是等价于把一个子树连到u上的操作$
$由于只能干一次，所以实际上我们肯定找\le n/2最大的那颗子树分裂出来$
$所以dp的方式就显而易见了，当然儿子父亲都要搞$
$maxSub[u]:=u的子树中\le n/2的最大子树的大小$
$f[u]:=u的父亲f的子树中，除了u这颗子树外，\le n/2的最大子树的大小$
$前者直接暴力转移就好，后者维护一下\le n/2的最大和次大就好$
$因为对于f来说如果u是最大不能选那么选次大，反之就选最大$
$当然对于父亲那边，我们需要dfs再搞一次，先继承一下父亲的答案$
$然后再减去u这颗子树，看看整个父亲的部分能不能更新$
$判断可行的时候，直接查看每个儿子就好了，判一判就做完了$
$时间复杂度O(n)$

```cpp
//
//  Created by TaoSama on 2016-08-25
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
const int N = 4e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
vector<int> G[N];
int sz[N], maxSub[N], fa[N];
//u子树中不超过n/2的最大子树
//不经过u这颗子树fa子树中能取到的不超过n/2的最大子树
//再dfs一次变成不经过u这颗子树整棵树中能取到的不超过n/2的最大子树

void dfs1(int u, int f) {
    sz[u] = 1;
    int maxv = 0, nxtv = 0;
    for(int v : G[u]) {
        if(v == f) continue;
        dfs1(v, u);
        sz[u] += sz[v];

        if(maxSub[v] >= maxv) {
            nxtv = maxv;
            maxv = maxSub[v];
        } else if(maxSub[v] > nxtv) nxtv = maxSub[v];
    }

    for(int v : G[u]) {
        if(v == f) continue;
        if(maxSub[v] == maxv) fa[v] = nxtv;
        else fa[v] = maxv;
    }
    maxSub[u] = maxv;
    if(sz[u] <= n / 2) maxSub[u] = max(maxSub[u], sz[u]);
}

int ans[N];
void dfs2(int u, int f) {
    int cnt = 0, ok = 1;
    for(int v : G[u]) {
        if(v == f) continue;
        if(sz[v] > n / 2) {
            if(sz[v] - maxSub[v] > n / 2) ok = 0;
            else ++cnt;
        }
    }
    if(n - sz[u] > n / 2) {
        if(n - sz[u] - fa[u] > n / 2) ok = 0;
        else ++cnt;
    }
    ans[u] = ok && cnt <= 1;

    for(int v : G[u]) {
        if(v == f) continue;
        fa[v] = max(fa[v], fa[u]);
        if(n - sz[v] <= n / 2) fa[v] = max(fa[v], n - sz[v]);
        dfs2(v, u);
    }
}


int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        for(int i = 1; i <= n; ++i) G[i].clear();
        for(int i = 1; i < n; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            G[u].push_back(v);
            G[v].push_back(u);
        }

        dfs1(1, 0);
        dfs2(1, 0);

        for(int i = 1; i <= n; ++i)
            printf("%d%c", ans[i], " \n"[i == n]);
    }

    return 0;
}
```