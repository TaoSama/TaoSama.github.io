---
title: HDU 5289	Assignment（two pointers）
categories:
  - 技巧
  - two pointers
  - 
tags:
  - two pointers
  - 
date: 2016-04-12 22:29:10
toc: 
---
题意：
>$N\le 10^5的序列，A_i\le 10^9$
$求连续区间中任意2个数差值不超过k的区间个数$

<!-- more -->

分析：
>$two\ pointers经典题辣，还是这种写法$
$对于每个左端点，找到最右的右端点统计贡献数即可$
$时间复杂度O(n)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-12
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, a[N], k;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &k);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);
        multiset<int> s;
        long long ans = 0;
        for(int l = 1, r = 1; l <= n; ++l) {
            while(r <= n) {
                if(s.size()) {
                    if(a[r] - *s.begin() >= k || *s.rbegin() - a[r] >= k)
                        break;
                }
                s.insert(a[r++]);
            }
            ans += r - l;
            s.erase(s.find(a[l]));
        }
        printf("%I64d\n", ans);
    }
    return 0;
}
```