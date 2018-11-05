---
title: HDU 4125 Moles（nlogn建立二叉搜索树、kmp）
categories:
  - 数据结构
  - 二叉搜索树
  - 
tags:
  - 
  - 
date: 2016-05-02 02:14:10
toc: 
---
题意：
>$N\le 6\times 10^5，给定1\sim N的序列，按照这个顺序建立一颗二叉搜索树$
$奇数是1，偶数是0，先序遍历这颗二叉搜索树生成1个01的欧拉序列$
$查找T串可重叠的出现了几次，|T|\le 7000$
<!-- more -->

分析：
>$不学是要还的。。$
$这个数据范围只能nlogn建立BST了，set动态维护一下插入的节点$
$每次查找比当前x大的的最小点r，以及比x小的最大点l$
$分别尝试r的左儿子，以及l的右儿子能不能插入就可以了$
$然后先序遍历一遍，C++开栈一发，跑个kmp就ok了$
$时间复杂度O(nlogn)$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-03
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
const int N = 6e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int rt, ls[N], rs[N];
char s[N << 1], t[7010];

void dfs(int rt) {
    s[n++] = (rt & 1) + '0';
    if(ls[rt]) {
        dfs(ls[rt]);
        s[n++] = (rt & 1) + '0';
    }
    if(rs[rt]) {
        dfs(rs[rt]);
        s[n++] = (rt & 1) + '0';
    }
}

int kmp() {
    m = strlen(t);
    vector<int> nxt(m + 1);
    nxt[0] = -1;
    for(int i = 0, j = -1; i < m;) {
        if(j == -1 || t[i] == t[j]) nxt[++i] = ++j;
        else j = nxt[j];
    }

    int ret = 0;
    for(int i = 0, j = 0; i < n;) {
        if(j == -1 || s[i] == t[j]) ++i, ++j;
        else j = nxt[j];
        if(j == m) {
            ++ret;
            j = nxt[j];
        }
    }
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int T; scanf("%d", &T);
    while(T--) {
        scanf("%d", &n);
        memset(ls, 0, sizeof ls);
        memset(rs, 0, sizeof rs);

        set<int> st;
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            if(!st.size()) rt = x;
            else {
                auto it = st.lower_bound(x);
                if(it == st.end()) rs[*--it] = x;
                else {
                    if(!ls[*it]) ls[*it] = x;
                    else rs[*--it] = x;
                }
            }
            st.insert(x);
        }
        scanf("%s", t);

        n = 0;
        dfs(rt);
        s[n] = 0;

        static int kase = 0;
        printf("Case #%d: %d\n", ++kase, kmp());
    }
    return 0;
}
```
