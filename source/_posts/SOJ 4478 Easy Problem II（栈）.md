---
title: SOJ 4478 Easy Problem II（栈）
categories:
  - 数据结构
  - 
  - 
tags:
  - 
  - 
date: 2016-04-11 01:14:10
toc: 
---
题意：
>$N\le 10^5，1-N的数按顺序入栈，现给定出栈序列，问是否合法$

<!-- more -->

分析：
>$栈模拟一下就好了$
$时间复杂度O(n)$

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
#include <stack>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        stack<int> s;
        int cnt = 0, ok = 1;
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            while(s.empty() || x != s.top()) {
                if(cnt == n) {
                    ok = false;
                    break;
                }
                s.push(++cnt);
            }
            s.pop();
        }
        puts(ok ? "Yes" : "No");
    }
    return 0;
}

```