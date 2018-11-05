---
title: UVA 10771 Barbarian tribes（思维 | 线段树模拟约瑟夫环）
categories:
  - 数据结构
  - 线段树
  - 
tags:
  - 线段树
  - 约瑟夫环
  - 思维
  - 异或
date: 2016-03-28 14:22:10
toc: 
---
题意：
>$1\le N + M\le 2000，1\le K\le 1000，N+M个人围成环，前N为G，后M为K$
$现在每轮：$
$每K个各杀1个，杀2个，添加一个到第2个死的位置上，相同加G，不同加K$
$也就是说每轮死1个，N+M-1轮后只剩1个，问是G还是K$

<!-- more -->

分析：
>$线段树求K-th\ number的姿势直接暴力模拟$
$需要注意的是，1个死了，当前名次要-1，1个没死不动就好$
$时间复杂度为O((N+M)log(N+M))，跑了1.6s$
$也就是说这题起码有1W组case。。。$
$你特么不说一声，让我浪费时间写链表模拟。。O((N+M)K)直接T成sb了$

----
>$其实有O(1)解法，设G为0，K为1，GG和KK变G，GK和KG变K$
$这其实就是异或，然后异或满足交换律，显然有奇数个1答案才是K$
$所以一行代码：$
```cpp
puts(M & 1 ? "Keka" : "Gared");
```

线段树模拟代码：
```cpp
//
//  Created by TaoSama on 2016-03-26
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

int n, m, k;

int val[N << 2], sum[N << 2];

void build(int l, int r, int rt) {
    sum[rt] = r - l + 1;
    if(l == r) {
        val[rt] = l <= n ? 1 : 2;
        return;
    }
    int m = l + r >> 1;
    build(l, m, rt << 1);
    build(m + 1, r, rt << 1 | 1);
}

pair<int, int> query(int k, int l, int r, int rt) {
    --sum[rt];
    if(l == r) return make_pair(l, val[rt]);
    int m = l + r >> 1;
    if(sum[rt << 1] >= k) return query(k, l, m, rt << 1);
    return query(k - sum[rt << 1], m + 1, r, rt << 1 | 1);
}

void update(int o, int v, int l, int r, int rt) {
    if(l == r) {
        val[rt] = v;
        sum[rt] = 1;
        return;
    }
    int m = l + r >> 1;
    if(o <= m) update(o, v, l, m, rt << 1);
    else update(o, v, m + 1, r, rt << 1 | 1);
    sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d%d", &n, &m, &k) == 3 && (n || m || k)) {
        build(1, n + m, 1);
        n += m;
        int rk = k;
        for(int i = 1; i <= n - 1; ++i) {
            int lft = n - i;
            int last = query(rk, 1, n, 1).second;
            rk = (rk - 1 + k) % lft;
            if(rk == 0) rk = lft;

            pair<int, int> now = query(rk, 1, n, 1);
            int v = last == now.second ? 1 : 2;
            update(now.first, v, 1, n, 1);
            rk = (rk + k) % lft;
            if(rk == 0) rk = lft;
        }
        int ans = query(1, 1, n, 1).second;
        puts(ans == 1 ? "Gared" : "Keka");
    }
    return 0;
}

```