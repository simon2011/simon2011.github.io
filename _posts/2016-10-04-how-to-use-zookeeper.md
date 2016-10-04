---
layout: post
title: zookeeper单机多实例部署
tags:
- zookeeper
- bigdata
categories: zookeeper
description: ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，它包含一个简单的原语集，分布式应用程序可以基于它实现同步服务，配置维护和命名服务等。
---
##主题介绍
介绍zookeeper单机多实例部署,更多适合于实验性质；ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，它包含一个简单的原语集，分布式应用程序可以基于它实现同步服务，配置维护和命名服务等。

<!-- more -->
##准备工作
- **Linux环境**

    个人使用虚拟机ubuntu的系统, ubuntu下载地址  http://www.ubuntu.com/download

- **JDK安装**

    zookeeper需要java运行环境，这个建议1.6以上，配置好 JAVA_HOME 、CLASSPATH、 PATH 变量。jdk下载地址：http://www.oracle.com/technetwork/java/javase/downloads/index.html

    环境变量配置：
    ```
    export JAVA_HOME="/usr/lib/jvm/jdk1.7.0_40"
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export ZOOKEEPER_HOME="/data/apache/zookeeper"
    export PATH="$PATH:$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin"
    
    ```
- **zookeeper安装**
 
    zookeeper安装包：http://zookeeper.apache.org/releases.html 下载稳定版本，现在的版本是3.4.5


###端口检查
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通常使用的端口 2181，2888，3888 ，检查是否占用，占用则下面的配置中更改端口。检查命令：

    netstat -ano | grep 2181
   
###单机多实例配置及启动
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;切换到zookeeper配置文件目录,使用命令
    ```
  mv zoo_sample.cfg  zoo.cfg
    
    ```
    
配置文件内容

 ```
 tickTime=2000
 initLimit=5
 syncLimit=5
 dataDir=/data/apache/zookeeper/data  # 目录需要手工建立，存放 zk 数据，主要是快照
 clientPort=2181
 # dataLogDir事务日志存放目录，最好配置，事务日志的写入速度严重影响zookeeper的性能
 dataLogDir=/data/apache/zookeeper/datalog
 server.1=192.168.130.170:2889:3889
 server.2=192.168.130.170:2890:3890
 server.3=192.168.130.170:2891:3891
    
  ```
    
 拷贝配置文件，生成三个配置文件：zoo-1.cfg、zoo2.cfg 、zoo3.cfg。

  zoo1.cfg需要为dataDir和dataLogDir设置目录，改动内容如下：
```
dataDir=/data/apache/zookeeper/data/zoo1
dataLogDir=/data/apache/zookeeper/datalog/zoo1
clientPort=2181
    
```
    
zoo2.cfg :

 ```
dataDir=/data/apache/zookeeper/data/zoo2
dataLogDir=/data/apache/zookeeper/datalog/zoo2
clientPort=2182
    
```
zoo3.cfg :

 ```
dataDir=/data/apache/zookeeper/data/zoo3
dataLogDir=/data/apache/zookeeper/datalog/zoo3
clientPort=2183
    
```
如上配置相同的本机IP，不同的端口号，这里配置了三个实例

区分到底是第几个实例呢，就要有个id文件，且名字必须是myid
 ```
echo "1" > /data/apache/zookeeper/data/zoo1/myid
echo "2" > /data/apache/zookeeper/data/zoo2/myid
echo "3" > /data/apache/zookeeper/data/zoo3/myid
    
```
启动所配置的实例
```
. zkServer.sh start zoo1.cfg 
. bin/zkServer.sh start zoo2.cfg 
. bin/zkServer.sh start zoo3.cfg
```
查看zookeeper选出来的leader，通过下面的脚本，分别指定配置文件，就可以查看哪一个实例是leader：
```
. zkServer.sh status zoo1.cfg 
```
综上所述是zookeeper的单机多实例集群方式部署
