---
title: Notes, Hive Tutorial
categories:
  - Doing
  - Hive
  - 
tags:
  - 
  - 
date: 2017-08-25 11:07:00
toc: true

---
~~Last Modified: 2017-08-28 16:49:00~~

### Acknowledge
* What is Hive?
  Hive is a data warehouse infrastructure tool to process structured data in Hadoop. It resides on top of Hadoop to summarize Big Data, and makes querying and analyzing easy.  

* Official Hive Tutorial
[Tutorial](https://cwiki.apache.org/confluence/display/Hive/Tutorial)

<!--- more --->

### Introduction
[Hive - Introduction](https://www.tutorialspoint.com/hive/hive_introduction.htm)

#### Big Data
* Hadoop
  * MapReduce
  * HDFS
* Tools
  * Sqoop
  * Pig
  * Hive

* Hive 
  `see the link above for details`
    
#### Syntax
`Syntax is omitted, see the reference for details`

* Views And Indexes 
  [Difference between view and index](https://stackoverflow.com/questions/24197856/what-is-difference-between-index-and-view-in-mysql)
* Select Joins
  [Inner Join vs Outer Join](http://www.diffen.com/difference/Inner_Join_vs_Outer_Join)
  [Inner Join vs Natural Join](https://stackoverflow.com/questions/8696383/difference-between-natural-join-and-inner-join), `Natural Join` is just short syntax for a **specific** `Inner Join`

  * JOIN (same as INNER JOIN)
    JOIN clause is used to combine and retrieve the records from multiple tables. 
    (It only shows the matched result)
  * LEFT OUTER JOIN
    LEFT OUTER JOIN returns all the rows from the left table, even if there are no matches in the right table. This means, if the ON clause matches 0 (zero) records in the right table, the JOIN still returns a row in the result, but with NULL in each column from the right table.
  * RIGHT OUTER JOIN
    RIGHT OUTER JOIN returns all the rows from the right table, even if there are no matches in the left table. If the ON clause matches 0 (zero) records in the left table, the JOIN still returns a row in the result, but with NULL in each column from the left table.
  * FULL OUTER JOIN
    FULL OUTER JOIN `combines` the records of both `the left and the right outer tables` that fulfil the JOIN condition. The joined table contains either all the records from both the tables, or fills in NULL values for missing matches on either side.
 
### Reference
[Learn Hive](https://www.tutorialspoint.com/hive/index.htm)
[Hive Language Manual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)
