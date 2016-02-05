title: maven仓库解析
tags:
- maven
- 仓库
date: 2016-02-05 17:55:00
---
<!-- toc -->
maven仓库有三种类型：
* local
* central
* remote

在maven 3.2.1的版本下，三个仓库的查找顺序为优先查找local，而central和remote顺序不一定，我试着运行了同一个java程序的编译多次，第一次总是从remote去下载，但是第二次有可能会从central下载。

因为从maven 3.0 开始，每个jar包目录下会有一个_remote.repositories的文件来记录jar的下载来源，而maven的effective pom也会有相关记录（需要试验确认一下），当remote和central都有同一个jar包时，由于两者不一致，有可能会导致编译不通过。


