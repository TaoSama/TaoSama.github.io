---
title: HDU 3364 Lanterns（线性基）
categories:
  - 数学
  - 高斯消元
  - 
tags:
  - 高斯消元
  - 线性基
  - 
date: 2016-08-06 17:39:10
toc: 
---

题意：
>$N\le 50个灯，M\le 50个开关，每个开关控制一些灯$
$Q\le 1000次询问，给定N个灯的状态，查询方法数$

<!-- more -->
分析：
>$开关问题，直接暴力复杂度O(qn^2m)就算不压位都跑得飞快$
$当然反过来，对每个开关构建矩阵，高斯消元求出线性基$
$之后对于每个状态只要判断能否组合成就可以了$
$压位之后，时间复杂度O({ n^2m \over 64 }+qn)$

代码一（线性基）:
```cpp
//
//  Created by TaoSama on 2016-07-28
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
const int N = 50 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
typedef long long LL;
LL a[N];

int xorGauss(int n, int m) {
    int r = 0, c = 0;
    for(; r < n && c < m; ++r, ++c) {
        int p = r;
        for(; p < n; ++p) if(a[p] >> c & 1) break;
        if(p == n) {--r; continue;}
        swap(a[p], a[r]);

        for(int i = 0; i < n; ++i) {
            if(i != r) {
                if(a[i] >> c & 1) a[i] ^= a[r];
            }
        }
    }
    return r;
}

bool check(LL x, LL rnk) {
    LL mix = 0;
    int r = 0, c = 0;
    for(; r < rnk && c < m; ++c) {
        bool have = mix >> c & 1;
        bool need = x >> c & 1;
        if(have == need) continue;
        bool ok = false;
        if(need) {
            while(r < rnk && !ok) {
                if(a[r] >> c & 1) {
                    mix ^= a[r];
                    ok = true;
                }
                ++r;
            }
        }
        if(!ok) return false;
    }
    return mix == x;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &m, &n);
        for(int i = 0; i < n; ++i) {
            a[i] = 0;
            int cnt = 0; scanf("%d", &cnt);
            while(cnt--) {
                int x; scanf("%d", &x);
                a[i] |= 1LL << x - 1;
            }
        }
        int r = xorGauss(n, m);

        static int kase = 0;
        printf("Case %d:\n", ++kase);
        int q; scanf("%d", &q);
        while(q--) {
            LL target = 0;
            for(int i = 0; i < m; ++i) {
                int x; scanf("%d", &x);
                if(x) target |= 1LL << i;
            }
            LL ans = check(target, r) ? 1LL << n - r : 0;
            printf("%I64d\n", ans);
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```

代码二（暴力）：
```cpp
//
//  Created by TaoSama on 2016-07-28
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
const int N = 50 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int a[N][N], b[N][N];

int xorGauss(int n, int m) {
    int r = 0, c = 0;
    for(; r < n && c < m; ++r, ++c) {
        int p = r;
        for(; p < n; ++p) if(a[p][c]) break;
        if(p == n) {--r; continue;}
        swap(a[p], a[r]);

        for(int i = 0; i < n; ++i) {
            if(i != r && a[i][c]) {
                for(int j = c; j <= m; ++j)
                    a[i][j] ^= a[r][j];
            }
        }
    }
    for(int i = r; i < n; ++i) if(a[i][m]) return -1;

    return m - r;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        memset(b, 0, sizeof b);
        for(int j = 0; j < m; ++j) {
            int k; scanf("%d", &k);
            while(k--) {
                int x; scanf("%d", &x);
                --x;
                b[x][j] = 1;
            }
        }

        static int kase = 0;
        printf("Case %d:\n", ++kase);
        int q; scanf("%d", &q);
        while(q--) {
            memcpy(a, b, sizeof b);
            for(int i = 0; i < n; ++i) scanf("%d", a[i] + m);
            int freeX = xorGauss(n, m);
            long long tot = ~freeX ? 1LL << freeX : 0;
            printf("%I64d\n", tot);
        }
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```