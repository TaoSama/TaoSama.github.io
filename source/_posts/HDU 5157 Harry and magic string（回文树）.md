---
title: HDU 5157 Harry and magic string（回文树）
categories:
  - 字符串
  - Manacher/回文树
  - 
tags:
  - 回文树
  - 
date: 2016-04-11 02:08:10
toc: 
---
题意：
>$N\le 10^5的字符串S，设T_1、T_2为S的2个回文子串，并且T_1和T_2不相交$
$求(T_1,\ T_2)的对数有多少$

<!-- more -->

分析：
>$回文树预处理$
$pre[i]:=以i字符结尾的回文串的个数，suf[i]:=以i字符开头的回文串的个数$
$对中一个求前缀和，比如是suf[i]，然后答案ans=\sum_{i=1}^{n-1}pre[i]\times sum[i+1]$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-09
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

struct PalindromicTree {
    static const int M = 1e5 + 10, S = 26;
    int n, sz, last;
    int nxt[M][S], fail[M], len[M], s[M];
    int cnt[M], dif[M];

    int newNode(int l) {
        len[sz] = l;
        cnt[sz] = dif[sz] = 0;
        memset(nxt[sz], 0, sizeof nxt[sz]);
        return sz++;
    }

    void init() {
        sz = last = 0;
        newNode(0); newNode(-1);
        s[n = 0] = -1; // 无关字符减少特判
        fail[0] = 1;
    }

    int getFail(int u) {
        while(s[n - len[u] - 1] != s[n]) u = fail[u];
        return u;
    }

    int add(int c) {
        s[++n] = c;
        int u = getFail(last); // 找到这个回文串的匹配位置
        int& v = nxt[u][c];
        if(!v) {
            int cur = newNode(len[u] + 2);
            fail[cur] = nxt[getFail(fail[u])][c];
            v = cur;
            cnt[v] = cnt[fail[v]] + 1;
        }
        ++dif[v];
        last = v;
        return cnt[v];
    }

    void count() {
        //父亲累加儿子，如果 fail[v]=u ，则 u 一定是 v 的子回文串
        for(int i = sz - 1; ~i; --i) dif[fail[i]] += dif[i];
    }
} pt;

char s[N];
long long suf[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%s", s + 1) == 1) {
        int n = strlen(s + 1);
        pt.init();
        suf[n + 1] = 0;
        for(int i = n; i; --i) {
            int cnt = pt.add(s[i] - 'a');
            suf[i] = suf[i + 1] + cnt;
        }

        pt.init();
        long long ans = 0;
        for(int i = 1; i <= n; ++i)
            ans += pt.add(s[i] - 'a') * suf[i + 1];
        printf("%I64d\n", ans);
    }
    return 0;
}
```