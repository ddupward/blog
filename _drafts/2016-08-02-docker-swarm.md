# Docker Swarm的简单集群搭建 #

## 准备 ##

- Docker 1.4.0 及以上 （确保用于搭建Swarm集群的Docker版本一致）
- 三个及以上服务器节点 （两台node，一台manager）
- 本文中所用到的是：
 1. Docker Version 1.12.0 build 8eab29e
 2. 三台物理服务器（CentOS 7.1）
	- manager:192.168.10.181
	- nodes:192.168.10.182、192.168.10.183

## 搭建步骤 ##
### 1、启动Docker daemon ###
    $ docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock &

### 2、从官方库中下载swarm镜像 ###
    $ docker pull swarm

### 3、在manager上生成集群token ###
    $ docker run --rm swarm create
    82413e81ed51fbad288f5cf666e56cab
82413e81ed51fbad288f5cf666e56cab即为集群的token

### 4、开始添加nodes到集群中，使用上面生成出的token###
node节点上：

    $ docker run -d swarm join --addr=192.168.10.182:2375 token://82413e81ed51fbad288f5cf666e56cab
    $ docker run -d swarm join --addr=192.168.10.183:2375 token://82413e81ed51fbad288f5cf666e56cab
### 5、查询集群nodes添加情况 ###
manager节点上：
    
    $ docker run --rm swarm list token://82413e81ed51fbad288f5cf666e56cab
	192.168.10.183:2375
	192.168.10.182:2375
我们可以看到，两个节点已经成功添加到集群中了

###6、安装集群管理工具 ###
本文在manager节点开启管理功能，当然也可以任选一个节点，安装集群管理工具
    
    $ docker run -d -p 2345:2375 swarm manage token://6856663cdefdec325839a4b7e1de38e8
端口2345可以自定义，以后可以通过manager节点IP+端口来访问管理工具

### 7、查看集群信息###
    $ docker -H 192.168.10.181:2345 info
    Containers: 7
     Running: 2
     Paused: 0
     Stopped: 5
    Images: 4
    Server Version: swarm/1.2.4
    Role: primary
    Strategy: spread
    Filters: health, port, containerslots, dependency, affinity, constraint
    Nodes: 2
     bdcn-c02: 192.168.10.182:2375
      └ ID: ITJG:XSES:337L:DZS2:EA2C:G7U3:6ILU:MMQS:M7KI:5TX7:FW3A:HO2U
      └ Status: Healthy
      └ Containers: 3 (1 Running, 0 Paused, 2 Stopped)
      └ Reserved CPUs: 0 / 8
      └ Reserved Memory: 0 B / 16.28 GiB
      └ Labels: kernelversion=3.10.0-327.22.2.el7.x86_64, operatingsystem=CentOS Linux 7 (Core), storagedriver=devicemapper
      └ UpdatedAt: 2016-08-02T07:30:58Z
      └ ServerVersion: 1.12.0
     bdcn-c03: 192.168.10.183:2375
      └ ID: QKKQ:3IPD:MPYZ:T64H:ANEU:Q4OI:W5EH:SI63:VHPI:WNQJ:GAJ7:E5EA
      └ Status: Healthy
      └ Containers: 4 (1 Running, 0 Paused, 3 Stopped)
      └ Reserved CPUs: 0 / 8
      └ Reserved Memory: 0 B / 16.28 GiB
      └ Labels: kernelversion=3.10.0-327.22.2.el7.x86_64, operatingsystem=CentOS Linux 7 (Core), storagedriver=devicemapper
      └ UpdatedAt: 2016-08-02T07:31:19Z
      └ ServerVersion: 1.12.0

###8、在集群上运行容器 ###

    $ docker -H 192.168.10.181:3333 run -d --name h1 hello-world
    $ docker -H 192.168.10.181:3333 run -d --name h2 hello-world
    $ docker -H 192.168.10.181:3333 run -d --name h3 hello-world
    $ docker -H 192.168.10.181:3333 run -d --name h4 hello-world
    $ docker -H 192.168.10.181:3333 run -d --name h5 hello-world
    $ docker -H 192.168.10.181:3333 run -d --name h6 hello-world
    $ docker -H 192.168.10.181:3333 run -d --name h7 hello-world
    $ docker -H 192.168.10.181:3333 run -d --name h8 hello-world

查看集群容器情况：

    $ docker -H 192.168.10.181:3333 ps -a
    CONTAINER ID   IMAGE   COMMAND  CREATED STATUS  PORTS   NAMES
    096b0535e3bd hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c03/h8
    e632c3905cc1 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c03/h7
    5704a45af15d hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c02/h6
    74b3d4a0c809 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c02/h5
    a8431dccf348 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c03/h4
    5fa57e9629c6 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c03/h2
    9336caa525b9 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c03/h1
    21a8316990b7 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   bdcn-c02/h3
    

192.168.10.182上容器情况：

    $ docker ps -a
    CONTAINER ID   IMAGE   COMMAND  CREATED STATUS  PORTS   NAMES
    5704a45af15d hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   h6
    74b3d4a0c809 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   h5
    21a8316990b7 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   h3
	9336caa525b9 hello-world "/hello" 22 seconds ago  Exited (0) 22 seconds ago   h1

192.168.10.183上容器情况：

    $ docker ps -a
    CONTAINER ID   IMAGE   COMMAND  CREATED STATUS PORTS   NAMES
    096b0535e3bd hello-world "/hello" 22 seconds ago   Exited (0) 22 seconds ago   h8
    e632c3905cc1 hello-world "/hello" 22 seconds ago   Exited (0) 22 seconds ago   h7
    a8431dccf348 hello-world "/hello" 22 seconds ago   Exited (0) 22 seconds ago   h4
    5fa57e9629c6 hello-world "/hello" 22 seconds ago   Exited (0) 22 seconds ago   h2

可以看出，我们通过Docker Swarm manager的管理，容器已经平均分布在了两台nodes上面了。

## 搭建中可能遇到的问题 ##

###集群info中 Status: Unhealthy/Pending###
     bdcn-c02: 192.168.10.182:2375
      └ ID: ITJG:XSES:337L:DZS2:EA2C:G7U3:6ILU:MMQS:M7KI:5TX7:FW3A:HO2U
      └ Status: Unhealthy
      └ Containers: 5
      └ Reserved CPUs: 0 / 8
      └ Reserved Memory: 0 B / 16.28 GiB
      └ Labels: kernelversion=3.10.0-327.22.2.el7.x86_64, operatingsystem=CentOS Linux 7 (Core), storagedriver=devicemapper
      └ Error: Cannot connect to the Docker daemon. Is the docker daemon running on this host?
      └ UpdatedAt: 2016-08-02T08:17:25Z
      └ ServerVersion: 1.12.0

报错信息：
Error: Cannot connect to the Docker daemon. Is the docker daemon running on this host?
造成原因有两个：
1. docker没有以daemon方式启动：`$ docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock &`
使用systemctl start docker命令来启动docker的话，需要修改文件‘/etc/default/docker’
添加一行：
    `DOCKER_OPTS="-H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock"`
重启docker服务后，
	`$ netstat -apn|grep 2375`
看到::2375即为成功，若无结果说明端口未正确监听到。
强烈建议使用docker daemon -H的方式启动，使用服务启动docker，我没有成功○|￣|_

2. 如果成功开启了监听，但是仍然显示不能填上daemon的话，尝试检查下firewalld服务是否有开启。
firewalld是CentOS7添加的防火墙服务，从CentOS6升级上来的用户十分不喜欢这水土不服的防火墙。既然docker的转发默认使用iptables，那么我们就把firewalld禁用掉。

    `$ systemctl stop firewalld`
    `$ systemctl disable firewalld`

3. 之前我一直认为，把iptables全权交给docker来管理，防火墙转发、开放规则就会万无一失，结果我错了。docker并没有按照我们的意愿，放开自己的2375端口，需要在/etc/sysconfig/iptables中手动添加一条：
`-A INPUT -p tcp -m state --state NEW -m tcp --dport 2375 -j ACCEPT`
重启两台node的iptables服务。




    
    



    

