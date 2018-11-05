---
title: Educational Codeforces Round 10 C. Foe Pairs（离线思想、贪心）
categories:
  - 思维
  - 离线思想
  - 
tags:
  - 贪心
  - 离线
date: 2016-03-28 21:50:10
toc: 
---
题意：
>$N，M\le 3\times 10^5，N个数，M个非法数对(a_i,b_i)$
$求不包含任何非法数对的区间个数$

<!-- more -->

分析：
>$ans=总区间数-非法的$
$经典离线套路了$
$非法的话，直接把非法数对转化成区间，对这些区间右端点排序$
$挨个枚举每个点作为右端点，对于每个点显然它的贡献是最大的那个l([1,l]都可以选做左端点)$
$累计贡献，就可以算出答案了$
$时间复杂度O(nlogn)$


代码：
```cpp
//
//  Created by TaoSama on 2016-03-25
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
const int N = 3e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int a[N], wh[N];
vector<int> qs[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &m) == 2) {
        for(int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
            wh[a[i]] = i;
        }
        for(int i = 1; i <= n; ++i) qs[i].clear();
        for(int i = 1; i <= m; ++i) {
            int x, y; scanf("%d%d", &x, &y);
            x = wh[x]; y = wh[y];
            if(x > y) swap(x, y);
            qs[y].push_back(x);
        }

        long long ans = 0;
        int l = 0;
        for(int i = 1; i <= n; ++i) {
            for(int x : qs[i]) l = max(l, x);
            ans += l;
        }
        ans = 1LL * n * (n + 1) / 2 - ans;
        printf("%I64d\n", ans);
    }
    return 0;
}

```