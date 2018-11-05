---
title: HDU 5769 Substring（后缀数组）
categories:
  - 字符串
  - SA/SAM
  - 
tags:
  - 后缀数组
  - 
  - 
date: 2016-08-05 19:32:10
toc: 
---

题意：
>$给定N\le 10^5的字符串，求包含一个特定字母X的不同子串的个数$

<!-- more -->
分析：
>$考虑不带特定字母的版本，对于每个后缀sa[i]的贡献是n-sa[i]-height[i]$
$由于必须包含X，所以对于每个sa[i]，记录一下之后最近的X的位置nxt[sa[i]]$
$那么贡献应该是n-nxt[sa[i]]，两者取min就好了$
$即n-max(nxt[sa[i]],\ sa[i]+height[i])为每个sa[i]的贡献$

代码:
```cpp
//
//  Created by TaoSama on 2016-08-01
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

int n;
char str[N], x[2];

int s[N], c[N], t[2][N];
int sa[N], rk[N], height[N];
// sa[0] is empty suffix, 1 ~ n
// height[i]:= lcp of sa[i-1] and sa[i]
void build(int n, int m) {
    int i, k, p, *x = t[0], *y = t[1];
    memset(c, 0, m << 2);
    for(i = 0; i < n; ++i) ++c[x[i] = s[i]];
    for(i = 1; i < m; ++i) c[i] += c[i - 1];
    for(i = n - 1; i >= 0; --i) sa[--c[x[i]]] = i;
    for(k = 1; k <= n; k <<= 1) {
        p = 0;
        for(i = n - k; i < n; ++i) y[p++] = i;
        for(i = 0; i < n; ++i) if(sa[i] >= k) y[p++] = sa[i] - k;
        memset(c, 0, m << 2);
        for(i = 0; i < n; ++i) ++c[x[y[i]]];
        for(i = 1; i < m; ++i) c[i] += c[i - 1];
        for(i = n - 1; i >= 0; --i) sa[--c[x[y[i]]]] = y[i];
        swap(x, y); p = 1; x[sa[0]] = 0;
        for(i = 1; i < n; ++i)
            x[sa[i]] = (y[sa[i - 1]] == y[sa[i]] &&
                        y[sa[i - 1] + k] == y[sa[i] + k]) ? p - 1 : p++;
        if(p >= n) break; m = p;
    }
    for(i = 0; i < n; ++i) rk[sa[i]] = i;
    for(i = 0, k = 0; i < n; ++i) {
        if(k) k--;
        if(!rk[i]) continue;
        int j = sa[rk[i] - 1];
        while(s[i + k] == s[j + k]) k++;
        height[rk[i]] = k;
    }
}

void see(int n) {
    for(int i = 0; i < n; ++i) printf("%d: %s\n", i, str + sa[i]);
    printf("sa: ");
    for(int i = 0; i < n; ++i) printf("%d ", sa[i]); puts("");
    printf("rk: ");
    for(int i = 0; i < n; ++i) printf("%d ", rk[i]); puts("");
    printf("ht: ");
    for(int i = 0; i < n; ++i) printf("%d ", height[i]); puts("");
}

int nxt[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%s%s", x, str);
        n = strlen(str);
        for(int i = 0; i < n; ++i) s[i] = str[i] - 'a' + 1;
        s[n] = 0;
        build(n + 1, 30);

//      see(n + 1);

        nxt[n] = n;
        for(int i = n - 1; ~i; --i) {
            nxt[i] = nxt[i + 1];
            if(str[i] == *x) nxt[i] = i;
        }

        long long ans = 0;
        for(int i = 1; i <= n; ++i) {
            ans += n - max(nxt[sa[i]], sa[i] + height[i]);
        }
        static int kase = 0;
        printf("Case #%d: %I64d\n", ++kase, ans);
    }

    return 0;
}
```