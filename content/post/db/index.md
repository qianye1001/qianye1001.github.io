---
title: 数据库类型
description: 介绍一些数据库的类型和其优缺点
slug: db-classification
date: 2023-05-01 12:00:00+0000
categories:
    - 数据库
tags:
    - Mysql
    - Redis
    - Elastic
    - DB
---

# 1. KEY-VALUE
> 例子：Redis，Memocashed， etcd

特征：

- 数据本身存储的结构类似于Map<String,Object>或者python的字典
- 有一组KEY，且每一个KEY都是唯一的，指向某个值
- Redis等数据库数据存储于内存中

优点：

- 内存io远远快于硬盘io （通常单次操作耗时在亚微秒级）

缺点：

- 内存为易丢失性存储
- 无法进行join等操作

使用：

- 多数时候作为缓存的角色来降低数据io的延迟
- 也可以用来做消息队列、发布订阅等需求
# 2.WIDE COLUMN
> 例子：Cassandra， Hbase

特征：

- 可以看作给KEY-VALUE型数据库的增加了一个维度
- 有一组KEY，且每一个KEY都是唯一的，一个KEY可以对应多个列族，每个族群对应一列有序的数据

优点：

- 可以处理非结构化的数据
- 可以执行数据的聚合操作
- 采用于SQL类似的CQL进行查询，学习成本不高
- 去中心化存储，支持横向扩展

缺点：

- 无法进行join等操作

使用：

- 多数用字啊经常写却不经常读取的场景里
- 处理时间相关数据较优秀
# 3.DOCUMENT
> 例子：MongoDB， FireStore

特征：

- 需要建立docuemnt，每个文档都是KEY-VALUE数据的容器
- 非结构化，不需要Schema
- docuemnts聚合成为一个Collection，Collection中的字段可以 被索引，docuemnt可以具有上下层次关系

优点：

- 一定程度上可以对关系型数据进行建模和读写
- 读数据会很快

缺点：

- 无法进行join等操作
- 写数据较为复杂

使用：

- 已经较为通用，可应用于内容管理，游戏开发等场景
# 4.RELATIONAL
> 例子：Mysql，SQLServer

特征：

- 采用SQL语句（structured query language）
- 每个表存储单一的类型，大部分时候都会有唯一在主键
- 通过外键将不同的表中的数据联合起来
- 数据以最小单元，按照特定的格式统一存储

优点：

- ACID 保证事务的有效性
- join查询

缺点：

- 需要首先确定Schema
- 难以规模化
# 5.GRAPH
> 例子：neo4j

特征：

- 数据的关系被视为图的边

优点：

- 多对多的关系建立简单
- 大数据量时候性能相较于SQL更好

用处：

- 在存储关系为核心的数据时
# 6.SEARCH
> 例子：elastic

特征：

- 对文本进行全文索引

优点：

- 文本全局搜索速度快

用处：

- 搜索
