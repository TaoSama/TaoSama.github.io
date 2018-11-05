---
title: Educational Codeforces Round 11 C. Hard Process（two pointers）
categories:
  - 技巧
  - two pointers
  - 
tags:
  - two pointers
  - 
date: 2016-04-10 23:00:10
toc: 
---
题意：
>$N\le 3\times 10^5的序列，A_i = 0 或者1$
$k\le N次操作将0变成1，求最长的连续1序列长度，并打印方案$

<!-- more -->

分析：
>$two\ pointers经典题啦，记录下比较好的写法$
$时间复杂度O(n)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-08
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

int n, k, a[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &k) == 2) {
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);

        int cnt = 0;
        pair<int, int> ans = {0, 0}; //sum, l
        for(int l = 1, r = 1; l <= n; ++l) {
            while(r <= n && cnt + !a[r] <= k) cnt += !a[r++];
            ans = max(ans, {r - l, l});
            cnt -= !a[l];
        }
        printf("%d\n", ans.first);
        for(int i = ans.second; ans.first--; ++i) a[i] = 1;
        for(int i = 1; i <= n; ++i) printf("%d%c", a[i], " \n"[i == n]);
    }
    return 0;
}
```