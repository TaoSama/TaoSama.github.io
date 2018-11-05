---
title: POJ 2566 Bound Found（two pointers）
categories:
  - 技巧
  - two pointers
  - 
tags:
  - two pointers
  - 
  - 
date: 2016-08-01 10:39:10
toc: 
---

题意：
>$N\le 10^5个数，|A_i|\le 10^4，现有K\le 100次询问$
$每次给定1个值x，求1个非空区间，使得|sum|=|\sum_{i=l}^r A_i|与x的差值尽量小$
$即使得||sum|-x|尽量小，输出这个|sum|，以及区间端点$

<!-- more -->
分析：
>$首先求个prefixSum_i，显然sum(l,\ r)=prefix_r-prefix_{l-1}$
$由于外面套了一个绝对值，|sum(l,\ r)|=|prefix_r-prefix_{l-1}|=|prefix_{l-1}-prefix_r|$
$那么prefixSum_i的顺序就无所谓了，窝萌可以排个序$
$排序后就有单调性了，就可以做很多事情了$
$比如窝萌就可以用two\ pointers来枚举所有区间来更新答案了$
$由于整个序列都可以作为答案，two\ pointers枚举的话为了方便可以丢个prefixSum_0进去$
$其他注意细节就好了$

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

int n, q;
pair<int, int> s[N];
int ans, diff, L, R;

void update(int l, int r, int x) {
    if(s[l].second == s[r].second) return; //empty range
    int sum = abs(s[r].first - s[l].first);
    int newDiff = abs(sum - x);
    if(newDiff < diff) {
        diff = newDiff;
        ans = sum;
        L = s[l].second;
        R = s[r].second;
        if(L > R) swap(L, R);
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &q) && (n || q)) {
        s[0] = make_pair(0, 0);
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            s[i].first = s[i - 1].first + x;
            s[i].second = i;
        }
        sort(s, s + n + 1);

        while(q--) {
            int x; scanf("%d", &x);
            diff = INF;

            int sum = 0;
            for(int l = 0, r = 0; l <= n; ++l) {
                sum = s[r].first - s[l].first;
                update(l, r, x);
                while(r < n && sum < x) {
                    ++r;
                    update(l, r, x);
                    sum = s[r].first - s[l].first;
                }
            }
            printf("%d %d %d\n", ans, L + 1, R);
        }
    }

    return 0;
}
```