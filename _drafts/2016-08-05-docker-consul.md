# Docker consul集群的搭建 #
## 准备 ##

- Docker 1.4.0 及以上 （确保用于搭建Swarm集群的Docker版本一致）
- 三个及以上服务器节点
- 本文中所用到的是：
 1. Docker Version 1.12.0 build 8eab29e
 2. 三台物理服务器（CentOS 7.1）
	- manager:192.168.10.181
	- nodes:192.168.10.182、192.168.10.183

## 搭建步骤 ##
### 1、启动Docker daemon （每个节点）###
    $ docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock &

### 2、建立consul container Volume文件夹 （可省略） ###
    $ mkdir -p /data/consul/{data,conf}
    $ chown -R consul: /data/consul

### 3、下载consul镜像 ###
    $ docker pull consul:latest

### 4、运行consul server container ###

192.168.10.181上执行：

    $ docker run -d \
    -p 8300:8300 \
    -p 8301:8301 \
    -p 8301:8301/udp \
    -p 8302:8302 \
    -p 8302:8302/udp \
    -p 8400:8400 \
    -p 8500:8500 \
    -p 8600:53 \
    -p 8600:53/udp \
    -h consul-s1 \
    -v /data/consul/data:/consul/data \
    -v /data/consul/conf:/consul/conf \
    --name=consul-s1 consul:latest \
    agent -server -bootstrap-expect=1 -ui \
    -config-dir=/consul/conf \
    -client=0.0.0.0 \
    -node=consul-s1 \
    -advertise=192.168.10.181

192.168.10.182上执行：
    
    $ docker run -d \
    -p 8300:8300 \
    -p 8301:8301 \
    -p 8301:8301/udp \
    -p 8302:8302 \
    -p 8302:8302/udp \
    -p 8400:8400 \
    -p 8500:8500 \
    -p 8600:53 \
    -p 8600:53/udp \
    -h consul-s2 \
    -v /data/consul/data:/consul/data \
    -v /data/consul/conf:/consul/conf \
    --name=consul-s2 consul:latest \
    agent -server -ui \
    -config-dir=/consul/conf \
    -client=0.0.0.0 \
    -node=consul-s2 \
    -join=192.168.10.181 \
    -advertise=192.168.10.182
    
192.168.10.183上执行：
    
    $ docker run -d \
    -p 8300:8300 \
    -p 8301:8301 \
    -p 8301:8301/udp \
    -p 8302:8302 \
    -p 8302:8302/udp \
    -p 8400:8400 \
    -p 8500:8500 \
    -p 8600:53 \
    -p 8600:53/udp \
    -h consul-s3 \
    -v /data/consul/data:/consul/data \
    -v /data/consul/conf:/consul/conf \
    --name=consul-s3 consul:latest \
    agent -server -ui \
    -config-dir=/consul/conf \
    -client=0.0.0.0 \
    -node=consul-s3 \
    -join=192.168.10.181 \
    -advertise=192.168.10.183

验证：
    `$ curl 192.168.10.181:8500/v1/status/peers`
    `["192.168.10.183:8300","192.168.10.181:8300","192.168.10.182:8300"]`

### 5、安装带有Consul的Swarm 集群  ###
swarm manager 安装在192.168.10.181上：
    
    $ docker run -d \
    -p 3375:3375 \
    --name swarm-manager \
    swarm:latest manage --host tcp://0.0.0.0:3375 consul://192.168.10.181:8500

swarm agent 安装在192.168.10.182~183上：
    
    $ docker run -d \
    --name swarm-agent \
    swarm:latest join --addr 192.168.10.181:2375 consul://192.168.8.182(3):8500

----------

通过consul的Web UI界面看到swarm服务已经被发现了，位于LOCK SESSIONS下：


![](http://i.imgur.com/67asn7L.png)

----------
![](http://i.imgur.com/5zWkt5m.png)

由于Swarm容器下面带有consul，所以我们很轻易的consul里发现的Swarm服务。
如果是我们自己的容器，那么我们就需要在容器下面加装consul agent，然后docker commit出一个新镜像使用。（也可使用Dockerfile重新封装image）

