---
title: VK Cup 2016 - Round 1 E. Bear and Contribution（贪心）
categories:
  - 思维
  - 贪心
  - 
tags:
  - 
  - 
date: 2016-03-30 15:18:10
toc: 
---
题意：
>$N，K\le 2\times 10^5，给定N个数，现在要使其中至少K个数变得相同$
$b，c\le 1000，其中增加5的代价是b，增加1的代价是c$

<!-- more -->

分析：
>$显然的想法是都变成已有某个数比较优$
$但是b，c大小没给定，如果b很小，那么我们可以稍微通过+1变大我们这个数，同时其他的数+5$
$所以我们发现选择的数应该是a_i+j,\ j\in[0,5)$
$这样我们只要维护5个set，来动态维护k个数就好了$
$但是每次我们要O(1)计算出变成当前数的花费，其实只要把之前的数都变成[0,4)$
$之后再统一变成当前数就好辣，就可以O(1)计算了，注意负数哦$
$时间复杂度O(5nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-30
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

typedef long long LL;
int n, k, a[N];
LL b, c;

int get(int x) {return (x % 5 + 5) % 5;}
int count(int x) {return (x - get(x)) / 5;}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d%I64d%I64d", &n, &k, &b, &c);
    for(int i = 1; i <= n; ++i) scanf("%d", a + i);
    b = min(b, 5 * c);
    sort(a + 1, a + 1 + n);

    multiset<LL> s[5];
    LL ans = 1e18, cost[5] = {};

    for(int i = 1; i <= n; ++i) {
        for(int j = 0; j < 5; ++j) {
            int wh = get(a[i] + j);
            int times = count(a[i] + j);
            LL cur = j * c - times * b;

            s[wh].insert(cur);
            cost[wh] += cur;
            if(s[wh].size() > k) {
                cost[wh] -= *s[wh].rbegin();
                s[wh].erase(s[wh].find(*s[wh].rbegin()));
            }

            if(s[wh].size() == k) {
                LL tmp = cost[wh] + times * b * k;
                ans = min(ans, tmp);
            }
        }
    }
    printf("%I64d\n", ans);
    return 0;
}

```