---
title: hexo部署与使用入门
date: 2018-03-30 11:25:31
top: 1
categories:
- Hexo
tags:
- 个人网站部署
---

## 背景

一直想要有一个自己的博客，简约而清晰。但一开始苦于没有合适的平台，就一直没有搭建。    
最近在找资料的时候经常看到我这个模板的网站，就google了一下，开始了自己搭建网站之路。    

github pages 提供了服务器，hexo提供了博客框架
1. Github Pages 是什么
    如果你对编程有所了解，就一定听说过github。它号称程序员的Facebook，有着极高的人气，许多重要的项目都托管在上面。
    简单说，它是一个具有版本管理功能的代码仓库，每个项目都有一个主页，列出项目的源文件。
    但是对于一个新手来说，看到一大堆源码，只会让人头晕脑涨，不知何处入手。他希望看到的是，一个简明易懂的网页，说明每一步应该怎么做。因此，github就设计了[Pages功能](https://pages.github.com/)，允许用户自定义项目首页，用来替代默认的源码列表。所以，github Pages可以被认为是用户编写的、托管在github上的静态网页。

2. hexo 博客框架   
    有了github pages之后，你的静态页面能够托管了，但是如何编写和管理这些静态页面呢？
    就有了Hexo。而且支持使用Markdown模板生成静态页面，这正符合我的需求。

## Hexo安装与快速上手

[Hexo 官方文档](https://hexo.io/zh-cn/docs/index.html)

### 安装前提

安装 Hexo 相当简单。然而在安装前，您必须检查电脑中是否已安装下列应用程序：   
1. Git
2. Nodejs

### 安装与使用

``` bash
# 设置 npm 为国内镜像提升安装速度，如果你已经设置过了请忽略
npm config set registry https://registry.npm.taobao.org/

# 安装 Hexo
npm install -g hexo-cli

```

使用查看 [hello-world](/2018/03/30/hello-world)

### 申请github pages与hexo部署git

制作自己的github pages网页：[官方文档](https://pages.github.com/)

按照步骤等到自己的 https://yourname.github.io 网站

部署git：   
1. 按照 hexo-deployer-git:   
    `npm install hexo-deployer-git --save`
2. 修改 \_config.yml 中的 deploy参数：
    如下：
    ```
    deploy:
    type: git 
    repo: git@github.com:karellincoln/karellincoln.github.io.git
    branch: master
    ```

    具体参数看hexo官方文档。


## 配置与next主题

### 网站通用配置 

修改 \_config.yml 文件
1. 语言： `language: zh-Hans`
2. 主题： `theme: next`
3. 添加评论：   
    `disqus_shortname: your_disqus_shortname`   
    disqus需要翻墙，申请账号之后选 I want to install Disqus on my site    
    添加自己的shortname和在配置文件中添加关联的网站。    
    [详细教程](https://www.jianshu.com/p/c4f65ebe23ad)   
4. 添加资源文件夹：`post_asset_folder: true`   
    为true以后 `hexo new [layout] <title>` 会生成对应的title文件夹。在.md文件中可以使用相对路径引用title文件夹中的文件。   
    如：`[images](images.png)` 直接使用相对路径

### next 主题配置

[Next github网址与教程](https://github.com/iissnan/hexo-theme-next)

1. 下载NexT：   
    `git clone --branch v5.1.2 https://github.com/iissnan/hexo-theme-next themes/next`

2. 更改网址主题：如上2
3. 开启评论：
    ```
    # Disqus
    disqus:
      enable: true
      shortname: karellincoln
      count: true
    ```

4. 添加tags 和 categories(标签和目录)：   
    ``` bash
    hexo new page "tags"
    hexo new page "categories"
    ```

    [其他配置](https://github.com/iissnan/hexo-theme-next#tags-page)

5. 其他个人和插入配置


## Hexo个人网站的协同合作

在\_config.yml 中部署git之后，使用`hexo deploy` 会直接push到branch参数的分支（master）上。而push上去的网站的代码，源文档没有push上去，不利于多机或多人共同工作。

解决方案：   
1. 在yourname.github.io项目上添加新的分支保存源文档，便于管理。   
    做完hexo的安装和设置主题之后，在项目目录下执行    
    ``` bash
    git init
    git checkout -b newbranch
    git add -A 
    git commit -m "comment"
    git remote add origin "github project"

    git push -u origin newbranch
    ```



2. `git push` 会报错：因为当前的项目中有子git 项目Next，而没有对应的submodule信息。   
    为了方便直接删除next中的.git文件夹。
    add，commit之后重新push。    
    这里没有验证过，我是通过 `git submodule add ...` 方式添加子模块解决的。

3. 为了方便，在github官网将newbranch设置为默认分支。

4. 到了新的电脑上时，我们需要将项目先下载到本地，然后再进行hexo初始化。   
    ``` bash
    git clone https://github.com/yourname/yourname.github.io.git
    cd yourname.github.io
    npm install hexo
    npm install
    npm install hexo-deployer-git –save
    ```

## 使用markdown和一些有用的标签插件

1. Front-matter
Front-matter 是文件最上方以 `---`分隔的区域，用于指定个别文件的变量，举例来说：   
    ```
    title: Hello World
    date: 2013/7/13 20:46:25
    categories:
    - Diary
    - Life
    tags:
    - PS3
    - Games
    ```

    其中tags标签下的 PS3 和Games是平级的，但是categories 下的Diary和Life是有层级的相当于Diary/Life目录下。

1. 我觉得有一个经常可能需要用到的一个，引用自己其他篇文章，能不能直接引用当前文件夹的title.md文件呢？    
    结果发现不可以！！！！    
    需要采用 `/year/month/day/title` 的方式引用自己的文章。     

2. [标签插件](https://hexo.io/zh-cn/docs/tag-plugins.html)   

