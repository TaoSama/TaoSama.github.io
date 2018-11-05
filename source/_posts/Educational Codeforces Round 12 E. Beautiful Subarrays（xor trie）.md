---
title: Educational Codeforces Round 12 E. Beautiful Subarrays（xor trie）
categories:
  - 技巧
  - xor
  - 
tags:
  - xor trie
  - 
date: 2016-05-01 16:25:10
toc: 
---
题意：
>$N\le 10^6个点的数，xor(l,\ r)=A_l\oplus A_{l+1}\oplus\cdots\oplus A_r$
$求xor(l,\ r)\ge k的(l,\ r)对数$
<!-- more -->

分析：
>$xor\ trie贪心$
$我们发现这个是个连续异或和，经典的技巧$
$设prefix[i]:=A_1\oplus A_2\oplus\cdots\oplus A_i$
$那么显然我们可以得到xor(l,\ r)=prefix[r]\oplus prefix[l-1]$
$所以就枚举每个r，不断的把prefix[i]从高位到低位插入到trie里$
$然后贪心的去找大于k的，因为插入的时候把这个prefix[i]的所有节点的cnt都增加了$
$最后再把=k的加上就可以不重不漏的算完了$
$时间复杂度O(nb)$

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

struct Trie {
    static const int M = 32 * 1e6 + 10, S = 2;
    int root, sz;
    int nxt[M][S], cnt[M];
    int newNode() {
        cnt[sz] = 0;
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
            ++cnt[v];
            u = v;
        }
    }
    int query(int x, int k) {
        int u = root, ret = 0;
        for(int i = 31; ~i; --i) {
            int c = x >> i & 1;
            if(k >> i & 1) u = nxt[u][c ^ 1];
            else {
                ret += cnt[nxt[u][c ^ 1]]; //>
                u = nxt[u][c];
            }
            if(u == -1) return ret;
        }
        return ret + cnt[u]; //=
    }
} trie;

int n, k;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &k) == 2) {
        trie.init();
        trie.insert(0);
        int sum = 0;
        long long ans = 0;
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            sum ^= x;
            ans += trie.query(sum, k);
            trie.insert(sum);
        }
        printf("%I64d\n", ans);
    }
    return 0;
}
```