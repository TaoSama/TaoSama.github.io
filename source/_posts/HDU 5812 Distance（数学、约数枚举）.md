---
title: HDU 5812 Distance（数学、约数枚举）
categories:
  - 数学
  - 
  - 
tags:
  - 约数枚举
  - 
  - 
date: 2016-08-10 19:30:10
toc: 
---

题意： 
>$维护1个集合S，d(x,\ y):=x经过多少次\ 乘/除素数\ 变成y$
$给定Q\le 10^5个操作，有三种类型$
$1\ x:插入x，若x存在则无视$
$2\ x:删除x，若x不存在则无视$
$3\ x:求min_{y\in S}\ \{\ d(x,\ y)\ \}$

<!-- more -->
分析：
>$令f(x)=x中质因子的个数，首先发现d(x,\ y)=f(x/gcd(a,\ b))+f(y/gcd(a,\ b))$
$所以我们可以枚举x的约数d，对于所有是d的倍数的y，求min\{\ f(y/d)\ \}$
$我们可以用multiset来维护(约数,\ 他的倍数的质因子个数)对$
$对于每次插入x，枚举x的约数d，插入(d,\ f(x/d))即可，删除同理$
$查询只需要枚举x的约数d，lower\ bound\ d对于的最小f(x/d)就好$
$时间复杂度O(q\times max\{d(A_i)\})$

```cpp
//
//  Created by TaoSama on 2016-08-09
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
const int N = 1e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int q;

vector<int> primes;
int g[N];
void gao() {
    for(int i = 1; i < N; ++i) g[i] = i;
    for(int i = 2; i < N; ++i) {
        if(g[i] == i) primes.push_back(i);
        for(int j = 0; j < primes.size() && i * primes[j] < N; ++j) {
            g[i * primes[j]] = primes[j];
            if(i % primes[j] == 0) break;
        }
    }
}

vector<int> divisors[N];
vector<pair<int, int> > factors[N];
int cnt[N];

int factorize(int x) {
    if(cnt[x]) return cnt[x];
    int y = x;
    auto& v = factors[y];
    while(x > 1) {
        int t = g[x];
        int e = 0;
        while(g[x] == t) ++e, x /= t;
        v.push_back({t, e});
        cnt[y] += e;
    }
    return cnt[y];
}

void dfs(int x, int k, int d) {
    if(k == factors[x].size()) {
        divisors[x].push_back(d);
        return;
    }

    dfs(x, k + 1, d);
    auto& p = factors[x][k];
    for(int i = 0; i < p.second; ++i) {
        d *= p.first;
        dfs(x, k + 1, d);
    }
}

void decomp(int x) {
    if(divisors[x].size()) return;
    factorize(x);
    dfs(x, 0, 1);
}

bool vis[N];
multiset<pair<int, int> > mf; //number of factors of the number's multiple

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//    freopen("C:\\Users\\TaoSama\\Desktop\\1004.in", "r", stdin);
//    freopen("C:\\Users\\TaoSama\\Desktop\\out.txt", "w", stdout);
#endif
    ios_base::sync_with_stdio(0);

    gao();

    while(scanf("%d", &q) == 1 && q) {
        static int kase = 0;
        printf("Case #%d:\n", ++kase);

        mf.clear();
        memset(vis, 0, sizeof vis);
        for(int i = 1; i <= q; ++i) {
            char op[2]; int x; scanf("%s%d", op, &x);
            if(*op == 'I') {
                if(vis[x]) continue;
                vis[x] = true;

                decomp(x);
                for(auto& div : divisors[x]) {
                    int num = factorize(x / div);
                    mf.insert({div, num});
                }
            } else if(*op == 'D') {
                if(!vis[x]) continue;
                vis[x] = false;

                decomp(x);
                for(auto& div : divisors[x]) {
                    int num = factorize(x / div);
                    mf.erase(mf.find({div, num}));
                }
            } else {
                if(!mf.size()) {
                    puts("-1");
                    continue;
                }

                decomp(x);
                int ans = INF;
                for(auto& div : divisors[x]) {
                    auto iter = mf.lower_bound({div, -INF});
                    if(iter != mf.end() && iter->first == div) {
                        int num = factorize(x / div);
                        ans = min(ans, num + iter->second);
                    }
                }
                printf("%d\n", ans);
            }
        }
    }

    return 0;
}
```