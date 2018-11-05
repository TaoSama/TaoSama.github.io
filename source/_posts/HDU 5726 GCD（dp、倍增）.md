---
title: HDU 5726 GCD（dp、倍增）
categories:
  - 动态规划
  - 
  - 
tags:
  - 倍增
  - gcd
date: 2016-07-24 19:27:10
toc: 
---

题意：
>$给定一个N\le 10^5个数，|A_i| \le 10^9，Q\le 10^5次询问$
$定义gcd(l,\ r)=gcd(a_l,\ a_{l+1},\ \cdots,\ a_r)$
$每次询问给定一个[l,\ r]，查询\forall_{1\le l'\le r'\le N}，gcd(l',\ r')=gcd(l,\ r)的(l',\ r')个数$

<!-- more -->
分析：
>`vector<pair<int, int> >`$\ f[i]:=以i结尾的区间，且gcd为first，个数为second$
$由于1个数有log级别的质因子个数，所以不同的gcd的个数也是log级别的$
$这样就可以通过dp预处理出全局的mp[g]:=gcd为g的区间个数$
$之后再通过倍增预处理出g[i][j]:=以i开始向右长度为2^j的gcd$
$对于每次询问就可以类似RMQ的做法O(1)求出gcd(l,\ r)了$
$总时间复杂度为O(nlog^2n)$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-20
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

typedef long long LL;

int n, a[N];
vector<pair<int, int> > f[2];
map<int, LL> mp;
int g[17][N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%d", a + i);

        mp.clear();

        int p = 0; f[p].clear();
        f[p].push_back({0, 1});
        for(int i = 1; i <= n; ++i) {
            f[!p].clear(); f[!p].push_back({0, 1});
            for(auto& u : f[p]) {
                int x, cnt; tie(x, cnt) = u;
                x = __gcd(x, a[i]);

                bool ok = true;
                for(auto& v : f[!p]) {
                    if(x == v.first) {
                        ok = false;
                        v.second += cnt;
                        break;
                    }
                }
                if(ok) f[!p].push_back({x, cnt});
//                printf("%d: %d %d\n", i, x, cnt);
                mp[x] += cnt; //global
            }
            p = !p;
        }

        for(int i = 1; i <= n; ++i) g[0][i] = a[i];

        for(int i = 1; 1 << i <= n; ++i)
            for(int j = 1; j <= n; ++j)
                g[i][j] = __gcd(g[i - 1][j], g[i - 1][j + (1 << i - 1)]);

        static int kase = 0;
        printf("Case #%d:\n", ++kase);

        int q; scanf("%d", &q);
        while(q--) {
            int l, r; scanf("%d%d", &l, &r);
            int k = 31 - __builtin_clz(r - l + 1);
            int gcd = __gcd(g[k][l], g[k][r - (1 << k) + 1]);
            printf("%d %I64d\n", gcd, mp[gcd]);
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}

```