---
title: Codeforces Round 349 (Div. 2) C. Reberland Linguistics（dp）
categories:
  - 动态规划
  - 线性dp
  - 
tags:
  - 线性dp
  - 
date: 2016-04-30 03:23:10
toc: 
---
题意：
>$给定5\le |L|\le 10^4长度的字符串，现划分这个字符串$
$使得第一个子串长度\ge 5，后面的所有子串长度为2或3，并且相邻的2个子串不能相同
$
$字典序输出所有划分方案中的长度为2或3的子串$
<!-- more -->

分析：
>$- - 当然是dp啦，f[i][0]:=前i个字符，划分长度2的合法子串，s[i-1,i]$
$同理，f[i][1]:=前i个字符，划分长度3的合法子串，s[i-2,i-1,i]$
$这个dp是顺着的，我们发现没法保证中途的合法性，不能存储这些子串$
$但是倒着dp就可以了，就都是合法的了，就可以边搞边存了$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-30
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

string s;
int f[N][2];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(cin >> s) {
        memset(f, 0, sizeof f);
        s = ' ' + s;
        int sz = s.size() - 1;
        set<string> ss;
        for(int i = sz, j = 1; i; --i, ++j) {
            if(j < 2 || i <= 5) continue;
            if(j == 2) {
                ss.insert(s.substr(i, 2));
                f[i][0] = 1;
            } else if(j == 3) {
                ss.insert(s.substr(i, 3));
                f[i][1] = 1;
            } else {
                if(f[i + 2][0]) {
                    string b = s.substr(i + 2, 2);
                    string c = s.substr(i, 2);
                    if(b != c) {
                        ss.insert(c);
                        f[i][0] = 1;
                    }
                }
                if(f[i + 2][1]) {
                    string c = s.substr(i, 2);
                    ss.insert(c);
                    f[i][0] = 1;
                }
                if(f[i + 3][0]) {
                    string c = s.substr(i, 3);
                    ss.insert(c);
                    f[i][1] = 1;
                }
                if(f[i + 3][1]) {
                    string b = s.substr(i + 3, 3);
                    string c = s.substr(i, 3);
                    if(b != c) {
                        ss.insert(c);
                        f[i][1] = 1;
                    }
                }
            }
        }

        cout << ss.size() << '\n';
        for(auto s : ss) cout << s << '\n';
    }
    return 0;
}
```