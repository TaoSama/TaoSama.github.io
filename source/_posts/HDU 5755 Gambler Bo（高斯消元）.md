---
title: HDU 5755 Gambler Bo（高斯消元）
categories:
  - 数学
  - 高斯消元
  - 
tags:
  - 高斯消元
  - 
  - 
date: 2016-08-05 20:10:10
toc: 
---

题意：
>$给定N\times M的矩阵，N，M\le 30，每个格子里的数A_{ij}\in [0,\ 3)$
$每次可以按一个格子，使得这个格子+2，上下左右4个格子+1，数加完后会模3$
$输出1个可以使得所有格子都变成0的操作，保证数据有解$

<!-- more -->
分析：
>$对N\times M个格子建立方程，每个格子含有5个变元$
$高斯消元解方程，打印解即可$
$时间复杂度O((NM)^3)$

代码:
```cpp
//
//  Created by TaoSama on 2016-07-26
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
const int N = 30 * 30 + 10, INF = 0x3f3f3f3f, MOD = 3;

int n, m, val[N];
int a[N][N], ans[N];
bool isFreeX[N];

inline int inv(int x) {return x;}

void getAns(int n, int m, int r) {
    for(int i = r - 1; ~i; --i) {
        for(int j = 0; j < m; ++j) {
            if(!a[i][j]) continue;
            ans[j] = a[i][m];
            for(int k = j + 1; k < m; ++k) {
                ans[j] -= a[i][k] * ans[k];
                ans[j] %= MOD;
                if(ans[j] < 0) ans[j] += MOD;
            }
            ans[j] = ans[j] * inv(a[i][j]) % MOD;
            break;
        }
    }
}

int gauss(int n, int m) {
    for(int i = 0; i < m; ++i) isFreeX[i] = false;
    int r = 0, c = 0;
    for(; r < n && c < m; ++r, ++c) {
        int maxR = r;       //row transform
        for(int i = r + 1; i < n; ++i)
            if(abs(a[i][c]) > abs(a[maxR][c])) maxR = i;
        if(maxR != r) swap(a[maxR], a[r]);

        if(!a[r][c]) { --r; isFreeX[c] = true; continue;}

        //eliminate coefficient
        for(int i = r + 1; i < n; ++i) {
            if(a[i][c]) {
                int delta = a[i][c] * inv(a[r][c]);
                for(int j = c; j <= m; ++j) {
                    a[i][j] -= delta * a[r][j];
                    a[i][j] %= MOD;
                    if(a[i][j] < 0) a[i][j] += MOD;
                }
            }
        }
    }
    for(int i = r; i < n; i++) if(a[i][m]) return -1;

    getAns(n, m, r);

    //at last, r is rank, m - r is the number of freeX
    return r;
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
        memset(a, 0, sizeof a);
        for(int i = 0; i < n * m; ++i) {
            scanf("%d", val + i);
            int x = i / m, y = i % m;
            a[i][i] = 2;
            static int d[][2] = { -1, 0, 0, -1, 1, 0, 0, 1};
            for(int j = 0; j < 4; ++j) {
                int nx = x + d[j][0], ny = y + d[j][1];
                if(nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
                a[i][nx * m + ny] = 1;
            }
            a[i][n * m] = -val[i] + MOD;
        }

        gauss(n * m, n * m);

        vector<pair<int, int> > v;
        for(int i = 0; i < n * m; ++i) {
            int x = i / m, y = i % m;
//            printf("%d, %d : %d\n", x, y, ans[i]);
            while(ans[i]--) {
                v.push_back({x + 1, y + 1});
//                static int d[][2] = { -1, 0, 0, -1, 1, 0, 0, 1};
//                val[i] = (val[i] + 2) % MOD;
//                for(int j = 0; j < 4; ++j) {
//                    int nx = x + d[j][0], ny = y + d[j][1];
//                    if(nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
//                    val[nx * m + ny] ++;
//                    val[nx * m + ny] %= MOD;
//                }
            }
        }
//        if(count(val, val + n * m, 0) != n * m) puts("WA");
//        else puts("AC");

        printf("%d\n", v.size());
        for(auto& p : v) printf("%d %d\n", p.first, p.second);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```