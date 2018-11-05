---
title: ZOJ 3930 Dice Notation（模拟）
categories:
  - 暴力
  - 小模拟
  - 
tags:
  - 模拟
  - 
date: 2016-04-10 23:29:10
toc: 
---
题意：
>$给定规则让你展开式子，就读3个小圆点就可以了$
$1.ndX\Rightarrow(\underbrace{[dX]\ +\ \cdots\ +\ [dX])}_n，dX\Rightarrow [dX]，+号左右各1个空格$
$2.给+-*/的左右各添加1个空格$
$3.最后添加，$"$\ =\ [Result]$"

<!-- more -->

分析：
>$有个坑，就是数字别解析，ndX的X也别解析$
$然后就能过了，赛上用BigInt逗比了，还是太懒，估计是把前导0给我去了$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-10
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

int n, idx;
char s[N], tmp[N];

char op[] = "+-*/";
bool isoperator(char c) {
    return c && strchr(op, c);
}

void display(int n, char* tmp) {
    if(n > 1) putchar('(');
    printf("[d%s]", tmp);
    for(int i = 1; i < n; ++i)
        printf(" + [d%s]", tmp);
    if(n > 1) putchar(')');
}

void dfs() {
    while(s[idx] && s[idx] != ')') {
        if(s[idx] == '(') {
            putchar('('); ++idx;
            dfs();
            putchar(')'); ++idx;
        }
        if(isdigit(s[idx])) {
            int l = 0, r = 0;
            while(isdigit(s[idx])) tmp[l++] = s[idx++]; tmp[l] = 0;
            if(s[idx] != 'd') {
                printf("%s", tmp); //just digit
                continue;
            }
            l = 0;
            for(int i = 0; tmp[i]; ++i) l = l * 10 + tmp[i] - '0';

            ++idx; //jump d
            while(isdigit(s[idx])) tmp[r++] = s[idx++];  tmp[r] = 0;
            display(l, tmp);
        }
        if(isoperator(s[idx])) printf(" %c ", s[idx++]);
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d ", &t);
    while(t--) {
        char buf[2005]; gets(buf);
        n = 0;
        for(int i = 0; buf[i]; ++i) {
            if(isspace(buf[i])) continue;
            if(buf[i] == 'd' && (!s[n] || s[n] && !isdigit(s[n]))) s[++n] = '1';
            s[++n] = buf[i];

        }
        s[n + 1] = 0;

        idx = 1;
        dfs();
        printf(" = [Result]\n");  //puts("");
    }
    return 0;
}

```