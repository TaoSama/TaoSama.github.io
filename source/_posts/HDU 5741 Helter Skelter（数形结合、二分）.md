---
title: HDU 5741 Helter Skelter（数形结合、二分）
categories:
  - 思维
  - 数形结合
  - 
tags:
  - 数形结合
  - 
  - 
date: 2016-08-05 18:56:10
toc: 
---

题意：
>$给定一个压缩过的0开头的01交替字符串，比如00110表示为\{ 2,\ 2,\ 1 \}$
$表示数字个数N\le 1000，x_i \le 10^6，Q\le 5\times 10^5次查询$
$每次给定a,\ b，问是否存在原串的子串0有a个，1有b个$

<!-- more -->
分析：
>$我们把所有的a和b\ n^2枚举一下，如果把这些(a,\ b)表示在二维平面上$
$手玩一下可以发现[0\ldots 0]这种子串表示出来的(a,\ b)必然在图形的下部$
$也可以发现[1\ldots 1]这种子串表示出来的(a,\ b)必然在图形的下部$
$还可以发现图形是连通且封闭的，现在窝萌可以把这个图形抠出来一个凸包$
$假设这些下部点是红点，上部点是绿点，其它点是蓝点$
$画出一个图，然后根据性质抠一下就好了$
$红点在竖线的底端，绿点在竖线的顶端$
![](http://7xru22.com1.z0.glb.clouddn.com/16-8-5/91108107.jpg)
$之后对于每个询问，只要二分一下a，得到b的下界和上界，即可知道是否合法$
$时间复杂度O(n^2+qlogn^2)$

代码:
```cpp
//
//  Created by TaoSama on 2016-07-25
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
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m, a[N];
char ans[N * N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    /*
       |            ___B
       |      G ___|   |
       |    ___|  _____|
       |___|     |
       |    _____| R
       |   |
    ___|___|_____________
      0|
    */

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        for(int i = 0; i < n; ++i) scanf("%d", a + i);

        vector<pair<int, int> > up, dw;
        for(int i = 0; i < n; ++i) {
            int zero = 0, one = 0;
            for(int j = i; j < n; ++j) {
                if(j % 2 == 0) zero += a[j];
                else one += a[j];

                //red points
                if(i % 2 == 0 && j % 2 == 0) dw.push_back({zero, one});
                //green points
                if(i % 2 == 1 && j % 2 == 1) up.push_back({zero, one});
            }
        }
        sort(dw.begin(), dw.end());
        sort(up.begin(), up.end());

        n = 0; //erase blues points, save lower red points
        for(int i = 0, j; i < dw.size(); i = j) {
            //jump vertical line
            for(j = i; j < dw.size() && dw[j].first == dw[i].first; ++j);
            //pop upper ones, save this lowest one
            while(n > 0 && dw[n - 1].second >= dw[i].second) --n;
            dw[n++] = dw[i];
        }
        dw.resize(n);

        n = 0; //erase blues points, save upper green points
        for(int i = 0, j; i < up.size(); i = j) {
            //go vertical line's top
            for(j = i; j < up.size() && up[j].first == up[i].first; ++j);
            //if upper
            if(!n || up[j - 1].second >= up[n - 1].second)
                up[n++] = up[j - 1];
        }
        up.resize(n);

        for(int i = 0; i < m; ++i) {
            int a, b; scanf("%d%d", &a, &b);
            auto st = lower_bound(dw.begin(), dw.end(), make_pair(a, -INF));
            auto ed = upper_bound(up.begin(), up.end(), make_pair(a, +INF));
            ans[i] = '0';
            if(st != dw.end()) {
                --ed;
                if(b >= st->second && b <= ed->second) ans[i] = '1';
            }
        }
        ans[m] = 0;
        puts(ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}

/*
dw:
3 0
up:
0 4

dw:
3 0
4 2
up:
0 2

dw:
3 0
4 2
6 3
7 7
8 11
10 12
up:
0 7
1 8
2 9
3 12
4 16
7 18
8 19
*/

```