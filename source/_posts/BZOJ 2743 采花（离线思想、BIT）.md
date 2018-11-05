---
title: BZOJ 2743 采花（离线思想、BIT）
categories:
  - 思维
  - 离线思想
  - 
tags:
  - BIT
  - 离线
date: 2016-03-23 05:20:10
toc:
---
题意：
>$N，C，Q\le 10^6，C\le N，给定一个N大小序列，A_i\le C，Q次询问$
$每次询问[L,R]区间有多少个出现至少2次的不同的整数$

<!-- more -->
分析：
>$经典离线套路题了，询问右端点排序$
$从左往右扫描每个数A_i（相当于固定了右端点）$
$维护A_i出现的前一个位置为last_{i}，用BIT维护，只要单点更新last_{i}为1$
$但是这样出现2次以上的就会多算，我们需要减去前一个位置的前一个位置$
$也就是更新last_{last_i}为-1，这样扫描下去就不会多算了，每次只有当前的前一次出现的位置产生了贡献$
$此时所有右端点为i的询问[L,i]的答案就是，ans(L,i)=sum(L,i)$
$这里其实把BIT倒过来就可以完成了，向前更新，向后查询$
$时间复杂度为O(nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-23
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
const int N = 1e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

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

int n, c, q, a[N];
vector<pair<int, int> > qs[N];
int last[N], wh[N], ans[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d%d", &n, &c, &q);
    for(int i = 1; i <= n; ++i) {
        scanf("%d", a + i);
        last[i] = wh[a[i]];
        wh[a[i]] = i;
    }
    for(int i = 1; i <= q; ++i) {
        int l, r; scanf("%d%d", &l, &r);
        qs[r].push_back(make_pair(l, i));
    }

    bit.init(n);
    for(int i = 1; i <= n; ++i) {
        if(last[i]) bit.add(last[i], 1);
        if(last[last[i]]) bit.add(last[last[i]], -1);
        for(int j = 0; j < qs[i].size(); ++j) {
            pair<int, int>& q = qs[i][j];
            ans[q.second] = bit.sum(q.first);
        }
    }
    for(int i = 1; i <= q; ++i) printf("%d\n", ans[i]);
    return 0;
}

```