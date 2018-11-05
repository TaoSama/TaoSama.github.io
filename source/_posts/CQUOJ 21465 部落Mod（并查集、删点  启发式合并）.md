---
title: CQUOJ 21465 部落Mod（并查集、删点 | 启发式合并）
categories:
  - 数据结构
  - 并查集
  - 
tags:
  - 并查集
  - 
date: 2016-05-09 02:05:10
toc: 
---
题意：
>$N\le 10^5个点，M\le 10^6个操作$
$U\ a\ b:合并a，b$
$D\ a:移除a所在的集合关系$
$S\ a:询问a所在的集合大小$
$F\ a\ b:询问a和b是否在同一集合$
<!-- more -->

分析：
>$暴力的做法就直接不路径压缩，只按秩合并保证树高，同时用set维护每棵子树的元素$
$对于D操作，直接暴力遍历根节点的set重建，看起来复杂度很高$
$实际均摊分析一下，暴力重建操作与树大小有关，树的大小与操作次数有关$
$相当于把重建操作均摊到每一次建立操作上了$
$按秩启发式合并加上set的复杂度，使得每次复杂度是log^2 n的$
$总时间复杂度为O(mlog^2n)$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-07
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

int n, m;
struct DSU {
    int n, p[N];
    set<int> s[N];
    void init(int _n) {
        n = _n;
        for(int i = 1; i <= n; ++i) resume(i);
    }
    void resume(int i) {
        p[i] = i;
        s[i].clear();
        s[i].insert(i);
    }
    int find(int x) {
        return p[x] == x ? x : find(p[x]);
    }
    void del(int x) {
        x = find(x);
        for(auto u : s[x]) {
            if(u == x) continue;
            resume(u);
        }
        resume(x);
    }

    void unite(int x, int y) {
        x = find(x), y = find(y);
        if(x == y) return;
        if(s[x].size() > s[y].size()) swap(x, y);
        p[x] = y;
        for(auto u : s[x]) s[y].insert(u);
    }
    int size(int x) {
        x = find(x);
        return s[x].size();
    }
    bool same(int x, int y) {
        x = find(x), y = find(y);
        return x == y;
    }
} dsu;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        dsu.init(n);
        while(m--) {
            char op[2];
            int x, y; scanf("%s%d", op, &x);
            if(*op == 'U') {
                scanf("%d", &y);
                dsu.unite(x, y);
            } else if(*op == 'D') dsu.del(x);
            else if(*op == 'S') printf("%d\n", dsu.size(x));
            else {
                scanf("%d", &y);
                puts(dsu.same(x, y) ? "Yes" : "No");
            }
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```

----
>$事实上有更加优美的做法，$[并查集删点](http://www.cppblog.com/menjitianya/archive/2015/12/10/212447.html)
$事实对于D操作，可以直接把这棵子树连到其它地方去，比如用0当作废弃点$
$对于重新合并的时候，直接rehash这个节点的id，这样之前的“废弃树”结构没变，这个点也新建了$
$非常完美的姿势做法，就是空间复杂度等于操作复杂度了，由于rehash这个原因$
$时间复杂度O(m\alpha(n))，空间复杂度O(m)$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-07
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
const int N = 2e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
struct DSU {
    int n, tim, id[N], p[N], sz[N];
    void init(int _n) {
        n = _n; tim = 0;
        for(int i = 0; i <= n; ++i) resume(i);
    }
    int resume(int i) {
        id[i] = tim;
        p[tim] = tim;
        sz[tim] = 1;
        return tim++;
    }
    int find(int x) {
        return p[x] = p[x] == x ? x : find(p[x]);
    }
    int findResume(int x) {
        int y = id[x];
        int fa = find(y);
        if(fa == 0) return resume(x);
        return fa;
    }
    void unite(int x, int y) {
        x = findResume(x), y = findResume(y);
        if(x == y) return;
        p[x] = y;
        sz[y] += sz[x];
    }
    void del(int x) {
        x = findResume(x);
        p[x] = 0;
    }
    int size(int x) {
        return sz[findResume(x)];
    }
    bool same(int x, int y) {
        return findResume(x) == findResume(y);
    }
} dsu;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        dsu.init(n);
        while(m--) {
            char op[2];
            int x, y; scanf("%s%d", op, &x);
            if(*op == 'U') {
                scanf("%d", &y);
                dsu.unite(x, y);
            } else if(*op == 'D') dsu.del(x);
            else if(*op == 'S') printf("%d\n", dsu.size(x));
            else {
                scanf("%d", &y);
                puts(dsu.same(x, y) ? "Yes" : "No");
            }
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```