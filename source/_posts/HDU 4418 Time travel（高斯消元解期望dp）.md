---
title: HDU 4418 Time travel（高斯消元解期望dp）
categories:
  - 数学
  - 高斯消元
  - 
tags:
  - 高斯消元
  - 期望dp
  - 
date: 2016-08-06 16:14:10
toc: 
---

题意：
>$给定N\le 100长度的路，这个路是来回走的$
$比如4个点，0, 1, 2, 3, 2, 1, 0, 1, ...$
$给定每次最大步数M，以及每个步数x\in[1,\ M]行走的概率p_x，保证\sum p_x=1$
$给定起点x，终点y，以及方向d，0正着1反着$
$求到达终点的期望步数$

<!-- more -->
分析：
>$测了下数据里并没有d=-1，这傻叉题d=-1根本不知道是干嘛的，无视就行了$
$来回走的周期cycle=2n-2，所以d=1把起点x映射一下x=(cycle-x)\%cycle$
$dp状态是f[i]:=i点到达终点的期望步数$
$f[i]=\sum p_j\times (f[(i+j)\%cycle]+j)$
$之后bfs一下，求出每个点的可达性，然后对于每个点建立方程$
$注意终点有2个就行，高斯消元一波就做完了$
$时间复杂度O(n^3)$

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
const int N = 200 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

const double EPS = 1e-8;
int sgn(double x) {
    return x < -EPS ? -1 : x > EPS;
}

int n, m, y, x, d;
double p[N];

double a[N][N], ans[N];
bool isFreeX[N];

void getAns(int n, int m, int r) {
//    memset(ans, 0, sizeof ans);  //not necessary
    for(int i = r - 1; ~i; --i) {
        for(int j = 0; j < m; ++j) {
            if(!sgn(a[i][j])) continue;
            ans[j] = a[i][m];
            for(int k = j + 1; k < m; ++k)
                ans[j] -= a[i][k] * ans[k];
            ans[j] = ans[j] / a[i][j];
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

        if(!sgn(a[r][c])) { --r; isFreeX[c] = true; continue;}

        //eliminate coefficient
        for(int i = r + 1; i < n; ++i) {
            if(sgn(a[i][c])) {
                double delta = a[i][c] / a[r][c];
                for(int j = c; j <= m; ++j)
                    a[i][j] -= delta * a[r][j];
            }
        }
    }
    for(int i = r; i < n; i++) if(sgn(a[i][m])) return -1;

    getAns(n, m, r);

    //at last, r is rank, m - r is the number of freeX
    return r;
}

bool can[N];
bool bfs(int sx) {
    memset(can, 0, sizeof can);
    queue<int> q; q.push(sx);
    can[sx] = true;
    bool ok = false;
    while(q.size()) {
        int u = q.front(); q.pop();
        if(u == y || u == (n - y) % n) ok = true;
        for(int i = 1; i <= m; ++i) {
            if(!sgn(p[i])) continue;
            int v = (u + i) % n;
            if(can[v]) continue;
            can[v] = true;
            q.push(v);
        }
    }
    return ok;
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
        scanf("%d%d%d%d%d", &n, &m, &y, &x, &d);
        for(int i = 1; i <= m; ++i) {
            int x; scanf("%d", &x);
            p[i] = x / 100.0;
        }
        n = 2 * n - 2; // 0,1,2,3,2,1 => 0,1,2,3,4,5

        //start coincides with destination
        if(n == 0) {puts("0.00"); continue;}

        if(d > 0) x = (n - x) % n;
        if(!bfs(x)) {puts("Impossible !"); continue;}

        memset(a, 0, sizeof a);
        //E_i = sum((E_j + j)*P_j) => E_i - sum(E_j*P_j) = sum(j*P_j)
        for(int i = 0; i < n; ++i) {
            if(i == y || i == (n - y) % n) {
                a[i][i] = 1;
                a[i][n] = 0; //E_destination = 0;
                continue;
            }
            a[i][i] = 1;
            for(int j = 1; j <= m; ++j) {
                if(!sgn(p[j])) continue;
                int x = (i + j) % n;
                if(!can[x]) continue;
                a[i][x] -= p[j];
                a[i][n] += j * p[j];
            }
        }

        gauss(n, n);
        printf("%.2f\n", ans[x]);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```