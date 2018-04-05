---
title: 关系型数据库之范式
top: 1
date: 2018-04-04 22:37:10
categories:
- 分布式
- 关系型数据库
tags:
- 范式
---


<div class="note info"><p>关系数据库设计的目标是生成一组关系模式
使我们存储信息时避免不必要的冗余，并且让我们可以方便地获取信息。
这是通过设计满足适当范式（normal form）的模式来实现的。</p></div>

<!-- more -->

## 好的关系设计的特点

一开始的表一般都是比较大的，因为人是懒惰的喜欢不加思索的将很多属性放到一起，而形成一张大表。

<div class="note default"><p>举例：表A(a,a1,b)和表B(b, b1)是一对多的关系，分别有主码a和b。
如果现在将AB连接成一张大表A-B(a,a1,b,b1)。那么A-B将有大量的冗余的b1属性值。</p></div>

而这样的设计会带来什么呢？
1. 冗余的b1信息，增加了存储，而且当要修改一个b所对应的b1值的时候会需要修改多行。这样的就要保证更复杂的事务原子性。
2. 使用A-B表，当要添加一个新的(b, b1)对时，由于没有A与之对应，这时就要处理空值问题了。


### 那什么样的表需要分解呢？
* 函数依赖(functional dependency)：如果存在模式（b，b1），其中b可以作为主码。则存在`b-->b1`的函数依赖关系
* 多值依赖(multivalued dependency): 比较难直接讲清楚。
    ![定义](multivalued-dependency.jpg)
    直观的说是a和b之间的关系独立于a和R-b之间的关系
    ![解析](multivalued-dependency-analy.jpg)
    可以认识到函数依赖是多值依赖的一种特殊情况，如果其中的一个关系是一对一的(即每一个a只有一个b或c与之对应)。
    根据多值依赖的分解满足无损分解。

任何一个函数依赖都是一个多值依赖。
一个表关系中存在不合理的函数依赖和多值依赖就是需要分解的。

### 如何判断分解的好坏呢？
* 有损分解(lossy decomposition)和无损分解(lossless decomposition)：有损分解是指将一个大表分解之后再使用自然连接所形成的表增加了一些元组，使得原来的一些对应关系丢失了。反之称为无损分解。
* 保持依赖(dependency preservation): 一个函数依赖同时也可以理解成为一个关系约束，如果关系模式中能够高效地检查这些约束是很有用的，称这个关系模式是保持依赖的。

有损分解是表的分解{% label primary@必须%}要保证的，但是保持依赖可以看情况，有时候可以通过{% label primary@物化视图%}高效的检查（但是也不是很高效）

## 范式

有了上面的知识之后，理解范式就变成能简单了。

### 第一范式 1NF

#### 定义：
一个域是原子的(atomic)， 如果该域的元素被认为是不可分割的单元。我们称一个关系模型R属于第一范式，如果R的所有属性的域都是原子的。

#### 理解：

原子的，是关系模型设计者的思想，在实现方面认为可以由{% label primary@固定的空间 %}表示而不是固定空间的集合或组合。
非原子的域主要和组合属性（嵌套关系）和多值属性（包含值得集合）：这些在表达上可能会更清晰一些但是都是不好的关系模型（产生冗余和修改的困难），SQL语句也不支持这种语法。

### Boyce-Codd 范式

#### 定义：
![定义](BCNF.png)

#### 理解：
根据我们分解关系表的最直接的要求，可以得到如果一个非平凡函数依赖中的a不是模式R的超码时，会产生多余的冗余信息。

但是这样的实现并不能一定保持依赖。同时也不能将多值依赖给分解了。保持依赖需要{% label primary@加入分解约束%}得到3NF，而多值依赖需要{% label primary@强化分解条件%}得到4NF。

#### 实现：

为了得到依赖闭包和实现3NF，引入函数依赖理论，属性集的闭包和正则覆盖(canonical cover)。
其中正则覆盖值得是与原函数依赖F拥有相同闭包的最小函数依赖集。
具体定义：
![正则覆盖](canonical-cover.jpg)

BCNF的实现：
![BCNF的实现](BCNF-algorithm.jpg)

### 其他范式

#### 定义：

3NF：
![3NF](3NF.jpg)

4NF:
A relation schema r(R) is in fourth normal form (4NF) with respect to a set D of functional and multivalued dependencies if, for all multivalued dependencies in D+ of the form  →→ , where  ⊆ R and  ⊆ R, at least one of the following holds:
*  →→  is a trivial multivalued dependency.
*  is a superkey for R.

#### 实现：

3NF：
![3NF-al](3NF-algorithm.jpg)

4NF：实现基本同BCNF。


<hr />

后面的每个范式没有具体讲了，因为觉得在好的关系设计中将主要的设计问题需要考虑的已经说清楚了。
具体的参见课本。

<div class="note primary"><p>参考文章：
《数据库系统概念（第六版）》
[SQL 第一范式、第二范式、第三范式、BCNF](https://cnbin.github.io/blog/2015/12/12/sql-di--fan-shi-,-di-er-fan-shi-,-di-san-fan-shi-bcnf/)
</p></div>
