---
layout: post
title: Oracle and Mysql varchar length
date: 2018-11-11 16:30
author: admin
comments: true
categories: [database]
tags: [oracle,mysql,database]
---

One of my collegues come across the problem. I gived her the answer and conclude it here!

# MySQL 列长度
- 5.0.3版本之前：0~255 bytes
- 5.0.3版本及之后：0~65535 bytes，注意又分以下三种
    - 上限VARCHAR(65535) //单字节
    - 上限VARCHAR(21844) //utf8 编码方式
    - 上限VARCHAR(16383) //utf8mb4 编码方式

# Oracle 列长度
- Oracle 10g/11g varchar2 取值范围: 1≤LENGTH≤4000 bytes/characters
- Oracle 12c 有两个版本 varchar2 取值范围: 
    - STANDARD版  1≤LENGTH≤4000 bytes/characters
    - EXTENDED版  1≤LENGTH≤32767 bytes/characters
