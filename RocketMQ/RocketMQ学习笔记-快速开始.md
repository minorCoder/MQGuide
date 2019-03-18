# RocketMQ学习笔记-快速开始

## 快速开始

快速介绍教程主要介绍快速设置RocketMQ消息系统在你本地的机器中发送和接受消息。

## 必要条件

下面的软件是假定你已经安装了的：

1. 64bit OS, Linux/Unix/Mac is recommended;
2. 64bit JDK 1.8+;
3. Maven 3.2.x;
4. Git;
5. 4g+ free disk for Broker server

## 下载和编译发布的版本

点击[这里](https://www.apache.org/dyn/closer.cgi?path=rocketmq/4.4.0/rocketmq-all-4.4.0-source-release.zip)下载4.40源代码的发布版本，也可以点击[这里](<http://rocketmq.apache.org/release_notes/release-notes-4.4.0/)下载二进制运行的发布版本

然后执行下面的命令解压缩4.40源码版本的，然后构建它的二进制版本。

~~~shell
  > unzip rocketmq-all-4.4.0-source-release.zip
  > cd rocketmq-all-4.4.0/
  > mvn -Prelease-all -DskipTests clean install -U
  > cd distribution/target/apache-rocketmq
~~~

## 启动 Name Server

```shell
 > nohup sh bin/mqnamesrv &
 > tail -f ~/logs/rocketmqlogs/namesrv.log
  The Name Server boot success...
```

## 启动 Broker

```shell
  > nohup sh bin/mqbroker -n localhost:9876 &
  > tail -f ~/logs/rocketmqlogs/broker.log 
  The broker[%s, 172.30.30.233:10911] boot success...
```

## 发送和接受消息

在发送/接受消息之前，我们需要告诉客户端当前的name servers。RocketMQ 提供多种办法实现这个。为了简便，我们使用环境变量`NAMESRV_ADDR`

```shell
 > export NAMESRV_ADDR=localhost:9876
 > sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
 SendResult [sendStatus=SEND_OK, msgId= ...

 > sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
 ConsumeMessageThread_%d Receive New Messages: [MessageExt...
```

## 关闭 Servers

```shell
> sh bin/mqshutdown broker
The mqbroker(36695) is running...
Send shutdown request to mqbroker(36695) OK

> sh bin/mqshutdown namesrv
The mqnamesrv(36664) is running...
Send shutdown request to mqnamesrv(36664) OK
```