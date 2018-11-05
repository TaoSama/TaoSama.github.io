---
title: HDU 5787 K-wolf Number（数位dp）
categories:
  - 动态规划
  - 数位dp
  - 
tags:
  - 数位dp
  - 
  - 
date: 2016-08-05 18:33:10
toc: 
---

题意：
>$求1\le L\le R\le 10^{18}范围内，每2\le K\le 5个数字都不同的数字有多少$

<!-- more -->
分析：
>$显然数位dp一下就做完了$
$f[i][pre][5]:=从高到低，填到第i位，且之前的数字是pre，pre已经有1\sim 4位数合法数字数$
$由于不能有前导0，所以开个first判一判，其他套个板子就好了$

代码:
```cpp
//
//  Created by TaoSama on 2016-08-02
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
const int N = 1e4 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long LL;
LL l, r, f[20][N][5];
int k;

const int ten[] = {1, 10, 100, 1000, 10000, 100000};
int get(int x, int i) {
    return x / ten[i] % 10;
}

int add(int x, int y) {
    return (x * 10 + y) % ten[k - 1];
}

int digit[20];

LL dfs(int i, int pre, int num, bool first, bool e) {
    if(!i) return 1;
    if(!e && ~f[i][pre][num]) return f[i][pre][num];
    LL ret = 0;
    int to = e ? digit[i] : 9;
    for(int d = 0; d <= to; ++d) {
        bool ok = true;
        for(int j = 0; j < min(k - 1, num) && ok; ++j)
            if(get(pre, j) == d) ok = false;
        if(!ok) continue;
        ret += dfs(i - 1, first && !d ? 0 : add(pre, d),
                   first && !d ? 0 : min(k - 1, num + 1),
                   first && !d, e && d == to);
    }
    return e ? ret : f[i][pre][num] = ret;
}

LL calc(LL x) {
    int cnt = 0;
    for(; x; x /= 10) digit[++cnt] = x % 10;
    return dfs(cnt, 0, 0, 1, 1);
}

bool judge(int x, int k) {
    int cnt = 0;
    for(; x; x /= 10) digit[++cnt] = x % 10;

    for(int i = 1; i <= cnt; ++i) {
        bool ok = true;
        for(int j = 1; j < k; ++j) {
            if(i - j >= 1 && digit[i] == digit[i - j]) {
                ok = false;
                break;
            }
        }
        if(!ok) return false;
    }
    return true;
}
int sum[1000005];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

//    for(k = 2; k <= 5; ++k) {
//        memset(f, -1, sizeof f);
//        for(int i = 1; i <= 10000; ++i) {
//            sum[i] = sum[i - 1] + judge(i, k);
//            if(sum[i]  +  1 != calc(i)) {
//                printf("%d: %d %I64d %d\n", i, sum[i] + 1, calc(i), k);
//                printf("WA\n");
//                break;
//            }
//        }
//    }

    while(scanf("%I64d%I64d%d", &l, &r, &k) == 3) {
        memset(f, -1, sizeof f);
        LL ans = calc(r) - calc(l - 1);
        printf("%I64d\n", ans);
    }

    return 0;
}
```