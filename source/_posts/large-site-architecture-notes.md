---
title: Notes, 大型网站技术架构 
categories:
  - Doing 
  - Distributed
  - 
tags:
  - 
  - 
  - 
date: 2017-10-27 11:52:10
toc: true
---

### Overview

#### Evolution of Large Site Architecture

##### Characteristics

* high concurrency, huge traffic
* high availability
* vast data
* users from everywhere, complex situation of network
* bad environment of security 
* quick demands changing, frequently releasing
* progessive development

<!--- more --->

##### Development Progress
* begin
![](/images/17-10-27_47885692.jpg)
* separate application and data service
* use cache
* use application cluster
* separate read and write of database
* use reverse proxy and CDN
![](/images/17-10-29_44949985.jpg)
* use distributed file system and distributed database
* use NoSQL and search engine
![](/images/17-10-29_8148745.jpg)
* separate bussiness
* use distributed services
![](/images/17-10-29_62668186.jpg)


##### Values
* key is to be flexible to the bussiness
* major pusher is bussiness developing

##### Wrong Thinking
* follow solutions of the gaints blindly
* be technical just for techniques
* try to solve all problems with techniques


#### Models of Large Site Architecture

##### Layer (horizontal)
* application layer: be responsible for specific bussiness and view showing (view, bussiness logic)
* service layer: provide service for application layer (data interfaces, logic processing)
* data layer: provide service data accessing and storage

##### Split (vertical)
split different functionalites and services into aggregated and decoupled modules.

##### Distributed
distributedly deploy the layered and splitted modules in different servers.

* distributed applications and services
* distributed static resources
* distributed data and storage
* distributed computing
* distributed configuration
* distributed lock
* distributed file system

##### Cluster
cluster the independent deployed server, i.e., many servers deployed the same application consists of a cluster.

##### Cache
* CDN
* reverse proxy
* local cache in application server
* distributed cache

##### Asynchronization
**distributed message queue**, is a typical producer-consumer model

* improve system availability
* speed up the response of website
* reduce the peek of concurrent accessing

##### Redundancy

* cold backup: storage archived in fixed period
* hot backup: separate read and write of database, real-time synchronization
* disaster recovery data center

##### Automation

* automatic releasing
  * automatic source control
  * automatic testing
  * automatic security dection
  * automatic deployment

* automatic monitoring
* automatic alerting
* automatic failure transferring
* automatic failure recoverring
* automatic downgrading
* automatic resource allocating

##### Security

* password
* verification code
* encryption
* filtering
* risk control

##### Architecture Model of Weibo
![](/images/17-10-29_87294532.jpg)


#### Keys of Large Site Architecture

##### Performance
* response time 
* throughput
* system performance monitor (top)

![](/images/17-10-29_24638084.jpg)

##### Availability 

* available time (99.99%)
* redundancy
* pre-released verification
* gray releasing

##### Scalability

easy to add and remove servers in cluster


##### Extensibility

* event driven architecture
* distributed service

##### Security

### Architecture

#### High-performance Architecture

##### Different Views of Website Performance

* user
  ![](/images/17-10-29_62774698.jpg)

* developer
  the performance of application itself and relevant subsystem.
  response latency, system throughput, concurrency, and system stability

* maintainer
  infrastructure performance, resource utilization

##### Metrics of Performance

* response time
  ![](/images/17-10-29_34403316.jpg)

* number of concurrency
  number of total users >> number of online users >> number of concurrent users

* throughput
  TPS(transaction per second), QPS(query per second), HPS(HTTP request per second)

* system performance monitor (top)
  system load, number of objects and threads, memory and CPU used, disk and netword I/O
  top: there floating number, recent 1min, 10mins, 15mins average running processes
  ![](/images/17-10-29_42727590.jpg)


##### Ways of Profiling

* performance testing
* load testing
* stress testing
* stability testing

![](/images/17-10-29_43135395.jpg)

![](/images/17-10-29_85052370.jpg)

* performance report
![](/images/17-10-29_25395924.jpg)

##### Strategy of Performance Optimization

* performance analysis
* performance optimization

##### Web Front Performance Optimization

* browser
  * reduce the number of HTTP requests
  * browser cache
  * enable compression
  * put CSS at the front of page, and JS at the bottom
  * reduce the transferring of Cookie
  
* CDN (Content Distribute Network)
  ![](/images/17-10-29_71736041.jpg)

* reverse proxy
  ![](/images/17-10-29_43823166.jpg)

##### Application Server Performance Optimization

**distributed cache**

* cache principle (80%-20% law)
  ![](/images/17-10-29_43534320.jpg)

* use cache properly
  * infrequently modified (read:write ≥ 2:1)
  * hot piece
  * set expired time
  
* cache availability
  * cache warm up
    preload the hot pieces
  * cache penetrating
    situation that requires to nonexistent data in high concurrency, one way is to cache it (nonexistent-null)

**architecture of distributed cache**

JBoss Cache: update synchronously (enterprise use)
![](/images/17-10-29_55001392.jpg)


Memcached: no communication between servers
![](/images/17-10-29_4636924.jpg)

* communication protocol: TCP, UDP, HTTP
  communication serializating protocol: text(XML, JSON), binary(Google Protobuffer)

* memcached use TCP for communication protocol, and it defines its own text serializating protocol.

* memcached's server communication module is based on `Libevent`.

* memory management
  * chunk-based allocation
    find a minimal chunk that can save the data
  * LRU
  ![](/images/17-10-29_21995999.jpg)


**asynchronizaton**
use message queue to reduce the peek
![](/images/17-10-29_94302977.jpg)

**cluster**

**code optimization**

* multi-thread
  number of threads = $\frac{task\ execution\ time}{task\ execution\ time-IO\ waiting\ time}\times CPU\ cores$
* thread-safe
  stateless object, local object, lock
* resource reusing
  singleton, object pool
* data structure
  hashtable: originlal-`MD5`->info figureprint-`HASH`->hashcode
* garbage collection
  object created in Eden-`Young GC`->From-`Young GC`->To-`Young GC`->From-...`threshold times Young GC`->Old->`Full GC`
  ![](/images/17-10-29_70621950.jpg)

**storage performance optimization**

* mechanical hard disk vs. solid state hard drive
* B+ tree vs. LSM tree
  N-branch search tree: at most 3 level, (maybe 5 disk IOs to update, 3 to get the index, 1 to read, 1 to write)
  ![](/images/17-10-29_64739055.jpg)
  N-level mergeable search tree: write operations do in memory, and create a new record in the $C_0$ tree
  ![](/images/17-10-29_65052291.jpg)

* RAID vs. HDFS
  ![](/images/17-10-29_86796256.jpg)
  ![](/images/17-10-29_41423906.jpg)


#### High-availability Architecture

##### Layered Architecture
application layer <- service layer <- data layer
more complicated:
![](/images/17-10-29_25459489.jpg)

##### High-availability Application

* failure transferring through load balancing
* session managemant
  a session is a semi-permanent interactive information interchange, i.e., a dialogue
  * session copy
  * session binding
  * use cookie to record session
  * session server
  ![](/images/17-10-29_80821252.jpg)
  
##### High-availability Service

* managed in priority
* time-out setting
* asynchronous call
* downgrade-abled service
* idempotent design
  i.e., repeated call can be handled properly.
  
##### High-availability Data

##### CAP Principe

* consistency
* availablity
* partition tolerance
![](/images/17-10-29_10107207.jpg)

**consistency**

* data strong consistency
  data is always consistent in all the physical copies
* data user consistency
  data may be not consistent in all the physical copies, but it
  can be accessed as a consistent and right one for user through 
  error correction and verification.
* data final consistency
  data may be not consistent in all the physical copies, and it
  may be not accessed consistently. but after some time, it can be
  corrected to user consistency.
  
##### Data Backup

##### Failure Transferring

* failure confirmation
  ![](/images/17-10-29_56937621.jpg)
* access transferring
* data recovering

##### Quality Insurance

**automatic releasing**
(gray releasing)
![](/images/17-10-29_67465635.jpg)

**automatic testing**

**pre-releasing verification**

**source control**

* master developing, branch releasing
* master releasing, branch developing

**website monitoring**

* user behavior log collection
  tools based on `Storm` (real-time computing framework)
* server performance monitoring
* running data report

**website monitoring control**

* system alerting
* failure transferring
* automatic downgrading


#### High-scalability Architecture

##### Physical Separation

（clustering)
![](/images/17-10-29_61171750.jpg)

##### Load Balancing

* HTTP redirection protocol
* DNS (domain name resolution)
* reverse proxy
* IP
  ![](/images/17-10-29_91517327.jpg)
* data link layer
  LVS (Linux Virtual Server)
  ![](/images/17-10-29_85037154.jpg)
  
**load balancing algorithms**

* round robin
* weighted round robin
* random
* least connections
* source hashing

##### Distributed Cache

**memchached access model**
![](/images/17-10-29_40831863.jpg)

consistent hashing
![](/images/17-10-29_34276778.jpg)

to solve the influence of cache load => `virtual nodes` (a server to 150 nodes)
![](/images/17-10-29_28184219.jpg)

##### Distributed Database

![](/images/17-10-29_59890444.jpg)

**spread tables into different database servers**
**put table into slices, then spread into different database servers**

database products of data slices: Amoeba, Cobar 

![](/images/17-10-29_80126287.jpg)
![](/images/17-10-29_18332945.jpg)

**NoSQL**

* abandon 2 basis of relation database: 
  SQL based on relation algebra, 
  and transaction consistency guarantee [atomicity, consistency, isolation, durability] (ACID)

* strengthen characteristics that large site concerned:
  high-availability, high-scalability

**Apache HBase**

![](/images/17-10-29_98641792.jpg)

![](/images/17-10-29_23962985.jpg)

#### High-extensibility Architecture

* event driven architecture
  ![](/images/17-10-29_9652231.jpg)
* distributed message queue
  Apache ActiveMQ
  ![](/images/17-10-29_81090792.jpg)
* distributed services

* web service and enterprise distributed service
  ![](/images/17-10-29_51015171.jpg)

* distributed service framework
  large site need simple and efficient distributed service 
  framework to build its service oriented architecture (SOA)
  it is said that Facebook manages its distributed service based on
  `Thrift` (an opensource remote service call framework)
  `Alibaba-Dubbo`
   ![](/images/17-10-29_67509941.jpg)

* extensible data structure
  NoSQL `ColumnFamily` (first in Google Bigtable)
  ![](/images/17-10-29_13082779.jpg)

* open platform to build website ecosystem
  ![](/images/17-10-29_78200842.jpg)


#### Security Architecture

##### Website attack and defense

**XSS(Cross Site Script) attack**

* reflective type
  ![](/images/17-10-29_41046481.jpg)

* persistent type
  ![](/images/17-10-29_42341545.jpg)

* solution: filter, HttpOnly

**Injection attack**
SQL injection, OS injection

SQL injection: open source(table name is public), error echoed, blind injection
![](/images/17-10-29_12246870.jpg)

solution: filter, parameter binding

**CSRF(Cross Site Request Forgety) attack**
![](/images/17-10-29_93927561.jpg)

solution: form token, verification code, referer check

**other attack**

* error echoed
* HTML comment
* file uploading
* path traversal

**web application firewall**
ModSecurity
![](/images/17-10-30_97390520.jpg)

** website security scanning**

##### Encryption and Key Security Management

* one-way hashing encryption
  MD5, SHA
  Rainbow Table to try to decrypt MD5
  ![](/images/17-10-30_70087109.jpg)

* symmetric encryption
  DES, RC
  ![](/images/17-10-30_72859252.jpg)

* asymmetric encryption
  RSA
  information security transmission, digital signature
  ![](/images/17-10-30_51192903.jpg)

* key security management
  ![](/images/17-10-30_44940270.jpg)

##### Infomation filtering and Anti-spam

* text matching
  double array trie, multi-level hashtable (simpler)
  ![](/images/17-10-30_84675517.jpg)

* classification algorithm
  Native Bayes, TAN, Association Rule Clustering System (ARCS)
  ![](/images/17-10-30_24237709.jpg)

* blacklist
  hashtable, bloomfilter
  ![](/images/17-10-30_32301588.jpg)

##### Risk Control

* rule engine
  ![](/images/17-10-30_25087873.jpg)
* statistics model
  ![](/images/17-10-30_76287040.jpg)

### Cases

#### Taobao
At first, Ma Yun bought a `C2C` website, then `LAMP`:
![](/images/17-10-30_37599252.jpg)

MVC: decouple view and bussiness logic
ORM (Object-relational mapping): decouple objects and relational database

Taobao didn't use the hot `Struts` and `Hibernate`, 
but choose to develop its own MVC frameword `Webx`, and to use `IBatis` for ORM.
Taobao also used `Weblogic` for application server, `Oracle` for database. They are commercial softwares. 
![](/images/17-10-30_959871.jpg)

Then, to use `Spring` instead of `EJB`, free `JBoss` instead of `Weblogic`

At last, abandon `Oracle`, `IBM`, `EMC`, and back to open source `MySQL` and `NoSQL`
![](/images/17-10-30_56259159.jpg)


#### Wikipedia
**based on** `LAMP`
![](/images/17-10-30_95474191.jpg)

**Wikipedia's web front**
the key architecture of  is `Squid` cluster:
![](/images/17-10-30_95191711.jpg)

**Wikipedia's backend**

* cache the format that application can be used directly
* cache servers store the session objects
* memcached's connection is cheap, and create one when needed
* increase memory to improve `MySQL`
* use `RAIO0` to speed up disk accessing
* set ACID of database at a some low level
* if `Master` database sever crashed, switch to `Slave`, 
  the close the write service, i.e., close the edition of users.

#### Doris (enormous distributed KV storage)
![](/images/17-10-30_90549351.jpg)
![](/images/17-10-30_91551116.jpg)
![](/images/17-10-30_33182040.jpg)
![](/images/17-10-30_47667517.jpg)
![](/images/17-10-30_2416629.jpg)
![](/images/17-10-30_96568966.jpg)
![](/images/17-10-30_15742744.jpg)
![](/images/17-10-30_91008989.jpg)
![](/images/17-10-30_14212433.jpg)

#### Seckilling System

##### chanllenge

* strike for the current bussiness
* high load of high concurrency
* increasing bandwidth
* the URL to place an order

##### solution

* independently deploy seckilling system
* static page for seckilling product
* rent netword bandwidth for seckilling
* dynamically generate random URL for placing order

##### architecture
![](/images/17-10-30_33308473.jpg)
![](/images/17-10-30_54626461.jpg)
![](/images/17-10-30_87372762.jpg)

#### Failure Analysis

##### disk space increases surprisingly
set log level to `DEBUG` by mistake.

##### high load of database
a SQL executes in the index page.

##### timeout failure in high concurrency
a singleton object need the unique lock to execute for a long time.

##### high load of database caused by cache
close the cache servers when releasing.

##### application start out of synchronization
Apache and JBoss start at the same time.
JBoss first, and `curl` to validate, then Apache.

##### big files occupy the disk IO
separate different sizes or types of files.

##### abuse of releasing environments
someone did performance testing in releasing environments.

##### non-standard releasing procedure
forget to uncomment some codes.
**commit after diff checking**.

##### bad programming habits
`NullPointerException` throws.
forget to check whether the object is `null`.

### Postscript
![](/images/17-10-30_1691765.jpg)













  



  
