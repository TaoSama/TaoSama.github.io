---
title: Hihocode 1492 Parentheses Sequence（dp）
categories:
  - 动态规划
  - 
  - 
tags:
  - 
  - 
date: 2017-04-05 02:33:10
toc: 
---

题意：
$给定一个长度为N\le 10^3的括号串，可以插入括号$
$问变成合法的括号串的最短插入次数，和方法数$

<!-- more -->

分析：
$首先回顾一下如何表示一个合法的括号串$
$令'('=1，')'=-1，那么需要前缀和\ge 0，且和=0$
$那么显然我们有一个三方的dp$
$f[i][j][k]:=1\sim i，插入了j个括号，sum=k的方法数$
$再开个bool表示一下状态存不存在即可$
$这样就可以找到最少多少个，以及相应的方案数了$

$蓝儿三方是不能过的，思考一下，这其实是一个背包问题$
$背包的是sum，所以其实我们看成是一个图的问题，即n\times sum个节点的图$
$那么插入的括号的个数自然变成了最短路，方法数自然变成了最短路方案数$
$然后dp就变成了f[i][k]:=1\sim i，sum=k的最少插入括号数和方法数$
$最后减去原来的括号个数即可$
$这个背包化图的思路可以做一下上次CF\ 407的C：$[题目链接](http://codeforces.com/problemset/problem/788/C)

代码：
```cpp
//
//  Created by TaoSama on 2017-04-04
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
#define pr(x) cerr << #x << " = " << x << "  "
#define prln(x) cerr << #x << " = " << x << endl
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
char s[N];
int f[N][N], g[N][N];

void add(int& x, int y) {
    if((x += y) >= MOD) x -= MOD;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%s", s + 1);
    n = strlen(s + 1);

    queue<pair<int, int>> q;
    memset(f, -1, sizeof f);
    f[0][0] = 0, g[0][0] = 1;
    q.push({0, 0});
    while(q.size()) {
        int i, j; tie(i, j) = q.front(); q.pop();
        for(int k = -1; k <= 1; k += 2) {
            char c = k == 1 ? '(' : ')';
            int ni = min(n, i + (s[i + 1] == c));
            int nj = j + k;
            if(nj >= 0 && nj <= n) {
                if(f[ni][nj] == -1) {
                    f[ni][nj] = f[i][j] + 1;
                    g[ni][nj] = g[i][j];
                    q.push({ni, nj});
                } else if(f[ni][nj] == f[i][j] + 1) {
                    add(g[ni][nj], g[i][j]);
                }
            }
        }
    }
    printf("%d %d\n", f[n][0] - n, g[n][0]);

    return 0;
}
```
