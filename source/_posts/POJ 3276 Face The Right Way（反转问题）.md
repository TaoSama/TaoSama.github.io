---
title: POJ 3276 Face The Right Way（反转问题）
categories:
  - 技巧
  - 反转问题
  - 
tags:
  - 反转问题
  - 
  - 
date: 2016-08-01 11:07:10
toc: 
---

题意：
>$N\le 5000个奶牛，有初始朝向B/F$
$现要通过每次反转K个奶牛操作，即B/F状态互换，使得所有奶牛最终都是F状态$
$求出最小反转次数M，以及这个最小M的下的最小K$

<!-- more -->
分析：
>$这类问题都一个苛刻的性质，即当前状态必然确定这个位置是否反转$
$本题考虑枚举K，对于每个K来求M$
$考虑使用f[i]:=记录f[i\sim i-k+1]这个区间是否被反转$
$那么所有能影响i这个位置的f[i]为f[i-k+1\sim i-1]$
$看这个和以及当前B/F状态即可知道i需不需要被反转$
$最后根据n-k+2\sim n以及对应影响的f[i]和即可知道是否合法$
$对于f[i]和，直接用一个变量维护即可$
$时间复杂度为O(n^2)$

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

int n, a[N];
int f[N];

int calc(int k) {
    memset(f, 0, sizeof f);
    int sum = 0, ret = 0;
    for(int i = 1; i + k - 1 <= n; ++i) {
        if(a[i] + sum & 1) {
            ++ret;
            f[i] = 1;
        }
        sum += f[i];
        if(i - k + 1 >= 1) sum -= f[i - k + 1];
    }
    for(int i = n - k + 2; i <= n; ++i) {
        if(a[i] + sum & 1) return INF;
        if(i - k + 1 >= 1) sum -= f[i - k + 1];
    }
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    scanf("%d", &n);
    for(int i = 1; i <= n; ++i) scanf("%d", a + i);
    for(int i = 1; i <= n; ++i) {
        char buf[2]; scanf("%s", buf);
        a[i] = *buf == 'B';
    }

    pair<int, int> ans = make_pair(INF, INF); // m k
    for(int i = 1; i <= n; ++i) {
        int o = calc(i);
        ans = min(ans, make_pair(o, i));
    }
    printf("%d %d\n", ans.second, ans.first);

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```