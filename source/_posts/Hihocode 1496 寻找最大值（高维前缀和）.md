---
title: Hihocode 1496 寻找最大值（高维前缀和）
categories:
  - 数学
  - FFT/NTT/FWT
  - 
tags:
  - 高维前缀和
  - 
date: 2017-04-05 02:44:10
toc: 
---

题意：
$给定一个长度为N\le 10^5的数列，1\le A_i\le 2^{20}$
$求\displaystyle\max_{i,\ j,\ i\neq j}\{A_i\times A_j\times (A_i\& A_j)\}的值$

<!-- more -->

分析：
$这个题显然不能枚举A_i和A_j，考虑枚举A_i\& A_j的值$
$令z=A_i\&A_j，事实上条件可以不用这么严格$
$只要找到z的超集Z'即可，即z\supset Z'$
$假如存在Z'中的两个元素z'_1，z'_2分别是最大值和次大值$
$满足z'_1\& z'_2=z，那么这个必然是z的答案$
$假如z'_1\& z'_2>z，那么令z'_1\& z'_2=y$
$那么这个答案必然在y处更新，且答案更大，也就是说不会影响答案$
$所以问题就变成了如何求z的超集的最大值和次大值了$

$这是一个高位前缀和问题，即20维空间，每一维大小是2$
$所以把1的答案都加到0上即可，因为1是0的超集$
$同理子集问题也可以这么搞，不过是0加到1上，0是1的子集$
$具体看代码，注意循环顺序，感受一下前缀和$

代码：
```cpp
//
//  Created by TaoSama on 2017-04-04
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
#define pr(x) cerr << #x << " = " << x << "  "
#define prln(x) cerr << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
pair<int, int> f[1 << 20];

pair<int, int> operator+(pair<int, int> A, pair<int, int> B) {
    pair<int, int> ret = A;
    if(B.first > ret.first) {
        swap(ret.first, ret.second);
        ret.first = B.first;
    } else if(B.second > ret.second) ret.second = B.second;
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 0; i < 1 << 20; ++i) f[i] = {0, 0};
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            f[x] = f[x] + make_pair(x, 0);
        }

        for(int i = 0; i < 20; ++i)
            for(int j = 0; j < 1 << 20; ++j)
                if(j >> i & 1) f[j ^ (1 << i)] = f[j ^ (1 << i)] + f[j];

        long long ans = 0;
        for(int i = 0; i < 1 << 20; ++i)
            ans = max(ans, 1LL * i * f[i].first * f[i].second);
        printf("%lld\n", ans);
    }

    return 0;
}

```
