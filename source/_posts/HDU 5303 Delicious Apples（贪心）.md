---
title: HDU 5303 Delicious Apples（贪心）
categories:
  - 思维
  - 贪心
  - 
tags:
  - 贪心
  - 
date: 2016-03-18 18:45:10
toc:
---
题意：
>$给定L\le 10^9的环形路，家在0号点，N\le 10^5颗苹果树，每棵有A_i个苹果，\sum A_i \le 10^5$
$现有一个K\le 10^5的篮子，求从家出发把所有的苹果装回家的最短路程$

<!-- more -->
分析：
>$首先可以发现，肯定是先把近的苹果先拿走，如果拿了远的再拿近的再拿远的路程会交叉，就不优了$
$其次再考虑会不会可能走一圈，发现是可以的，见下图：$
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-18/25212458.jpg)
$设起点到a的距离为l_1，到b的距离为l_2，且l_1,\ l_2 > {L\over 4}, A_a+A_b\le k$
$那么走一圈的路程是L，直接从最近的拿是2(l_1+l_2) > L，此时显然走一圈更优$
$更可以得出l_1,l_2\le {L\over 4}直接拿比较好$
$问题来了，可不可能走多圈呢，还是上面的图，看2圈的情况：$
$不过设A_a = x < k,\ A_b = k + y,\ 且A_a+A_b \le 2k$
$显然如果两边都是k的话，就不能绕圈了，所以可能走2圈一定是这样的$
$直接拿的话是2(l_1+2l_2)>{3L\over 2}，走2圈是2L，走1圈+先拿k是L+2l_2$
$显然走2圈最差，其次L+2l_2 - (2(l_1+2l_2))=L - (2(l_1+l_2)) < 0，走1圈+先拿k最优$
$显然更多圈的情况其实都可以规约到2圈的情况$
$由于苹果个数10^5，把苹果分开看，l[i]:=左边拿i个苹果的代价,r[i]同理$
$所以问题解决了，只要枚举最后一次2边拿的让它走1圈就好了$
$时间复杂度因为有排序所以是O(nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-18
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

int L, n, k;
typedef long long LL;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d%d", &L, &n, &k);
        vector<LL> l(1, 0), r(1, 0); //l[0] = r[0] = 0
        for(int i = 1; i <= n; ++i) {
            int x, y; scanf("%d%d", &x, &y);
            while(y--) {
                if(x << 1 < L) l.push_back(x);
                else r.push_back(L - x);
            }
        }
        sort(l.begin(), l.end());
        sort(r.begin(), r.end());

        for(int i = k; i < l.size(); ++i) l[i] += l[i - k];
        for(int i = k; i < r.size(); ++i) r[i] += r[i - k];

        LL ans = l.back() + r.back() << 1;
        for(int i = 0; i <= k; ++i) {
            int a = max(0, (int)l.size() - 1 - i);
            int b = max(0, (int)r.size() - 1 - (k - i));
            ans = min(ans, L + (l[a] + r[b] << 1));
        }
        printf("%I64d\n", ans);
    }
    return 0;
}

```