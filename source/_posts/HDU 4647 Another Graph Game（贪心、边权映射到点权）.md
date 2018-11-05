---
title: HDU 4647 Another Graph Game（贪心、边权映射到点权）
categories:
  - 思维
  - 贪心
  - 
tags:
  - 贪心
  - 
date: 2016-04-11 01:35:10
toc: 
---
题意：
>$N，M\le 10^5，N个点M条边，点权W_i、边权C_i \le 10^9 $
$2个人玩游戏轮流选点得到权值，选过的不能再选$
$规定如果一条边的2个端点都被同一个人选到，那么它获得边权$
$假设2个人采取最优策略，输出先手得分-后手得分$

<!-- more -->

分析：
>$考虑没有边的情况，那么显然2个人从大到小选$
$有边，那么就把边权均分到2个点上，如果同1个人选到刚好就加起来了，不同人就抵消了$
$符合题意，避免小数，把权加倍即可$
$时间复杂度O(nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-11
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
long long a[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &m) == 2) {
        for(int i = 1; i <= n; ++i) scanf("%I64d", a + i), a[i] <<= 1;
        for(int i = 1; i <= m; ++i) {
            int u, v, w; scanf("%d%d%d", &u, &v, &w);
            a[u] += w; a[v] += w;
        }
        sort(a + 1, a + 1 + n);
        long long ans = 0;
        for(int i = n; i; --i)
            if(i & 1) ans -= a[i];
            else ans += a[i];
        printf("%I64d\n", ans >> 1);
    }
    return 0;
}

```