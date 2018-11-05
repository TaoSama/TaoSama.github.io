---
title: HDU 5801 Up Sky,Mr.Zhu（可持久化Trie）
categories:
  - 数据结构
  - 可持久化Trie
  - 
tags:
  - 可持久化Trie
  - 
  - 
date: 2016-08-13 21:00:10
toc: 
---

题意： 
>$给定N\le 10^5的字符串S，字符集大小为5，其中的回文子串长度<20$
$定义回文子串str[0\ldots n-1]的特征串为str[\lfloor n/ 2\rfloor\ldots n-1]$
$给定询问区间s[L\ldots R]里，特征串前缀为T的回文串有多少个，|T|\le 10$

<!-- more -->
分析：
>$区间询问就是裸的可持久化Trie$
$以每个字符s[i]为结尾建立每棵Trie，暴力把以s[i]结尾的全部特征串插进去$
$由于区间询问的时候要保证回文串也在这个范围内，这么做是有问题的$
$但是特征串长度在[1,\ 10]，所以可以暴力对每个长度建立可持久Trie$
$这样空间是10\times N\times 10\times 5，就炸了$
$所以折衷一下，离线询问，枚举每个特征串长度，每次重建可持久化Trie就行$
$为了方便处理奇偶回文，可以枚举回文长度，注意各种合法边界，然后就没啥了$
$时间复杂度O(n\times 10^2+q\times 10)$

```cpp
//
//  Created by TaoSama on 2016-08-13
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

typedef long long LL;

int root[N];
struct PersistentTrie {
    static const int M = N * 10, S = 5;
    int sz;
    struct Node {
        LL val;
        int nxt[S];
    } dat[M];
    void init() {
        sz = 0;
        memset(&dat[0], 0, sizeof dat[0]);
    }
    int newNode(int rt) {
        dat[++sz] = dat[rt];
        return sz;
    }
    void update(char* s, int n, int& rt) {
        rt = newNode(rt);
        int u = rt;
        for(int i = 0; i < n; ++i) {
            int c = s[i] - 'a';
            int& v = dat[u].nxt[c];
            v = newNode(v);
            ++dat[v].val;
            u = v;
        }
    }
    LL query(char* s, int rt) {
        int u = rt;
        for(int i = 0; s[i]; ++i) {
            int c = s[i] - 'a';
            u = dat[u].nxt[c];
        }
        return dat[u].val;
    }
} trie;

int q;
char s[N], t[N][15];
int l[N], r[N];
LL ans[N];

bool check(char* s, int len) {
    for(int i = 0; i < len / 2; ++i)
        if(s[-i] != s[-(len - i - 1)]) return false;
    return true;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%s%d", s + 1, &q) == 2) {
        int n = strlen(s + 1);
        for(int i = 1; i <= q; ++i) scanf("%d%d%s", l + i, r + i, t[i]);

        memset(ans, 0, sizeof ans);
        for(int len = 1; len < 20; ++len) {
            trie.init();
            for(int i = 1; i <= n; ++i) {
                root[i] = root[i - 1];
                if(i < len) continue;
                bool ok = check(s + i, len);
                if(!ok) continue;
                int l = i - (len - 1) / 2;
                trie.update(s + l, (len + 1) / 2, root[i]);
            }
            for(int i = 1; i <= q; ++i) {
                if(r[i] - l[i] + 1 < len) continue;
                ans[i] += trie.query(t[i], root[r[i]]) - trie.query(t[i], root[l[i] + len - 2]);
            }
        }
        for(int i = 1; i <= q; ++i)
            printf("%I64d\n", ans[i]);
    }
    return 0;
}
```