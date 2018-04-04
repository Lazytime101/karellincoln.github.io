---
title: MongoDB存储与查询机制
date: 2018-04-03 20:34:59
top: 1
categories:
- 分布式
- NoSQL
tags:
- MongoDB
---


## 前言

这篇是 [NoSQL数据库技术及其应用研究](/2018/04/03/NoSQL数据库技术及其应用研究) 的下篇，也是主要引用 NoSQL数据库技术及其应用研究 论文。



## 存储机制与反范式模式设计

### 数据模型

1. 逻辑模型：   
    一个MongoDB系统由多个数据库组成，每个数据库有一组集合(collection) 组成，每个集合由任意个文档（Document）组成，而每个文档由一系列字段（Field）组成，每个字段是一个键值对（key-value pair），其中key是字段的名称，value是对应的属性值，
    <div align="center"><img src="MongoDB数据模型.jpg" width="200"></div>
2. 物理模型：   
    一个数据库在文件中的存储，其中webinfo是数据库名
    ![MongoDB数据库文件结构](MongoDB数据库文件结构.jpg)

    .ns 文件是命名空间文件，存储的是一个哈希表，存储每个子命名空间的元数据。
    .0 等是数据文件，新的数据文件比前一个文件大一倍，最大为2GB。数据文件有多个数据块（extent）组成，数据块由多个数据记录（record）组成。extent和record都是双向链表，record是存储的最小单位，存储BSON格式的数据。

使用GridFS文件系统规范，提供一种透明的机制将一个大文件分割成为多个较小的块对象（chunk）存储。**这个还没有理解**

### 存储架构

分为客户端和服务器，在服务器中运行Mongod提供服务，客户端应用程序通过调用 Driver API， 使用Mongo Wire Protocol通讯协议向mongod提交请求并获得应答。

### 反范式模式设计

关系型数据库的规范化理论提出了1NF, 2NF, 3NF, BCNF, 4NF 的范式概念，基本思想是通过模式分解来逐步消除数据依赖中不合适的部分，使模式中的各关系模式达到某种程度的分离。

