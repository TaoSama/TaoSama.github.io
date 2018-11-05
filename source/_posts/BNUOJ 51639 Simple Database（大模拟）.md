---
title: BNUOJ 51639 Simple Database（大模拟）
categories:
  - 暴力
  - 大模拟
  - 
tags:
  - 大模拟
  - 
date: 2016-05-09 01:38:10
toc: 
---
题意：
>$模拟sql语句的执行过程，具体见题面$，[点击](https://www.bnuoj.com/v3/problem_show.php?pid=51639)
<!-- more -->

分析：
>$创建表，删除表，插入表都没啥说的$
$就是删除的条件可以省略，以及SET\ WHERE后面是有多个条件的$
$然后没啥照着模拟就好了，咋样好写咋来$
$弱智的我表示加上调试语句写了9.4k$
$时间复杂度自己估算能跑得过就好了，反正vector我当时算了是没问题的$

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
#include <sstream>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

void RE() {
    printf("%d\n", *((int*)0));
}

struct Table {
    string name;
    vector<string> key;
    vector<vector<string> > content;
    Table() {}
    Table(string name, vector<string> key): name(name), key(key) {}

    void add(vector<string> row) {
        if(key.size() != row.size()) {
            cout << "add illegal row\n";
            RE();
        }
        content.push_back(row);
    }

    void del(vector<pair<string, string> >& conditions) {
        vector<vector<string> >::iterator iter = content.begin();
        for(; iter != content.end();) { //row
            bool ok = true;
            for(int j = 0; j < iter->size(); ++j) { //col
                for(int k = 0; k < conditions.size(); ++k) { //conditions
                    if(key[j] == conditions[k].first) {
                        if((*iter)[j] != conditions[k].second) {
                            ok = false;
                            break;
                        }
                    }
                }
                if(!ok) break;
            }
            if(ok) iter = content.erase(iter);
            else ++iter;
        }
    }

    void update(vector<pair<string, string> >& sets,
                vector<pair<string, string> >& conditions) {
        for(int i = 0; i < content.size(); ++i) { //row
            bool ok = true;
            for(int j = 0; j < content[i].size(); ++j) { //col
                for(int k = 0; k < conditions.size(); ++k) {
                    if(key[j] == conditions[k].first) {
                        if(content[i][j] != conditions[k].second) {
                            ok = false;
                            break;
                        }
                    }
                }
                if(!ok) break;
            }
            if(!ok) continue;
            //update
            for(int j = 0; j < content[i].size(); ++j) {
                for(int k = 0; k < sets.size(); ++k) {
                    if(key[j] == sets[k].first)
                        content[i][j] = sets[k].second;
                }
            }
        }
    }

    vector<int> select(vector<pair<string, string> >& conditions) {
        vector<int> choose;
        for(int i = 0; i < content.size(); ++i) { //row
            bool ok = true;
            for(int j = 0; j < content[i].size(); ++j) { //col
                for(int k = 0; k < conditions.size(); ++k) {
                    if(key[j] == conditions[k].first) {
                        if(content[i][j] != conditions[k].second) {
                            ok = false;
                            break;
                        }
                    }
                }
                if(!ok) break;
            }
            if(ok) choose.push_back(i);
        }
        return choose;
    }
};
vector<Table> db;

#ifdef LOCAL
void see1(vector<string>& v) {
    for(auto s : v) cout << s << ' '; cout << endl;
}

void seedb() {
    cout << endl << string(20, '*') << endl;
    for(auto table : db) {
        cout << "name: " << table.name << endl;
        for(auto key : table.key) cout << key << ' '; cout << endl;
        for(auto vs : table.content) {
            for(auto s : vs) {
                cout << s << ' ';
            }
            cout << endl;
        }
    }
    cout << string(20, '*') << endl << endl;
}

void seeCondition(vector<pair<string, string> >& conditions) {
    for(auto p : conditions)
        cout << p.first << "=" << p.second << endl;
    cout << endl;
}
#endif // LOCAL

int findTable(string& name) {
    for(int i = 0; i < db.size(); ++i) {
        Table& table = db[i];
        if(name == table.name) return i;
    }
    return -1;
}

bool isSymbol(char c) {
    if(isalnum(c) || c == '_') return false;
    return true;
}

bool strip(string& s) {
    while(s.size() && isSymbol(s[0])) s.erase(0, 1);
    while(s.size() && isSymbol(s[s.size() - 1])) s.erase(s.size() - 1, 1);
}

void split(string& s, vector<string>& vs) {
    stringstream ss(s);
    string col;
    while(ss >> col) {
        strip(col);
        vs.push_back(col);
    }
}

void create(string& s) {
    vector<string> vs;
    split(s, vs);
//    see1(vs);

    if(~findTable(vs[0])) {cout << "error\n"; return;}
    db.push_back(Table(vs[0], vector<string>(vs.begin() + 1, vs.end())));
}

void drop(string& s) {
    int wh = findTable(s);
    if(wh == -1) {cout << "error\n"; return;}
    db.erase(db.begin() + wh);
}

void insert(string& s) {
    vector<string> vs;
    split(s, vs);
//    see1(vs);

    int wh = findTable(vs[0]);
    if(wh == -1) {cout << "error\n"; return;}
    db[wh].add(vector<string>(vs.begin() + 1, vs.end()));
}

void split(string& s, string& name, vector<pair<string, string> >& sets,
           vector<pair<string, string> >& conditions) {
    int white = s.find(' ');
    name = s.substr(0, white);
    s = s.substr(white + 1);

    size_t SET = s.find("SET");
    size_t WHERE = s.find("WHERE");
    if(SET != string::npos) {
        SET += 3;
        string setString = s.substr(SET, WHERE - SET);
        stringstream ss(setString);
        string key, value;
        while(ss >> key >> value >> value) {
            strip(key); strip(value);
            sets.push_back(make_pair(key, value));
        }
    }

    if(WHERE != string::npos) {
        WHERE += 5;
        string conditionString = s.substr(WHERE);
        stringstream ss(conditionString);
        string key, value;
        while(ss >> key >> value >> value) {
            strip(key); strip(value);
            conditions.push_back(make_pair(key, value));
        }
    }
}

bool checkKey(int wh, vector<pair<string, string> >& sets,
              vector<pair<string, string> >& conditions) {
    Table& table = db[wh];
    for(int i = 0; i < sets.size(); ++i) {
        bool have = false;
        for(int j = 0; j < table.key.size(); ++j) {
            if(sets[i].first == table.key[j]) {
                have = true;
                break;
            }
        }
        if(!have) return false;
    }
    for(int i = 0; i < conditions.size(); ++i) {
        bool have = false;
        for(int j = 0; j < table.key.size(); ++j) {
            if(conditions[i].first == table.key[j]) {
                have = true;
                break;
            }
        }
        if(!have) return false;
    }
    return true;
}

void del(string& s) {
    string name;
    vector<pair<string, string> > sets, conditions;
    split(s, name, sets, conditions);
    int wh = findTable(name);
    if(wh == -1) {cout << "error\n"; return;}

//    seeCondition(sets); seeCondition(conditions);

    if(!conditions.size()) db[wh].content.clear();
    else {
        if(!checkKey(wh, sets, conditions)) {cout << "error\n"; return;}
        db[wh].del(conditions);
    }
}

void update(string& s) {
    string name;
    vector<pair<string, string> > sets, conditions;
    split(s, name, sets, conditions);
    int wh = findTable(name);
    if(wh == -1) {cout << "error\n"; return;}
    if(!checkKey(wh, sets, conditions)) {cout << "error\n"; return;}

//  seeCondition(sets); seeCondition(conditions);

    db[wh].update(sets, conditions);
}

void select(string& s) {
    string name;
    vector<pair<string, string> > sets, conditions;
    split(s, name, sets, conditions);
    int wh = findTable(name);
    if(wh == -1) {cout << "error\n"; return;}
    if(!checkKey(wh, sets, conditions)) {cout << "error\n"; return;}

//    puts("select");
//    seeCondition(conditions);

    Table& table = db[wh];
    vector<int> choose = table.select(conditions);
    for(int i = 0; i < table.key.size(); ++i) {
        if(i) cout << ' ';
        cout << table.key[i];
    }
    cout << '\n';
    for(int i = 0; i < choose.size(); ++i) {
        vector<string>& v = table.content[choose[i]];
        for(int j = 0; j < v.size(); ++j) {
            if(j) cout << ' ';
            cout << v[j];
        }
        cout << '\n';
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif

    int t; cin >> t;
    while(t--) {
        int q; cin >> q; cin.get();
        static int kase = 0;
        cout << "Case #" << ++kase << ":\n";

        db.clear();
        while(q--) {
            string s;  getline(cin, s);
            int white = s.find(' ');
            string cmd = s.substr(0, white);
            string lft = s.substr(white + 1);

            if(cmd == "CREATE") create(lft);
            else if(cmd == "DROP") drop(lft);
            else if(cmd == "INSERT") insert(lft);
            else if(cmd == "DELETE") del(lft);
            else if(cmd == "UPDATE") update(lft);
            else select(lft);

//            cout << q << endl; seedb();
        }
    }
    return 0;
}
```
