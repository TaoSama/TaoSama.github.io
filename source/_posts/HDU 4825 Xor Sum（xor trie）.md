---
title: HDU 4825 Xor Sum（xor trie）
categories:
  - 技巧
  - xor
  - 
tags:
  - xor trie
  - 
date: 2016-05-01 15:00:10
toc: 
---
题意：
>$N\le 10^5个数，M\le 10^5次询问$
$每次询问给出一个整数K，从数列A_i中找出一个数S使得K\oplus S值最大$
<!-- more -->

分析：
>$xor\ trie基础题$
$将所有数插入01\ trie中，每次询问就在trie上贪心的找$
$由于要最大，那么就从高位到低位插入，这样查找的时候也从高位到低位$
$由于xor的性质，0\oplus1=1，显然优先找不同的，这样贪心的找就可以了$
$时间复杂度为O(nb)，b=log_2(max\{A_i\})$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-21
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

int n, q;

struct Trie {
    static const int M = 32 * 1e5 + 10, S = 2;
    int root, sz;
    int nxt[M][S], val[M];
    int newNode() {
        val[sz] = 0;
        memset(nxt[sz], -1, sizeof nxt[sz]);
        return sz++;
    }
    void init() {
        sz = 0;
        root = newNode();
    }
    void insert(int x) {
        int u = root;
        for(int i = 31; ~i; --i) {
            int c = x >> i & 1, &v = nxt[u][c];
            if(v == -1) v = newNode();
            u = v;
        }
        val[u] = x;
    }
    int query(int x) {
        int u = root;
        for(int i = 31; ~i; --i) {
            int c = x >> i & 1;
            if(~nxt[u][c ^ 1]) u = nxt[u][c ^ 1];
            else u = nxt[u][c];
        }
        return val[u];
    }
} trie;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &q);
        trie.init();
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            trie.insert(x);
        }

        static int kase = 0;
        printf("Case #%d:\n", ++kase);
        while(q--) {
            int x; scanf("%d", &x);
            printf("%d\n", trie.query(x));
        }
    }
    return 0;
}
```