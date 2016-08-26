---
layout: archive
title:  "Docker Swarm mode 集群的搭建和使用"
date:   2016-08-26
author: lijie
categories: docker swarm service
---

## 准备 ##

- Docker 1.12.0 及以上 （确保用于搭建Swarm集群的Docker版本一致）
- 三个及以上服务器节点
- 本文中所用到的是：
 1. Docker Version 1.12.0 build 8eab29e
 2. 三台物理服务器（CentOS 7.1）
	- manager:192.168.10.181
	- nodes:192.168.10.182、192.168.10.183

## 搭建步骤 ##

### 1、启动Docker daemon （每个节点）###

    $ docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock &
### 2、选择一个节点，建立一个Swarm Manager ###

    $ docker swarm init --advertise-addr 192.168.10.181
    Swarm initialized: current node (dsaxvxxnkv6ngrim305f0ubfx) is now a manager.
    
    To add a worker to this swarm, run the following command:
    docker swarm join \
    --token SWMTKN-1-1mw4m1geuldbfgm63cokadlu8ialptujb09j0w3mby1ocm6bd0-6rbak3tmc0x6kcvpqh6ka1qnp \
    192.168.10.181:2377
    
    To add a manager to this swarm, run the following command:
    docker swarm join \
    --token SWMTKN-1-1mw4m1geuldbfgm63cokadlu8ialptujb09j0w3mby1ocm6bd0-b6hns9xm9km87e22gwnm3laa2 \
    192.168.10.181:2377
参数 --advertise-addr 填写manager的ip，也就是你选择节点的ip
同时，docker给出了添加manager和join的token，后续需要使用到。
当然，也可以用下面命令：
    
	$ docker node ls
    ID   HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
    dsaxvxxnkv6ngrim305f0ubfx *  bdcn-c01  Ready   Active Leader


### 3、继续添加节点到Swarm中 ###

添加一个worker node，我们转到另两个服务器：执行上面提示的命令：
    $ docker swarm join --token SWMTKN-1-1mw4m1geuldbfgm63cokadlu8ialptujb09j0w3mby1ocm6bd0-6rbak3tmc0x6kcvpqh6ka1qnp 192.168.10.181:2377
    This node joined a swarm as a worker.
完毕后再去manager节点查看：

    $ docker node ls
    ID   HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
    6sg17tbuqsrk6azwqgyv5os5e bdcn-c03  Ready   Active
    chrsiuuz9uex4oudcmxvys856 bdcn-c02  Ready   Active
    dsaxvxxnkv6ngrim305f0ubfx *  bdcn-c01  Ready   Active Leader

Swarm mode集群搭建完毕，manager为192.168.10.181，worker为192.168.10.182、192.168.10.183。
现在，我们来看下manager节点上的docker运行状态：
    
	$ docker info
    Containers: 4
     Running: 2
     Paused: 0
     Stopped: 2
    Images: 9
    Server Version: 1.12.0
    Storage Driver: devicemapper
    ---参数信息省略---
    Swarm: active
     NodeID: dsaxvxxnkv6ngrim305f0ubfx
     Is Manager: true
     ClusterID: 72erv1y667wylbyczji3vatnu
     Managers: 1
     Nodes: 3
     Orchestration:
      Task History Retention Limit: 5
     Raft:
      Snapshot interval: 10000
      Heartbeat tick: 1
      Election tick: 3
     Dispatcher:
      Heartbeat period: 5 seconds
     CA configuration:
      Expiry duration: 3 months
     Node Address: 192.168.10.181
    Runtimes: runc
    Default Runtime: runc
    Security Options: seccomp
    ---参数信息省略---

----------

## 在Swarm Mode集群上面运行一个服务 ##

### 1、 hello-world ping###

    $ docker service create --replicas 1 --name helloworld alpine ping docker.com
    8l2vt8iq80sv1ilvauz5pf9r2
参数 --replicas 编译
### 2、查看正在运行的服务 ###

    $ docker service ls
    ID NAME REPLICAS  IMAGE   COMMAND
    8l2vt8iq80sv  helloworld  1/1   alpine  ping docker.com
### 3、查看某个服务的详细信息 ###

    $ docker service inspect --pretty helloworld
    ID:		8l2vt8iq80sv1ilvauz5pf9r2
    Name:		helloworld
    Mode:		Replicated
     Replicas:	1
    Placement:
    UpdateConfig:
     Parallelism:	1
     On failure:	pause
    ContainerSpec:
     Image:		alpine
     Args:		ping docker.com
    Resources:
### 4、查看某个服务的运行情况 ###

    $ docker service ps helloworld
    ID NAME  IMAGE   NODE  DESIRED STATE  CURRENT STATE   ERROR
    9r7iwqv6oxeyehfl6pe9kheux  helloworld.1  alpine  bdcn-c03  Running Running 14 minutes ago
### 5、Swarm 集群的高可用 ###

通常情况下，manager节点同时具有worker节点的职能。我们可以通过添加manager节点的数量，来提高整个Swarm集群的容错性。
当然，如果Swarm manager节点工作负载比较大，我们可以让manager节点只做manager，使它不具备worker节点的职能。
让manager专注做manager的事情，可以使用命令：
    
    $ docker node update --availability drain <NODE-ID>

manager节点的恢复：
集群中大于一半以上数量的manager存活（(N/2)+1）

    $ docker swarm init --force-new-cluster --listen-addr <nodeip>:2377
执行完毕以后，该节点会丢弃本节点之前的信息，重新继承现在集群的数据和工作





