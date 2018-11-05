---
title: 头条秋招春招失败的思考
categories:
  - Doing
  - Interview
  - 
tags:
  - 
  - 
date: 2017-04-28 17:20:10
toc: true

---

毕设交了一波初稿，总算回来填坑了。回头想想，不得不说头条面试反映了我的心境变化。

<!--- more --->

## 秋招后台岗
### 一面
记得有2个题，其中一个应该非常水的题，没啥印象了。

#### 数组中乘积最大连续子数组
这个现在就是一眼的dp，只怪刷了那么多题的我还是刷题太少了。

* Old Solution
直接暴力是$O(n^3)$的，所以就显然的前缀积优化到$O(n^2)$
蓝儿因为有$0$，这里就会有些问题，为了避免除$0$要做一些处理
不过实际上只要乘了$0$这一段乘积就是$0$了，所以把所有$0$的位置抠出来就可以了
然后枚举每一段，就不会有事了，答案初始化为$0$就可以了，前提是有一个$0$。。
* New Solution
很简单的dp啊，，$f[i][0/1]:$=到$i$这个位置的连续乘积最大值/最小值
就是套用最大连续子段和的套路，这破题我还被问过两遍
$HR$又做了一个更复杂的的树形的，不过那个时候我一眼秒了。。
就因为有负数嘛，所以就简单的维护下最小值就好了。。

### 二面
#### 写个类似单调栈的玩意儿
印象中应该让写了一个支持`push`,`pop`,`getMin`的栈，所有操作$O(1)$

* Solution
这东西也很简单啊，就维护数字栈的之后再顺手维护一个单调栈，细节注意一下就好了
我当时并没有想到单调栈，可能平时用的不多。不过还是有脑子的，随便一想就想到了。
我记得这个破题剑指offer上有吧，虽然一直没注意。

#### 微信怎么拉取朋友圈信息
这东西我真不会啊，就xjb说了一下，本地存个跟帐号有关的hashvalue表示已经拉取了多少
每次拉取的时候，服务器也生成一个，比对一下，不适最新的就拉取就好了。
当然你问我什么别人点赞评论啥的，我真的不会啊。。

#### 服务器很少，流量很大，怎么负载均衡啊
不会啊，xjb扯淡啊，就随手说了一下缓存服务器，Nginx啥的(这东西我tm就听过名字。
~~讲道理啊，为啥不买设备啊喂！(大雾~~

### 三面
哇，这面跑不了啊，直接就问我后台技术栈啥的。
骗不过去了啊。然后就不知道啊。有点绝望啊。
意识不清醒了啊，打完比赛找不到工作一同交织在一起啊。
然后面试官问我读不读研啊。然后脑子一抽说想读啊。
然后我还耿直的去问头条有没有quit工作去读研啊
~~没见过比我更sb的人了~~
主要还是比赛打炸了，心态太差了。几个月都没调好。

### 然后就没然后了啊


## 春招算法岗

### 一面
扯扯淡啊，自我介绍啥的啊，ACM打的怎么样啊。。

#### 二叉树最长路径长度
一看就当普通树做了啊，然后就无脑说两次dfs求直径就好了啊。
他说能不能一次啊，那就无脑dp求一下直径啊。
他说还能不能再简单一点啊。 ~~(dp还不简单啊~~
哇 我又看了一下题，发现二叉树。
然后我就说那简化一下啊。

* Code

```cpp
struct Node{
    Node* ls, *rs;
};

const int INF = INT_MAX / 2;

int maxd = -INF;
int dfs(Node* cur){
    if(cur == nullptr) return 0;
    int ldep = dfs(cur->ls);
    int rdep = dfs(cur->rs);
   	maxd = max(maxd, ldep + rdep + 1);
    return max(ldep, rdep) + 1;
}
```

#### 脑筋急转弯
有25个人，跑道一次只能跑5个人，问最少跑几次能得到冠亚季军啊。

* Solution
一眼看穿是多路归并啊，然后算了一下是8啊。。

### 二面
不记得问了啥啊，就扔了一个题，然后问了点儿机器学习的东西

#### 复杂链表的复制

* Description
有一个链表L,其每个节点有2个指针，一个指针next指向链表的下个节点，另一个random随机指向链表中的任一个节点，可能是自己或者为空，写一个程序，复制这个链表

* Solution
剑指offer上有啊，看了一眼思路啊当时就，然后很慌啊。
不过面了那么多面试啊，一下子就冷静了啊。然后就会了啊。
就后面拷贝一份搞一搞啊。

* Code

```cpp
struct Node{
    int val;
    Node *nxt, *random;
    Node(){}
    Node(int val, Node *nxt, Node *random): val(val), nxt(nxt), random(random){}
    
};

Node* copyComplexList(Node *head){
    if(head == nullptr) return nullptr;
    
    for(Node *cur = head; cur != nullptr; cur = cur->nxt->nxt){
        Node *copied = new Node(cur->val, cur->nxt, cur->random);
        cur->nxt = copied;
    }
    for(Node *cur = head->nxt; cur != nullptr; cur = cur->nxt->nxt){
        cur->random = cur->random->nxt;
    }
    
    Node *nHead = head->nxt;
    for(Node *cur = head; cur != nullptr; cur = cur->nxt){
        Node *copied = cur->nxt;
        Node *copiedNxt = copied->nxt->nxt;
        Node *curNxt = cur->nxt->nxt;
        cur->nxt = curNxt;
        copied->nxt = copiedNxt;
    }
    return nHead;
}
```

#### 机器学习
* 手推一下LR吧
就直接把Hypothesis Function，Cost Function写一写，Gradient Decent推一推啊。
然后我就推了一下啊，sigmoid函数的导数差点不会推啊。。有惊无险啊
* 正则参数$\lambda$选取的影响啊
 大了会overfitting啊，小了会underfitting啊。

#### 一个奇怪的题
* Description
对一个数组，有n个数据，找一个索引的位置k，使前k个数的方差var(k)和后面n-k个数的方差var(n-k)之和最小。

* Solution
不知道能不能$O(1)$啊，不会做啊，就暴力展开了一下方差啊
然后$O(n)$枚举$k$，$O(1)$算答案，总复杂度$O(n)$

* Code

```cpp
/*
var = \sum_{i=1}^n (x_i - avg)^2 
= \sum_{i=1}^n (x_i ^ 2 + avg^2 - 2*x_i*avg)
= \sum_{i=1}^n (x_i ^ 2 + (sum / n)^2 - 2 * x_i * (sum / n))
= \sum_{i=1}^n x_i^2 + sum^2 /n - 2*sum^2/n= \sum_{i=1}^n x_i^2 - sum^2/n
*/

int findIndex(const vector<int>& v){
    int preSqSum = 0, totSqSum = 0;
    int preSum = 0, totSum = 0;
    pair<double, int> varAndIdx = {-1, -1};
    for(int i = 0; i < v.size(); ++i){
        totSqSum += v[i] * v[i];
        totSum += v[i];
    }
    for(int i = 0; i < v.size(); ++i){
        preSum += v[i];
        preSqSum += v[i] * v[i];
        int sufSqSum = totSqSum - preSqSum;
        int sufSum = totSum - preSum;
        double preVar = preSqSum - 1.0 * preSum * preSum / (i + 1);
        double sufVar = sufSqSum - 1.0 * sufSum * sufSum/ (v.size() - i - 1);
        varAndIdx = max(varAndIdx, {preVar + sufVar, i});
    }
    return varAndIdx.second;
}
```

### 三面
#### 24点游戏

* Description
给定4个整数0～9，给出是否能计算得到24，加减乘除括号，普通算术运算。精度他说`1e-5`

* Solution
以前写过啊，蓝儿没脑子了啊，连面了两面。
就选择写暴力搜索所有表达式啊。
然后就因为`string v = "" + toChar(a) + toChar(b) + toChar(c) + toChar(d);`
这个垃圾代码不CE挂了啊。。我以为会CE的啊。结果最后半天才反应到这里。
写了半个多小时，面试官他写了一个都写完了啊。。难受啊。。
我当时好怂啊，不自信啊。。结果面试结束后几分钟就调通了啊。

* My Code
找不到最终版本了，就扔个有点小问题的吧。纪念一下我这个“C++大师”

```cpp
#include <bits/stdc++.h>
using namespace std;

const string op = "+-*/";

int getPriority(char c) {
    if(c == '+' || c == '-') return 1;
    else if(c == '*' || c == '/') return 2;
    return 0;
}

vector<string> inToPost(const string& expr) {
    stack<char> opr;
    vector<string> ret;
    for(int i = 0; i < expr.size(); ++i) {
        char c = expr[i];
        if(c == '(') opr.push(c);
        else if(c == ')') {
            while(true) {
                char top = opr.top(); opr.pop();
                if(top == '(') break;
                ret.push_back(string(1, top));
            }
        } else if(isdigit(c)) {
            string digit;
            for(; i < expr.size() && isdigit(expr[i]); ++i) digit += expr[i];
            ret.push_back(digit);
            --i;
        } else {
            int curP = getPriority(c);
            for(; opr.size() && getPriority(opr.top()) >= curP; opr.pop())
                ret.push_back(string(1, opr.top()));
            opr.push(c);
        }
    }
    for(; opr.size(); opr.pop()) ret.push_back(string(1, opr.top()));
    return ret;
}

double calc(const vector<string>& post) {
    stack<double> opd;
    for(const auto& s : post) {
        if(isdigit(s[0])) opd.push(stod(s));
        else {
            assert(opd.size());
            double y = opd.top(); opd.pop();
            assert(opd.size());
            double x = opd.top(); opd.pop();
            if(s[0] == '+') opd.push(x + y);
            else if(s[0] == '-') opd.push(x - y);
            else if(s[0] == '*') opd.push(x * y);
            else opd.push(x / y);
        }
    }
    return opd.top();
}


bool check(int dep, bool lftBracketed, string s) {
    if(dep == s.size()) {
        if(lftBracketed) s += ')';
//        cout << s << endl;
        if(abs(calc(inToPost(s)) - 24) < 1e-5) return true;
        return false;
    }
    if(isdigit(s[dep])) {
        if(dep > 0) {
            for(int i = 0; i < 4; ++i) {
                string ns = s;
                ns.insert(dep, 1, op[i]);
                if(check(dep + 2, lftBracketed, ns)) return true;
                if(lftBracketed) {
                    ns.insert(dep + 2, 1, ')');
                    if(check(dep + 3, 0, ns)) return true;
                } else {
                    ns.insert(dep + 1, 1, '(');
                    if(check(dep + 3, 1, ns)) return true;
                }
            }
        } else if(check(dep + 1, lftBracketed, s)) return true;
    } else if(check(dep + 1, lftBracketed, s)) return true;
    return false;
}

bool twentyFour(int a, int b, int c, int d) {
    auto toChar = [](int x) {return char('0' + x);};
    string v = string("") + toChar(a) + toChar(b) + toChar(c) + toChar(d);
    sort(v.begin(), v.end());

    bool ok = false;
    do {
        ok |= check(0, 0, v);
    } while(!ok && next_permutation(v.begin(), v.end()));
    return ok;
}

int main() {
    string test = "5*5-5/5";
    //cout << calc(test) << endl;
    cout << (calc(inToPost(test))) << endl;
    cout << twentyFour(5, 5, 5, 5) << endl;

    return 0;
}
```

* Interviewer's Code
没仔细读这个代码啊，改天研究一下正确性，写一个bugfree的24点感觉不容易啊。。

```python
import itertools
def isEqual(v0, v1):
    return abs(v0 - v1) < 1e-6
def expresssion(code, v0, v1):
    if code == 0:
        return v0 + v1
    elif code == 1:
        return v0 - v1
    elif code == 2:
        return v1 - v0
    elif code == 3:
        return v0 * v1
    elif code == 4:
        if isEqual(v1, 0):
            return float('NaN')
        return float(v0)/v1
    elif code == 5:
        if isEqual(v0, 0):
            return float('NaN')
        return float(v1)/v0
def printExpr(code, v0, v1):
    if code == 0:
        print '%s+%s' %(v0, v1)
    if code == 1:
        print '%s-%s' %(v0, v1)
    if code == 2:
        print '%s-%s' % (v1, v0)
    if code == 3:
        print '%s*%s' % (v0, v1)
    if code == 4:
        print '%s/%s' % (v0, v1)
    if code == 5:
        print '%s/%s' %(v1, v0)
def search(a, v):
    if len(a) == 1:
        return isEqual(a[0], v)
    elif len(a) == 2:
        for code in range(6):
            if isEqual(expresssion(code, a[0], a[1]), v):
                printExpr(code, a[0], a[1])
                return True
        return False
    elif len(a) == 3:
        for b in itertools.permutations(a):
            for code in range(6):
                if search([expresssion(code, b[0], b[1]), b[2]], v):
                    printExpr(code, b[0], b[1])
                    return True
        return False
    elif len(a) == 4:
        for b in itertools.permutations(a):
            for code in range(6):
                if search([expresssion(code, b[0], b[1]), b[2], b[3]], v):
                    printExpr(code, b[0], b[1])
                    return True
        return False
    elif len(a) == 4:
        for b in itertools.permutations(a):
            for code in range(6):
                if search([expresssion(code, b[0], b[1]), b[2], b[3]], v):
                    printExpr(code, b[0], b[1])
                    return True
        return False
print search([3, 3, 8, 8], 24)
```

### 然后就又没然后了啊

## 后记
好好学习辣！然后要自信！失败了并不会失去什么。
