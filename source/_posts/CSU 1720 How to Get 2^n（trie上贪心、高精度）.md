---
title: CSU 1720 How to Get 2^n（trie上贪心、高精度）
categories:
  - 技巧
  - xor
  - 
tags:
  - trie上贪心
  - 
date: 2016-04-29 00:46:10
toc: 
---
题意：
>$给定N\le 10^5个数，1\le A_i\le 10^{30}(2^{100}>10^{30})$
$求A_i+A_j=2^x的(i,\ j)对数$
<!-- more -->

分析：
>$先把大整数转换成二进制，然后从低位到高位插到trie里$
$对于每个数A_i，先找到1个A_j，使得A_i+A_j为最小的那个2^x，从trie上找到A_j$
$对于之后比它大的2^y，A_j'的取值是A_j从x这个二进制位起都是1，trie累加之后的即可$
$感觉不是很好写，时间复杂度O(nb)，b=100$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-24
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
#include <bitset>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const int B = 101;

int n;

struct Type {
    int n;
    bitset<B> b;
    Type() {
        n = 0;
        b.reset();
    }
};

Type a[N];

void toBinary(Type& a, char* b) {
    a = Type();
    int n = strlen(b);
    for(int i = 0; i < n; ++i) b[i] -= '0';
    reverse(b, b + n);

    int cnt = 0; --n;
    while(~n) {  //use b[0], u fucking zz
        a.b[a.n++] = b[0] & 1; //mod
        int mod = 0;
        for(int i = n; ~i; --i) { //divide
            mod = mod * 10 + b[i];
            b[i] = mod >> 1;
            mod &= 1;
        }
        if(!b[n]) --n;
    }
}

Type subtract(Type& a, Type& b) {
    Type ret;
    bool lent = false;
    for(int i = 0; i < B; ++i) {
        int x = a.b[i];
        if(lent) {
            x -= 1;
            if(x < 0) x += 2;
            else lent = false;
        }
        x -= b.b[i];
        if(x < 0) {
            x += 2;
            lent = true;
        }
        ret.b[i] = x;
    }
    return ret;
}

char b[50];

int getBitLength(Type& b) {
    int bits = 0;
    for(int j = B; j; --j) {
        if(b.b[j - 1]) {
            bits = j;
            break;
        }
    }
    return bits;
}

struct Trie {
    static const int M = B * 1e5 + 10, S = 2;
    int nxt[M][S], val[M];
    int root, sz;
    int newNode() {
        val[sz] = 0;
        memset(nxt[sz], 0, sizeof nxt[sz]);
        return sz++;
    }
    void init() {
        sz = 0; newNode();
        root = newNode();
    }
    void update(Type& b, int d) {
        int u = root;
        for(int i = 0; i < b.n; ++i) {
            int c = b.b[i], &v = nxt[u][c];
            if(!v) v = newNode();
            u = v;
        }
        val[u] += d;
    }
    int query(Type& b, int bits) {
        int u = root;
        int ret = 0, n = getBitLength(b);
        for(int i = 0; i < bits; ++i) {
            int c = b.b[i];
            u = nxt[u][c];
            if(i == n - 1) ret += val[u];
        }
        for(int i = bits; i < B; ++i) {
            u = nxt[u][1];
            ret += val[u];
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

    int T; scanf("%d", &T);
    while(T--) {
        scanf("%d", &n);

        trie.init();
        for(int i = 1; i <= n; ++i) {
            scanf("%s", b);
            toBinary(a[i], b);
            trie.update(a[i], 1);
        }

        long long ans = 0;
        for(int i = 1; i <= n; ++i) {
            trie.update(a[i], -1);

            int bits = a[i].n;
            Type b; b.b[bits] = 1;
            Type c = subtract(b, a[i]);
            ans += trie.query(c, bits);

            trie.update(a[i], 1);
        }

        printf("%lld\n", ans >> 1);
    }
    return 0;
}
```