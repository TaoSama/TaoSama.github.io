---
title: HDU 5536 Chip Factory（xor trie）
categories:
  - 技巧
  - xor
  - 
tags:
  - xor trie
  - 
date: 2016-05-01 15:02:10
toc: 
---
题意：
>$3\le N\le 10^3个数，求\max_{i,j,k} (s_i+s_j) \oplus s_k的最大值，i\ne j\ne k$
<!-- more -->

分析：
>$xor\ trie简单技巧$
$将所有数插入01\ trie中，n^2枚举s_i+s_j，由于要不同先把它俩删了$
$然后再去贪心的找s_k，不要忘记恢复就好$
$时间复杂度O(n^2b)，b=log_2(max\{A_i\})$

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

int n, a[N];

struct Trie {
    static const int M = 32 * 1e3 + 10, S = 2;
    int root, sz;
    int nxt[M][S], have[M];
    int newNode() {
        have[sz] = 0;
        memset(nxt[sz], -1, sizeof nxt[sz]);
        return sz++;
    }
    void init() {
        sz = 0;
        root = newNode();
    }
    void update(int x, int d) {
        int u = root;
        for(int i = 31; ~i; --i) {
            int c = x >> i & 1, &v = nxt[u][c];
            if(v == -1) v = newNode();
            have[v] += d;
            u = v;
        }
    }
    int query(int x) {
        int u = root, ret = 0;
        for(int i = 31; ~i; --i) {
            int c = x >> i & 1;
            if(have[nxt[u][c ^ 1]]) {
                ret |= 1 << i;
                u = nxt[u][c ^ 1];
            } else u = nxt[u][c];
        }
        return ret;
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
        scanf("%d", &n);
        trie.init();
        for(int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
            trie.update(a[i], 1);
        }
        int ans = 0;
        for(int i = 1; i <= n; ++i) {
            trie.update(a[i], -1);
            for(int j = i + 1; j <= n; ++j) {
                trie.update(a[j], -1);
                ans = max(ans, trie.query(a[i] + a[j]));
                trie.update(a[j], 1);
            }
            trie.update(a[i], 1);
        }
        printf("%d\n", ans);
    }
    return 0;
}
```