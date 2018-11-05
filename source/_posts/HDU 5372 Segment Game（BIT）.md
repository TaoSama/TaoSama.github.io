---
title: HDU 5372 Segment Game（BIT）
categories:
  - 数据结构
  - 树状数组
  - 
tags:
  - BIT
  - 
date: 2016-05-09 16:24:10
toc: 
---
题意：
>$N\le 2\times 10^5次操作$
$0\ l:如果是第i次添加操作，那么加入一条长度为i的线段，即[l,\ l+i]$
$1\ i:删除第i次添加操作添加的线段$
$输出每个添加操作的线段所完全包含的线段个数$
<!-- more -->

分析：
>$破题卡nlog^2n的cdq分治做法。。$
$然后赛上就卡死了，事实上每次添加的线段长度都更长这个条件很苛刻的$
![](http://7xru22.com1.z0.glb.clouddn.com/16-5-9/81258324.jpg)
$画个图发现就五种情况，那么答案就显然易见了$
$并且发现①②可以合并成左端点在A外的情况$
$③④可以合并成右端点在B外的情况$
$ans(⑤) = 总线段数-这两种情况的和$
$2个BIT分别维护下左右端点的数量即可$
$我自己脑补了题意。。特么的题意是删除第i次添加操作的，，这个添加二字$
$好吧，时间复杂度O(nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-09
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

struct BIT {
    int n, b[N];
    void init(int _n) {
        n = _n;
        memset(b, 0, sizeof b);
    }
    void add(int i, int v) {
        for(; i <= n; i += i & -i) b[i] += v;
    }
    int sum(int i) {
        int ret = 0;
        for(; i; i -= i & -i) ret += b[i];
        return ret;
    }
} bit[2];

int n, op[N], x[N], y[N];
int wh[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d", &n) == 1) {
        vector<int> xs[2];
        int addStp = 0;
        for(int i = 1; i <= n; ++i) {
            scanf("%d%d", op + i, x + i);
            if(op[i] == 1) continue;
            wh[++addStp] = i; y[i] = x[i] + addStp;
            xs[0].push_back(x[i]);
            xs[1].push_back(y[i]);
        }
        for(int i = 0; i < 2; ++i) {
            sort(xs[i].begin(), xs[i].end());
            xs[i].resize(unique(xs[i].begin(), xs[i].end()) - xs[i].begin());
            bit[i].init(xs[i].size());
        }

        static int kase = 0;
        printf("Case #%d:\n", ++kase);
        for(int i = 1; i <= n; ++i) {
            if(op[i] == 1) {  //delete
                int t = wh[x[i]];
                int l = lower_bound(xs[0].begin(), xs[0].end(), x[t]) - xs[0].begin() + 1;
                int r = lower_bound(xs[1].begin(), xs[1].end(), y[t]) - xs[1].begin() + 1;

                bit[0].add(l, -1);
                bit[1].add(r, -1);
            } else {
                int l = lower_bound(xs[0].begin(), xs[0].end(), x[i]) - xs[0].begin() + 1;
                int r = lower_bound(xs[1].begin(), xs[1].end(), y[i]) - xs[1].begin() + 1;

                int all = i - 1;
                int out = bit[0].sum(l - 1) + all - bit[1].sum(r);
                printf("%d\n", all - out);

                bit[0].add(l, 1);
                bit[1].add(r, 1);
            }
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```

---
>$当然这题如果没有这个严格的条件限制，cdq分治就可以做更一般的情况了$
$就转化成了右端点排序的离线sb题了$
$时间复杂度是O(nlog^2n)$

>$代码由于和之前那样没注意到那个题意的毒，就不贴了$