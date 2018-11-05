---
title: Codeforces Round 345 (Div. 2) D. Image Preview（二分搜索）
categories:
  - 暴力
  - 搜索
  - 二/三分搜索
tags:
  - 二分搜索
  - 
date: 2016-03-08 20:39:10
toc:
---
题意：
>$n\le 5\times 10^5手机图片，每种图片可能是h的也可能是w的，w要观看的话就要有旋转花费b$
$观看一张图片需要1单位时间，手机图片显示是个环，滑动切换的花费是a$
$初始在第一张图片，切换到的图片必须要观看$
$给定T\le 10^9时间，问最多能看多少图片$

<!-- more -->
分析：
>$特判第一张图片，预处理出前缀观看时间，和后缀观看时间$
$向右枚举一次，然后二分倒着能看多少，两者加起来，同理向左枚举，二分顺着能看多少$
$注意细节和边界$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-07
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
const int N = 5e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, a, b, T;
int pre[N], suf[N];
char s[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d%d%d", &n, &a, &b, &T) == 4) {
        scanf("%s", s + 1);
        pre[1] = suf[n + 1] = 0;
        for(int i = 2; i <= n; ++i)
            pre[i] = pre[i - 1] + a + (s[i] == 'w' ? b : 0) + 1;
        for(int i = n; i; --i)
            suf[i] = suf[i + 1] + a + (s[i] == 'w' ? b : 0) + 1;
        reverse(suf + 1, suf + 1 + n);

        int first = (s[1] == 'w' ? b : 0) + 1;
        int ans = first <= T;
        for(int i = 2; i <= n; ++i) {
            if(pre[i] + first > T) break;
            int leave = T - pre[i] - first - (i - 1) * a;
            int can = upper_bound(suf + 1, suf + 1 + n, leave) - suf - 1;
            ans = max(ans, i + can);
        }
        for(int i = 1; i <= n; ++i) {
            if(suf[i] + first > T) break;
            int leave = T - suf[i] - first - i * a;
            int can = upper_bound(pre + 2, pre + 1 + n, leave) - pre - 2;
            ans = max(ans, i + can + 1);
        }
        ans = min(ans, n);
        printf("%d\n", ans);
    }
    return 0;
}

```