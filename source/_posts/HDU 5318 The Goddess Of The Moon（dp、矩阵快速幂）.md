---
title: HDU 5318 The Goddess Of The Moon（dp、矩阵快速幂）
categories:
  - 动态规划
  - 矩阵优化
  - 
tags:
  - 矩阵快速幂
  - 
date: 2016-03-28 16:10:10
toc: 
---
题意：
>$N\le 50，M\le 10^9，N个字符串，选出M个拼接到一起$
$(i,\ j)拼接的条件是i的后缀和j的前缀的公共长度\ge 2$
$问拼接成不同的字符串的个数，答案对10^9+7取模$

<!-- more -->

分析：
>$N小，并且M是10^9，赤果果的告诉你是矩阵快速幂$
$据说字符串要先去重，这是一个trick$
$f[i][j]:=长度为i，j结尾的方法数，转移矩阵是trans[i][j]:=(i,\ j)能否拼接$
$ans =\sum_{i=1}^n f[M][i]= f[1][i]\cdot (trans[i][j])^{M-1}$
$时间复杂度为O(n^3logm)$


代码：
```cpp
//
//  Created by TaoSama on 2016-03-22
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
const int N = 50 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;

typedef long long LL;
struct Matrix {
    int row, col;
    LL mat[N][N];
    void init(int r, int c, bool one = false) {
        row = r; col = c;
        memset(mat, 0, sizeof mat);
        if(!one) return;
        for(int i = 0; i < row; ++i) mat[i][i] = 1;
    }
    Matrix operator* (const Matrix& rhs) {
        Matrix ret; ret.init(row, rhs.col);
        for(int k = 0; k < col; ++k) {
            for(int i = 0; i < row; ++i) {
                if(mat[i][k] == 0) continue;
                for(int j = 0; j < rhs.col; ++j) {
                    if(rhs.mat[k][j] == 0) continue;
                    ret.mat[i][j] = (ret.mat[i][j] + mat[i][k] * rhs.mat[k][j]) % MOD;
                }
            }
        }
        return ret;
    }
    Matrix operator^ (LL n) {
        Matrix ret, x = *this;
        ret.init(row, col, 1);
        while(n) {
            if(n & 1) ret = ret * x;
            x = x * x;
            n >>= 1;
        }
        return ret;
    }
} A, ans;

string a[55];

bool check(int x, int y) {
    for(int i = 2; i <= a[x].size(); ++i) {
        string suffix = a[x].substr(a[x].size() - i);
        string prefix = a[y].substr(0, i);
        if(suffix == prefix) return true;
    }
    return false;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        set<string> s;
        for(int i = 0; i < n; ++i) {
            char buf[20]; scanf("%s", buf);
            s.insert(buf);
        }
        n = 0;
        for(auto &str : s) a[n++] = str;

        A.init(n, n);
        for(int i = 0; i < n; ++i)
            for(int j = 0; j < n; ++j)
                A.mat[i][j] = check(i, j);

//      prln(check(0, 0));
//      for(int i = 0; i < n; ++i){
//          for(int j = 0; j < n; ++j)
//              printf("(%s, %s): %d\n", a[i].c_str(), a[j].c_str(), A.mat[i][j]);
//      }

        ans.init(1, n);
        for(int i = 0; i < n; ++i) ans.mat[0][i] = 1;
        ans = ans * (A ^ m - 1);

        int sum = 0;
        for(int i = 0; i < n; ++i)
            if((sum += ans.mat[0][i]) >= MOD) sum -= MOD;
        printf("%d\n", sum);
    }
    return 0;
}

```