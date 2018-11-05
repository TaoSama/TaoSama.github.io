---
title: ZOJ 3938 Defuse the Bomb（模拟）
categories:
  - 暴力
  - 小模拟
  - 
tags:
  - 
  - 
date: 2016-04-25 22:35:10
toc: 
---
题意：
>$模拟Defuse\ the\ Bomb这个游戏的说明书啦$

<!-- more -->

分析：
>$写个函数来判断是位置还是值咯，代码量少，而且不会写错$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-23
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

//
void update(vector<pair<int, int> >& ans, int* a, bool position, int value) {
    if(position) ans.push_back({value, a[value]});
    else {
        for(int i = 1; i <= 4; ++i)
            if(a[i] == value) ans.push_back({i, a[i]});
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        vector<pair<int, int> > ans;
        ans.push_back({0, 0}); //garbage value
        for(int i = 1; i <= 5; ++i) {
            int d, a[5]; scanf("%d", &d);
            for(int j = 1; j <= 4; ++j) scanf("%d", a + j);
            if(i == 1) {
                if(d == 1) update(ans, a, 1, 2);
                else update(ans, a, 1, d);
            } else if(i == 2) {
                if(d == 1) update(ans, a, 0, 4);
                else if(d == 2 || d == 4)
                    update(ans, a, 1, ans[1].first);
                else ans.push_back({1, a[1]});
            } else if(i == 3) {
                if(d == 1) update(ans, a, 0, ans[2].second);
                else if(d == 2) update(ans, a, 0, ans[1].second);
                else if(d == 3) update(ans, a, 1, 3);
                else update(ans, a, 0, 4);
            } else if(i == 4) {
                if(d == 1) update(ans, a, 1, ans[1].first);
                else if(d == 2) update(ans, a, 1, 1);
                else update(ans, a, 1, ans[2].first);
            } else {
                if(d == 1) update(ans, a, 0, ans[1].second);
                else if(d == 2) update(ans, a, 0, ans[2].second);
                else if(d == 3) update(ans, a, 0, ans[4].second);
                else update(ans, a, 0, ans[3].second);
            }
        }
        for(int i = 1; i <= 5; ++i)
            printf("%d %d\n", ans[i].first, ans[i].second);
    }
    return 0;
}
```