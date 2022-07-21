## <font color="red">Docker网络很重要</font>
1、初识docker0
![[Pasted image 20220629175616.png]]
docker默认创建的网络
![[Pasted image 20220718163507.png]]
bridge -- 桥接模式(docker默认)
host    --  宿主机模式
none   -- 无网络模式

<font color="red">通常</font>启动容器未指定网络时，容器的网络默认绑定到docker0网卡下。如下：
```
	docker run -d -P --name tomcat01 diytomcat:1.0  #等价于下面命令
	docker run -d -P --net bridge --name tomcat01 diytomcat:1.0
```
用上面命令启动一个容器后，查看docker网络
![[Pasted image 20220720141506.png]]
进入容器查看网络
![[Pasted image 20220720141727.png]]
<font color="red">发现</font>docker和容器间多了一个网络配对，这就是veth-pair网络技术。容器和docker之间建立链接，一边链接协议，一遍通信，实现容器和docker之间通信。
![[Pasted image 20220720143043.png]]

2、容器间网络
![[Pasted image 20220720151141.png]]
tomcat03 直接ping tomcat04 ping不通

查看tomcat04的IP信息
![[Pasted image 20220720151341.png]]

容器tomcat03直接ping容器tomcat04的ip
![[Pasted image 20220720151455.png]]
<font color="red">发现</font>，上述两个容器之间直接通过容器名无法访问，只能ping通ip。

3、<font color="red">为了</font>实现容器名之间直接访问，可以实用--link技术。
```
# --link=container 就能实现两个容器直接访问
docker run -d -P --name tomcat05 --link=tomcat03 diytomcat:1.0

# 查看tomcat05 hosts文件
docker exec -it tomcat05 cat /etc/hosts

# ......
# 172.17.0.4      tomcat03 4eee8b34a9c8

# 发现tomcat05 hosts文件中已有tomcat03

# 用容器名直接访问 能ping通
docker exec -it tomcat05 ping tomcat03

```
一般不建议使用--link，因为多容器时使用该方法非常麻烦，推荐使用自定义网络

4、创建自己的网卡
```
# 查看网络命令
docker network --help
```
```
# 创建自己的网络
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet

# bridge 桥接模式
# 192.168.0.0/16 网段
# 192.168.0.1 网关
# mynet 自定义网络名
```
```
# 查看网络
docker network ls  #发现多了一个mynet
```
![[Pasted image 20220720154256.png]]
```
# 查看mynet的元信息
docker network inspect mynet
```
![[Pasted image 20220720154723.png]]
```
# 启动两台容器，指定网络到mynet下(--net mynet 默认是--net bridge)
docker run -d -P --name tomcat06 --net mynet diytomcat:1.0
docker run -d -P --name tomcat07 --net mynet diytomcat:1.0

# tomcat06 和 tomcat07 互相ping网络
docker exec -it tomcat06 ping tomcat07
# PING tomcat07 (192.168.0.3) 56(84) bytes of data.
# 64 bytes from tomcat07.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.087 ms

docker exec -it tomcat07 ping tomcat06
# PING tomcat07 (192.168.0.3) 56(84) bytes of data.
# 64 bytes from tomcat07.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.087 ms
```

<font color="red">发现在自定义网络mynet下，容器间不需要--link，即可互相连通。但在docker0下需要--link指定才能联通，而不能直接容器名访问。</font>

5、<font color="red">问题:</font> 现在有两个网络下(比如docker0和mynet)，它们下面的容器需要访问?
```
# 先查看现象
docker exec -it tomcat05 ping tomcat06
```
![[Pasted image 20220720160104.png]]
```
无法访问，该怎么解决？这里提供了connect命令
```
![[Pasted image 20220720160301.png]]
```
# connect 实现一个container联通到一个网卡下，这里mynet
docker network connect mynet tomcat05
```
```
# 查看网络mynet元信息
docker network inspect mynet
```
![[Pasted image 20220720161202.png]]
```
# 用tomcat05 ping tomcat06/tomcat07两台机器
docker exec -it tomcat05 ping tomcat06
```
![[Pasted image 20220720161432.png]]
<font color="red">综上:  </font><u>发现通过connect命令，给tomcat05在mynet网段下，分配了一个子网，然后tomcat05容器就能访问tomcat06和tomcat07容器了。？？？该怎么理解呢。。这好比tomcat05容器在docker0下有自己的一个内网IP，此时给它设置了一个公网IP（在mynet下），那么tomcat05就能直接访问mynet下的容器了。</u>
