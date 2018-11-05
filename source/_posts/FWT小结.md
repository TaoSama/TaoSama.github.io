---
title: FWT小结
categories:
  - 小结
  - 
  - 
tags:
  - 
  - 
date: 2016-09-10 00:18:10
toc: true
---

## Ⅰ. 快速沃尔什变换认知
* 这东西英文名叫$Fast\ Walsh$-$Hadamard\ Transform$
* $FWT$是用来快速计算位运算卷积的，至于什么是卷积，可以先学习一下$FFT$
* [FFT小结，点击链接](https://www.zybuluo.com/TaoSama/note/171617)
* 既然提到了位运算，必然要和子集扯上关系，也就是说可以来求子集卷积

<!-- more -->
## Ⅱ. 快速沃尔什变换
### 形式
* 类似于$FFT$的卷积形式，假设$\otimes$为卷积符号，对于$2$个等长的系数向量$\overrightarrow{a}$和$\overrightarrow{b}$
* 对于它们的卷积$\overrightarrow{c}=\overrightarrow{a}⊗\overrightarrow{b}$，有$C_k= \displaystyle \sum_{i\otimes j=k} A_i \times B_j$
* 其实当$\otimes$为$+$的时候，就是普通的卷积形式了

### 计算
 假设$2$个向量长度都是$n$，那么暴力计算位运算卷积是$O(n^2)$的

#### 变换
* 类比$FFT$，假设我们知道一种类似于$DFT$的变换$tf$，可以使向量$X$产生一个新向量$tf(X)$
* 以及类似于$IDFT$的变换$utf$，能使得$utf(tf(X))=X$
* 并且变换$tf$必须具有这样的性质：$tf(X\otimes Y)=tf(X)\times tf(Y)$

#### 分治
* 类比$FFT$，我们考虑分治，接下来为了方便，我们令$\otimes$为$xor$运算，即$\oplus$
* 令$Z=X\otimes Y$
* 考虑$X$和$Y$都只有$1$个元素的时候，此时不需要变换，显然有$Z_{0\oplus 0=0}=X_0 \times Y_0$，即满足$tf(X)=X=utf(X)$
* 考虑各有$2$个元素的时候，令$X=(a,\ b),\ Y=(c,\ d)$，此时$Z$
$Z_0=ac+bd,\ Z_1=ad+bc$，即$(a,\ b)\otimes (c,\ d)=(ac + bd,\ ad + bc)$

---

* 令$tf(a,\ b) = (a - b,\ a + b)$
那么$tf(a,\ b) \times tf(c,\ d)$
$= (a - b,\ a + b) \times (c - d, c + d)$
$= ( (a-b)\times(c-d) ,\ (a+b)\times(c+d) )$
$= ( ac - ad - bc + bd,\ ac + ad + bc + bd )$
$ = ( ac + bd - ad - bc,\ ac + bd + ad + bc )$
$= tf( ac + bd,\ ad + bc )$
$ = tf( (a,b) \otimes (c,d ) )$
* 显然我们可以发现，对于偶数长度的向量，均分成$2$个向量都满足这个性质
* 当然也可以用数学归纳法证一波，具体去看[Reference](#Reference)里的证明吧
* 之后我们就可以利用这个性质来递归的变换
* 令$X$是个偶数长度的向量，且$X=(X1,\ X2)$，$X1$和$X2$各是$X$的一半
* 那么有$tf(X1,\ X2) = (tf(X1) - tf(X2),\ tf(X1) + tf(X2))$
* 具体就是先递归变换$2$个等长的子序列，$X1$和$X2$都递归变换过了，
那么新的$X1=X1-X2$，同理新的$X2=X1+X2$

---

* 当然逆变换$utf$也很好构造，只需要反向回去就好了，每次解一下方程
* 令$Y1 = tf(X1) - tf(X2),\ Y2 = tf(X1) + tf(X2)$，
现在$Y1$和$Y2$已知，解出$tf(X1)$和$tf(X2)$即可


#### 三种位运算变换总结
* $tfxor(A)=(tfxor(A_0+A_1),\ tfxor(A_0−A_1))$
$utfxor(A)=(utfxor( (A_0+A_1)/2),\ utfxor( (A_0−A_1)/2))$
* $tfand(A)=(tfand(A_0+A_1),\ tfand(A_1))$
$utfand(A)=(utfand(A_0−A_1),\ utfand(A_1))$
* $tfor(A)=(tfor(A_0),\ tfor(A_1+A_0))$
$utfor(A)=(utfor(A_0),\ utfor(A_1−A_0))$

### 时间复杂度与写法
* 时间复杂度$T(n) = 2T(n/2)+O(n)$，根据主定理为$O(nlogn)$
* 写法就跟$FFT$类似了，先把长度扩展到$2$的幂次，之后按照前面说的分治就好了
* 具体可以参照下面的板子题

### 位运算卷积与子集卷积
* 我没有太深的理解，基本的理解就是，其实它们是共通的
* 把数看成二进制下的子集，那么 `&` 便是集合交，`|` 是集合并，`^` 是集合对称差
* 所以很多时候题目可以从这$2$个角度都能说通，也提供了另一个思维方向

## Ⅲ.  题目选讲
### [IForg 1028 Bob and Alice are playing numbers](http://www.ifrog.net/acm/problem/1028)

分析：
板子题，$n$个数里选$2$个数进行三种位运算的最大值
数的大小只有$10^6$，$cnt[i]:=i$这个数出现了多少次
然后卷积一下自己，减去自己和自己的，倒着枚举找到最大的那个就做完了

代码:
```cpp
//
//  Created by TaoSama on 2016-09-09
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

LL quick(LL x, LL n) {
    LL ret = 1;
    for(; n; n >>= 1) {
        if(n & 1) ret = ret * x % MOD;
        x = x * x % MOD;
    }
    return ret;
}

const LL invTwo = quick(2, MOD - 2);

void fwtXor(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    fwtXor(a, h);
    fwtXor(a + h, h);
    for(int i = 0; i < h; ++i) {
        LL x1 = a[i];
        LL x2 = a[i + h];
        a[i] = (x1 + x2) % MOD;
        a[i + h] = (x1 - x2 + MOD) % MOD;
    }
}

void ifwtXor(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    for(int i = 0; i < h; ++i) {
        // y1=x1+x2
        // y2=x1-x2
        LL y1 = a[i];
        LL y2 = a[i + h];
        a[i] = (y1 + y2) * invTwo % MOD;
        a[i + h] = (y1 - y2 + MOD) * invTwo % MOD;
    }
    ifwtXor(a, h);
    ifwtXor(a + h, h);
}

void fwtAnd(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    fwtAnd(a, h);
    fwtAnd(a + h, h);
    for(int i = 0; i < h; ++i) {
        LL x1 = a[i];
        LL x2 = a[i + h];
        a[i] = (x1 + x2) % MOD;
        a[i + h] = x2;
    }
}

void ifwtAnd(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    for(int i = 0; i < h; ++i) {
        // y1=x1+x2
        // y2=x2
        LL y1 = a[i];
        LL y2 = a[i + h];
        a[i] = (y1 - y2 + MOD) % MOD;
        a[i + h] = y2;
    }
    ifwtAnd(a, h);
    ifwtAnd(a + h, h);
}

void fwtOr(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    fwtOr(a, h);
    fwtOr(a + h, h);
    for(int i = 0; i < h; ++i) {
        LL x1 = a[i];
        LL x2 = a[i + h];
        a[i] = x1;
        a[i + h] = (x2 + x1) % MOD;
    }
}

void ifwtOr(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    for(int i = 0; i < h; ++i) {
        // y1=x1
        // y2=x2+x1
        LL y1 = a[i];
        LL y2 = a[i + h];
        a[i] = y1;
        a[i + h] = (y2 - y1 + MOD) % MOD;
    }
    ifwtOr(a, h);
    ifwtOr(a + h, h);
}

int n, op;
const int C = 1 << 20;
LL a[N], cnt[C + 10];

int gao() {
    for(int i = C; ~i; --i) if(cnt[i]) return i;
    return -1;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &op);
        memset(cnt, 0, sizeof cnt);
        for(int i = 1; i <= n; ++i) {
            scanf("%lld", a + i);
            ++cnt[a[i]];
        }

        static int kase = 0;
        printf("Case #%d: ", ++kase);
        if(op == 1) {
            fwtAnd(cnt, C);
            for(int i = 0; i < C; ++i) cnt[i] = cnt[i] * cnt[i] % MOD;
            ifwtAnd(cnt, C);
            for(int i = 1; i <= n; ++i) --cnt[a[i] & a[i]];
            printf("%d\n", gao());
        } else if(op == 2) {
            fwtXor(cnt, C);
            for(int i = 0; i < C; ++i) cnt[i] = cnt[i] * cnt[i] % MOD;
            ifwtXor(cnt, C);
            for(int i = 1; i <= n; ++i) --cnt[a[i] ^ a[i]];
            printf("%d\n", gao());
        } else {
            fwtOr(cnt, C);
            for(int i = 0; i < C; ++i) cnt[i] = cnt[i] * cnt[i] % MOD;
            ifwtOr(cnt, C);
            for(int i = 1; i <= n; ++i) --cnt[a[i] | a[i]];
            printf("%d\n", gao());
        }
    }

    return 0;
}
```

### SRM 518 NIM

题意: 
$2$个人玩$nim$游戏，能选$K\le 10^9$堆，每堆必须是素数$p_i\le L\le 10^6$，后手赢的方案数

分析:
$nim$游戏，由$SG$定理知，先手$xorsum$为$0$输，即后手赢
问题就变成了这个，之后就可以$dp$了，$f[i][j]:=$选$i$堆异或和为$j$的方法数
显然$f[1][j]$是知道的，转移是$f[i][j]=\displaystyle\sum_{x\oplus y=j} f[i-1][x] \times f[1][y]$
发现这是个$and$卷积的形式，答案就是卷积的$k$次幂，所以直接做就好了

主要代码：
```
fwtXor(a, L)
a[i] = a[i] ^ k
ifwtXor(a, L)
ans = a[0]
```

### Codeforces 449D Jzzhu and Numbers
题意：
给定长度为$N\le 10^6$的数列，$A_i\le 10^6$，选出$0<k\le N$个数
使得它们二进制与起来的值为$0$，求方法数
分析：
题解给了一个容斥的做法，是基于子集卷积的
$f[s]:=$子集状态为$s$的方法数，$g[s]:=s$中$1$的个数
$f[s]$可由$fwt$子集卷积变换得到，之后我们根据容斥原理：
$ans=2^n+\displaystyle\sum_{s=1}^{2^{20}-1}(-1)^{g(s)}\cdot2^{f(s)}$，这里空集被容斥掉了
事实上，可以不用自己容斥，无论是哪种理解，对于某个$f[s]$，可以随便选
即变成$2^{f[s]}$，然后再$ifwt$变换回去，答案就是$f[0]$，这里空集同样被容斥掉了
从这里我们看出其实卷积也有容斥的感觉
(试了一下代码发现$fwt$变换其实就是所谓的高维前缀和)

代码：
```cpp
//
//  Created by TaoSama on 2016-09-09
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
const int N = 1e6 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

typedef long long LL;

void fwtAnd(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    fwtAnd(a, h);
    fwtAnd(a + h, h);
    for(int i = 0; i < h; ++i) {
        LL x1 = a[i];
        LL x2 = a[i + h];
        a[i] = (x1 + x2) % MOD;
        a[i + h] = x2 % MOD;
    }
}

void ifwtAnd(LL* a, int len) {
    if(len == 1) return;
    int h = len >> 1;
    for(int i = 0; i < h; ++i) {
        // y1=x1+x2
        // y2=x2
        LL y1 = a[i];
        LL y2 = a[i + h];
        a[i] = (y1 - y2 + MOD) % MOD;
        a[i + h] = y2 % MOD;
    }
    ifwtAnd(a, h);
    ifwtAnd(a + h, h);
}

int n;
const int C = 1 << 20;
LL cnt[C + 10], two[C + 10];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    two[0] = 1;
    for(int i = 1; i < C; ++i) two[i] = two[i - 1] * 2 % MOD;

    while(scanf("%d", &n) == 1) {
        memset(cnt, 0, sizeof cnt);
        for(int i = 1; i <= n; ++i) {
            int x; scanf("%d", &x);
            ++cnt[x];
        }

        fwtAnd(cnt, C);
        for(int i = 0; i < C; ++i) cnt[i] = two[cnt[i]];
        ifwtAnd(cnt, C);
        printf("%I64d\n", cnt[0]);
    }

    return 0;
}
```


## Reference
* [SRM 518 NIM官方题解，含有证明](https://apps.topcoder.com/wiki/display/tc/SRM+518)
* [PICKS关于FWT，构造方案](http://picks.logdown.com/posts/179290-fast-walsh-hadamard-transform)
* [板子来源参考](http://www.chanmefang.com/index.php/2015/11/11/fwt/)
