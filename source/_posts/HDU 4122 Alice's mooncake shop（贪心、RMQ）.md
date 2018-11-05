---
title: HDU 4122 Alice's mooncake shop（贪心、RMQ）
categories:
  - 思维
  - 贪心
  - 
tags:
  - RMQ
  - 
date: 2016-05-02 23:56:10
toc: 
---
题意：
>$给定N\le 2500个订单，升序排列，保证合法$
$现在有个店开M\le 10^5小时，每小时做月饼的价格都不一样，不考虑做月饼的时间$
$每个月饼的保质期是t\le 10^5小时，每小时的花费为s\le 200$
$订单可以现做现卖，问如何制作才能满足所有订单，并使得总花费最小$
$求这个花费$
<!-- more -->

分析：
>$首先先解析日期都成小时$
$对于i小时的订单，显然应该取[i-t+1,\ i]的最小的那个花费$
$为了方便处理，假设都在最后一天买，显然某一天的花费应该a_i+(m-i)\times s$
$之后取min\{cost[i-t+1,\ i]\}，再减去多算的(m-i)\times s$
$然后就做完了$
$时间复杂度O(nlogm+m)$
$妈蛋，我样处理日期要考虑重复的情况，赛上T到死了。。。while与if的一字之差$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-02
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
#include <tuple>
#include <cassert>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 2e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int t, s;
struct Order {
    int y, m, d, h;
    int id;
    void read() {
        scanf("%d%d%d%d", &d, &y, &h, &r);
    }
    void see() {
        printf("(%d, %d, %d, %d)\n", y, m, d, h);
    }
    int r;
} o[2505];

void RE() {
    printf("%d\n", *((int*)0));
}

typedef long long LL;

LL a[N];

struct SparseTable {
    int n;
    LL dp[20][N];
    void init(int _n, LL* a) {
        n = _n;
        for(int i = 1; i <= n; ++i) dp[0][i] = a[i];
        for(int i = 1; (1 << i) <= n; ++i)
            for(int j = 1; j + (1 << i) - 1 <= n; ++j)
                dp[i][j] = min(dp[i - 1][j],
                               dp[i - 1][j + (1 << i - 1)]);
    }
    LL RMQ(int l, int r) {
        int k = 31 - __builtin_clz(r - l + 1);
        return min(dp[k][l], dp[k][r - (1 << k) + 1]);
    }
} st;


char mm[][10] = {"", "Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};
int mdays[] = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};

bool isLeap(int y) {
    return y % 4 == 0 && y % 100 || y % 400 == 0;
}

void go(int& y, int& m, int& d, int& h) {
    ++h;
    if(h >= 24) h = 0, ++d;
    int cur = mdays[m];
    if(m == 2 && isLeap(y)) ++cur;
    if(d > cur) d -= cur, ++m;
    if(m > 12) m -= 12, ++y;
}

int getTime(int m, int day, int year, int h) {
    int res = 0;
    for(int i = 2000; i < year; i++) {
        if(isLeap(i))res += 366 * 24;
        else res += 365 * 24;
    }
    for(int i = 1; i < m; i++) {
        res += mdays[i] * 24;
        if(isLeap(year) && i == 2)res += 24;
    }
    for(int i = 1; i < day; i++)res += 24;
    res += h + 1;
    return res;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);


    while(scanf("%d%d", &n, &m) == 2 && (n || m)) {
        for(int i = 1; i <= n; ++i) {
            char mon[10]; scanf("%s", mon);
            o[i].read();
            for(int j = 1; j <= 12; ++j) {
                if(!strcmp(mm[j], mon)) {
                    o[i].m = j;
                    break;
                }
            }

        }
        scanf("%d%d", &t, &s);

        int Y = 2000, M = 1, D = 1, H = 0;
        for(int j = 1, i = 1; ; ++j) {
//          o[i].see();
            while(make_tuple(Y, M, D, H) ==
                    make_tuple(o[i].y, o[i].m, o[i].d, o[i].h)) {
                o[i].id = j;
//                printf("%d = %d\n", i, j);
                ++i;
            }
            if(i == n + 1) break;
            go(Y, M, D, H);
        }


        for(int i = 1; i <= m; ++i) {
            scanf("%I64d", a + i);
            a[i] += 1LL * (m - i) * s;
        }
        st.init(m, a);

        LL ans = 0;
        for(int i = 1; i <= n; ++i) {
            int d = o[i].id;
            LL cur = st.RMQ(max(1, d - t + 1), d);
            cur -= 1LL * (m - d) * s;
            ans += cur * o[i].r;
        }
        printf("%I64d\n", ans);
    }
    return 0;
}
```
