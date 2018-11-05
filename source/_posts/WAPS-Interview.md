---
title: WAPS Interview
categories:
  - Doing
  - Interview
  - 
tags:
  - 
  - 
date: 2017-04-28 17:05:10
toc: true
---

3.31 rejected..
Since the campus recruitment in this year is over, a little summary here..

<!--- more --->

### Online Coding
#### ???
I forgot this one and no record, really easy though.

#### Verify Preorder Serialization of a Binary Tree
One way to serialize a binary tree is to use pre-order traversal. 
When we encounter a non-null node, we record the node's value. 
If it is a null node, we record using a sentinel value such as #.

* Examples 
    • "9,3,4,#,#,1,#,#,2,#,6,#,#" → true
    • "1,#" → false
    • "9,#,#,1" → false

* Solution
Just simply go pre-order traversal and check whether each node has two sons...
Careful about some corner cases..

* Code
![](http://7xru22.com1.z0.glb.clouddn.com/17-4-28/55313160-file_1493372200147_2948.png)

### Onsite Interview
4 easy problems... I gotta have a solution only with a glance.

I only recall 2 of them.

#### Anagram Matching
anagram: a word formed by rearranging the letters of another, such as cinema, formed from iceman
try to figgure out the number of occurrences of all the anagrams of $T$ in $S$, lowercase alphabets

* Solution
use the number of occurrences of each alphabet to hash all the anagrams of $T$
total time complexity is $O(\sum |S|),where\sum = 26$

#### Maximum Weighted Independent Set of a tree
choose some nodes that each pair of them have no edge.
try to maximize the total weight of the chosen nodes

* Solution
simple tree dp. $f[u][0/1]:=$ the maximum weight of the independent set that the subtree rooted at $u$, and choosing $u$ or not
and the transition is simple:
choose $u$, all of its sons mustn't be choosed
not choose $u$, choosing or not choosing its sons is OK, choose the maximum one. 
$ans=\max \{f[root][0], f[root][1]\}$
total time complexity is $O(N)$

#### Summary
Two interviewers are both very nice and patient. Good Expericence.

#### VP Interview
Fvcking stupid that I talked about FP programming...
I'm not familiar with that..
I think the Japanese interviewer was a little sleepy, so absolutely my interview is a shit.. even he "woke up" when heard FP..
And it seemed that the Chinese one is weak.. some easy cpp questions..

Bad Expericence！
A Lesson：no more talking about the things without knowing a shit..


### Something more
No chance to attempt the indeed interview is a pity..
it would be very nice to have a free trip to Japan..

