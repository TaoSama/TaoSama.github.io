---
title: HDU 5654 xiaoxin and his watermelon candy（离线思想、BIT）
categories:
  - 思维
  - 离线思想
  - 
tags:
  - 离线
  - BIT
date: 2016-04-07 21:12:10
toc: 
---
题意：
>$N，Q\le 2\times 10^5，N个数的序列，A_i\in[0,10^9]$
$定义奇怪的三元组为(i,j,k)，i,j,k连续，且A_i\le A_j\le A_k$
$询问区间[l,r]中不同的奇怪三元组的个数$

<!-- more -->

分析：
>$离线sb套路题，询问右端点排序$
$从左往右扫描A_i，一旦找到合法的三元组$
$BIT维护出现的个数，更新要在三元组的第1个位置$
$就把之前的位置-1，现在的位置+1$
$现在所有以i为右端点的询问的答案，只要查询sum(L,i)就可以了$
$即ans(L,i)=sum(L,i)，这里BIT倒过来用就可以了，向前更新，向后查询$
$当然你要主席树强制在线也是兹磁的，反正复杂度一样的$
$时间复杂度为O(nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-07
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
const int N = 2e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, q, a[N];
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

int ans[N];
vector<pair<int, int> > qs[N];
typedef tuple<int, int, int> tripe;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);
        scanf("%d", &q);
        for(int i = 1; i <= n; ++i) qs[i].clear();
        for(int i = 1; i <= q; ++i) {
            int l, r; scanf("%d%d", &l, &r);
            qs[r].push_back({l, i});
        }

        bit.init(n);
        int cnt = 0;
        map<tripe, int> mp;
        for(int i = 1; i <= n; ++i) {
            cnt = a[i] >= a[i - 1] ? cnt + 1 : 1;
            if(cnt > 2) {
                tripe cur = tripe(a[i - 2], a[i - 1], a[i]);
                if(mp.count(cur)) bit.add(mp[cur], -1);
                mp[cur] = i - 2;
                bit.add(i - 2, 1);
            }
            for(auto& q : qs[i]) ans[q.second] = bit.sum(q.first);
        }
        for(int i = 1; i <= q; ++i) printf("%d\n", ans[i]);
    }
    return 0;
}
```