---
title: CROC 2016 - Elimination Round E. Intellectual Inquiry（贪心、dp）
categories:
  - 动态规划
  - 线性dp
  - 
tags:
  - 贪心
  - 线性dp
date: 2016-03-22 00:10:10
toc:
---
题意：
>$N\le 10^6长度的字符串，给定字符集大小K\le26$
$现在后面添加M \le10^6个字符，使得新的字符串的不同子序列个数最多$
$输出这个个数，对10^9+7取模$

<!-- more -->
分析：
>$先看没有添加字符的情况，考虑f[i]:=前i个字符形成的不同子序列个数$
$如果这个字符之前没出现过，先累加之前的答案，然后添加这个字符，或者单独这个字符本身$
$即f[i]=2\cdot f[i-1]+1$
$如果这个字符之前出现过了，还是先累加之前的答案，然后添加这个字符$
$但是这个字符出现过了，就不算单独本身了，之前算过了$
$定义上一次出现的位置为pre，还要减去这个字符和pre-1这些字符重复的部分$
$即f[i]=2\cdot f[i-1]-f[pre-1]$
$对于后面新增加的字符，怎样才能让不同的字符最多，我们观察转移方程发现：$
$答案是成倍递增的，所以要让一开始的大，也就是说减去的少，基于这样的贪心$
$那么每次选择字符的时候都选上一次出现位置最早的$
$其实也就是给pre数组排个序，周期性的添加嘛，每个周期都是一样的$
$知道了添加的字符，继续做上面的dp就可以了$
$时间复杂度O(N+M)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-21
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
const int N = 2e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, k;
string s;
int pre[26], rk[26], f[N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    cin >> n >> k >> s;
    memset(pre, -1, sizeof pre);
    for(int i = 0; i < s.size(); ++i) pre[s[i] - 'a'] = i;
    for(int i = 0; i < k; ++i) rk[i] = i;
    sort(rk, rk + k, [](int x, int y) {
        return pre[x] < pre[y];
    });
    for(int i = 0; i < n; ++i) {
        int c = rk[i % k];
        s += char(c + 'a');
    }

    memset(pre, -1, sizeof pre);
    for(int i = 0; i < s.size(); ++i) {
        int c = s[i] - 'a';
        if(~pre[c]) f[i + 1] = (2 * f[i] % MOD - f[pre[c]] + MOD) % MOD;
        else f[i + 1] = (2 * f[i] + 1) % MOD;
        pre[c] = i;
    }
    int ans = (f[s.size()] + 1) % MOD;
    cout << ans << '\n';
    return 0;
}

```