---
title: ZOJ 3932 Handshakes（逆向思维）
categories:
  - 思维
  - 逆向思维
  - 
tags:
  - 
  - 
date: 2016-04-10 23:37:10
toc: 
---
题意：
>$有一间教室，N\le 10^5依次来到这间教室，每个人来的时候要跟里面的所有人握手$
$现在给定每个人进来那一次握了多少次手$
$求每个人最多握多少次手，输出那个最大值$

<!-- more -->

分析：
>$显然最大的情况就是后面的人全是它的朋友$
$如果后面某个人握手了，就当作是前面的人也握了$
$所以倒着搞一遍就好了$
$时间复杂度O(n)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-10
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

int n, a[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);
        int ans = 0, cnt = 0;
        for(int i = n; i; --i) {
            ans = max(ans, a[i] + cnt);
            cnt += a[i] > 0;
        }
        printf("%d\n", ans);
    }
    return 0;
}

```