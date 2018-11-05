---
title: HDU 5756 Boss Bo（主席树、标记永久化）
categories:
  - 数据结构
  - 主席树
  - 
tags:
  - 主席树
  - 
  - 
date: 2016-08-11 21:00:10
toc: 
---

题意： 
>$给定N\le 5\times 10^4个点的一棵树，Q\le 10^5$
$定义一个点是好点，当且仅当他所有祖先都不是坏点$
$每次询问指定K个点为坏点，查询1个点P到所有好点的$
$op=1:距离和$
$op=2:最小距离$
$op=3:最大距离$

<!-- more -->
分析：
>$定义换句话说，如果一个点是坏点，那么子树都是坏点$
$剩下的就是官方题解的做法了$
![](http://7xru22.com1.z0.glb.clouddn.com/16-8-13/60225630.jpg)

```cpp
//
//  Created by TaoSama on 2016-08-13
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
const int N = 5e4 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long LL;
const LL LLINF = 0x3f3f3f3f3f3f3f3fLL;

int n, q;
vector<int> G[N];

int dep[N], L[N], R[N], vs[N], dfsNum;
void dfs(int u, int fa) {
    L[u] = ++dfsNum;
    vs[dfsNum] = u;
    for(int v : G[u]) {
        if(v == fa) continue;
        dep[v] = dep[u] + 1;
        dfs(v, u);
    }
    R[u] = dfsNum;
}

int root[N];
struct PersistentSegTree {
    static const int M = N * 2 * 20;
    int sz;
    struct Node {
        int ls, rs;
        LL addv, maxv, minv, sum;
        void add(LL v, int len) {
            addv += v;
            sum += v * len;
            maxv += v;
            minv += v;
        }
        void see() {
            pr(addv); pr(maxv); pr(minv); prln(sum);
        }
    } dat[M];
    int newNode(int rt) {
        dat[++sz] = dat[rt];
        return sz;
    }
    void init() {
        sz = 0;
        memset(&dat[0], 0, sizeof dat[0]);
    }
    void up(int rt, int len) {
        dat[rt].sum = dat[dat[rt].ls].sum + dat[dat[rt].rs].sum + dat[rt].addv * len;
        dat[rt].minv = min(dat[dat[rt].ls].minv, dat[dat[rt].rs].minv) + dat[rt].addv;
        dat[rt].maxv = max(dat[dat[rt].ls].maxv, dat[dat[rt].rs].maxv) + dat[rt].addv;
    }
    void build(int l, int r, int& rt) {
        rt = newNode(0);
        if(l == r) {
            dat[rt].add(dep[vs[l]], 1);
            return;
        }
        int m = l + r >> 1;
        build(l, m, dat[rt].ls);
        build(m + 1, r, dat[rt].rs);
        up(rt, r - l + 1);
    }
    void update(int L, int R, int v, int l, int r, int& rt) {
        rt = newNode(rt);
        if(L <= l && r <= R) {
            dat[rt].add(v, r - l + 1);
            return;
        }
        int m = l + r >> 1;
        if(L <= m) update(L, R, v, l, m, dat[rt].ls);
        if(R > m) update(L, R, v, m + 1, r, dat[rt].rs);
        up(rt, r - l + 1);
    }
    LL query1(int L, int R, LL z, int l, int r, int rt) {
        if(L <= l && r <= R) return dat[rt].sum + z * (r - l + 1);
        int m = l + r >> 1;
        LL ret = 0;
        if(L <= m) ret += query1(L, R, z + dat[rt].addv, l, m, dat[rt].ls);
        if(R > m) ret += query1(L, R, z + dat[rt].addv, m + 1, r, dat[rt].rs);
        return ret;
    }
    LL query2(int L, int R, LL z, int l, int r, int rt) {
        if(L <= l && r <= R) return dat[rt].minv + z;
        int m = l + r >> 1;
        LL ret = LLINF;
        if(L <= m) ret = min(ret, query2(L, R, z + dat[rt].addv, l, m, dat[rt].ls));
        if(R > m) ret = min(ret, query2(L, R, z + dat[rt].addv, m + 1, r, dat[rt].rs));
        return ret;
    }
    LL query3(int L, int R, LL z, int l, int r, int rt) {
        if(L <= l && r <= R) return dat[rt].maxv + z;
        int m = l + r >> 1;
        LL ret = -LLINF;
        if(L <= m) ret = max(ret, query3(L, R, z + dat[rt].addv, l, m, dat[rt].ls));
        if(R > m) ret = max(ret, query3(L, R, z + dat[rt].addv, m + 1, r, dat[rt].rs));
        return ret;
    }
} T;

void dfs2(int u, int fa) {
    if(u == 1) {
        T.init();
        T.build(1, n, root[1]);
    } else {
        root[u] = root[fa];
        T.update(1, n, 1, 1, n, root[u]);
        T.update(L[u], R[u], -2, 1, n, root[u]);
    }
    for(int v : G[u]) {
        if(v == fa) continue;
        dfs2(v, u);
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &q) == 2) {
        for(int i = 1; i <= n; ++i) G[i].clear();
        for(int i = 1; i < n; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            G[u].push_back(v);
            G[v].push_back(u);
        }

        dfsNum = 0;
        dfs(1, 0);
        dfs2(1, 0);

        LL last = 0;
        for(int z = 1; z <= q; ++z) {
            int k, p, op; scanf("%d%d%d", &k, &p, &op);
            p = (p + last) % n + 1;

            vector<pair<int, int> > seg(k);
            for(int i = 0; i < k; ++i) {
                int x; scanf("%d", &x);
                seg[i] = {L[x], R[x]};
            }
            sort(seg.begin(), seg.end());

            k = 0;
            for(int i = 0, j; i < seg.size(); i = j) {
                int r = seg[i].second;
                for(j = i + 1; j < seg.size() && seg[j].first <= seg[i].second; ++j)
                    r = max(r, seg[j].second);
                seg[k++] = {seg[i].first, r};
            }
            seg.resize(k);
            seg.push_back({n + 1, n + 1});
            if(seg[0] == make_pair(1, n)) {
                puts("-1");
                last = 0;
                continue;
            }

            if(op == 1) {
                last = 0;
                for(int i = 0, l = 1; i < seg.size(); ++i) {
                    int r = seg[i].first - 1;
                    if(l <= r) {
                        last += T.query1(l, r, 0, 1, n, root[p]);
                    }
                    l = seg[i].second + 1;
                }
                printf("%I64d\n", last);
            } else if(op == 2) {
                last = LLINF;
                for(int i = 0, l = 1; i < seg.size(); ++i) {
                    int r = seg[i].first - 1;
                    if(l <= r) {
                        last = min(last, T.query2(l, r, 0, 1, n, root[p]));
                    }
                    l = seg[i].second + 1;
                }
                printf("%I64d\n", last);
            } else {
                last = -LLINF;
                for(int i = 0, l = 1; i < seg.size(); ++i) {
                    int r = seg[i].first - 1;
                    if(l <= r) {
                        last = max(last, T.query3(l, r, 0, 1, n, root[p]));
                    }
                    l = seg[i].second + 1;
                }
                printf("%I64d\n", last);
            }
        }
    }
    return 0;
}
```