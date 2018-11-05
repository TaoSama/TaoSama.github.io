---
title: POJ 3977 Subset（折半搜索）
categories:
  - 技巧
  - 折半搜索
  - 
tags:
  - 折半搜索
  - 
  - 
date: 2016-08-01 12:07:10
toc: 
---

题意：
>$N\le 35个数的集合S，|A_i|\le 10^{15}$
$求1个非空子集S'，使得|\sum_{S'\in S}A_i|最小，同时使得元素个数最少$

<!-- more -->
分析：
>$数据范围都告诉你是折半搜索辣$
$显然预处理2^{N/2}个非空子集的答案先存起来排序(特么窝忘记排序了调一年)$
$之后枚举后2^{N-N/2}个非空子集，假设当前子集是sum,\ cnt$
$那么显然应该去枚举-sum，但是由于可能不存在$
$所以应该lower\_bound这个(-sum,\ -INF)一波$
$显然找到的就是大于等于的那个最小的，并且cnt也是最小的$
$之后这个lower\_bound-1就是小于的那个$
$但是这个时候由于重复的，找到cnt是最大的，不是可选解$
$显然取出这个值sum'，再去lower\_bound查找一下(sum',\ -INF)$
$就是要求的最小的cnt的那个辣$
$时间复杂度O(n2^nlog2^n)$

代码:
```cpp
//
//  Created by TaoSama on 2016-07-29
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
typedef long long LL;
LL a[40];

LL ABS(const LL& x) {
    if(x < 0) return -x;
    return x;
}

void checkAns(pair<LL, LL>& ans, pair<LL, LL> rhs, LL sum, LL cnt) {
    LL lftSum = rhs.first, lftCnt = rhs.second;
    ans = min(ans, make_pair(ABS(sum + lftSum), cnt + lftCnt));
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d", &n) == 1 && n) {
        for(int i = 0; i < n; ++i) scanf("%lld", a + i);

        pair<LL, LL> ans((LL)1e18, (LL)1e18);

        int half = n >> 1, lft = n - half;
        vector<pair<LL, LL> > v;
        for(int i = 1; i < 1 << half; ++i) {
            LL sum = 0, cnt = 0;
            for(int j = 0; j < half; ++j) {
                if(i >> j & 1) {
                    ++cnt;
                    sum += a[j];
                }
            }
            v.push_back(make_pair(sum, cnt));
            checkAns(ans, make_pair(0, 0), sum, cnt);
        }
        sort(v.begin(), v.end());

        for(int i = 1; i < 1 << lft; ++i) {
            LL sum = 0, cnt = 0;
            for(int j = 0; j < lft; ++j) {
                if(i >> j & 1) {
                    ++cnt;
                    sum += a[half + j];
                }
            }
            checkAns(ans, make_pair(0, 0), sum, cnt);

            int x = lower_bound(v.begin(), v.end(), make_pair(-sum,
                                (LL) - INF)) - v.begin();
            if(x != v.size()) checkAns(ans, v[x], sum, cnt);

            if(x) {
                --x;
                x = lower_bound(v.begin(), v.end(), make_pair(v[x].first,
                                (LL) - INF)) - v.begin();
                checkAns(ans, v[x], sum, cnt);
            }
        }
        printf("%lld %lld\n", ans.first, ans.second);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```