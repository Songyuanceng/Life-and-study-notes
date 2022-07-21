### 1.Dockerfile 用来构建docker镜像的文件命令参数脚本
+ 1、编写一个 dockerfile 文件。
+ 2、docker build 构建成为一个镜像。
+ 3、docker run 运行镜像
+ 4、docker push 发布镜像（DockerHub、阿里云镜像仓库!）

### 2.Dockerfile构建过程
基础知识:
+ 1、每个保留关键字（指令）都必须是大写字母。
+ 2、从上到下的执行顺序。
+ 3、# 表示注释。
+ 4、每一个指令都会创建提交一个新的镜像层，并提交。
示例插图：![[Pasted image 20220624235530.png]]

Dockerfile 是面向开发的，以后要发布项目，做镜像，需要编写dockerfile文件，编写十分简单。
Docker镜像逐渐成为企业的交付标准，务必要掌握！
Dockerfile：构建文件，定义了一切的步骤，源代码！
DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品。
Docker容器：容器就是镜像运行起来的容器。

### 3.Dockerfile 常用命令
```
FROM           # 基础镜像，一切从这里开始构建
MAINTAINER     # 镜像是谁写的，姓名+邮箱
RUN            # 镜像构建的时候，需要运行的命令
ADD            # 添加内容
WORKDIR        # 镜像的工作目录
VOLUME         # 挂载的目录
EXPOSE         # 暴露的端口配置
CMD            # 指定这个容器启动时要运行的指令，只有最后一个会生效，可被替代
ENTRYPOINT     # 指定这个容器启动时要运行的指令，可以追加命令
ONBUILD        # 当构建一个被继承 DockerFile 这个时候就会运行 ONBUILD 的指令，触发指令
COPY           # 类似ADD，将文件拷贝到镜像中
ENV            # 构建的时候设置环境变量
```
网上示例图: ![[Pasted image 20220625000818.png]]

### 4.实战 编写dockerfile 构建自己的镜像
#### 4.1 构建一个自己的centos
```
FROM centos

MAINTAINER syc<183088806@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

# 2 执行构建命令
docker build -f dockerfile_path -t jingxiang_name:[tag] .

# 3 运行镜像
docker run -d jingxiang_name

# 注意：上述dockerfile文件build时，centos是8.x版本时会报安装vim的错
```
<font color="red">补充: docker history jingxiang_name 可查看镜像的构建过程</font>
![[Pasted image 20220625170119.png]]

### 4.3 CMD和ENTRYPOINT区别
```
# 区别
CMD 追加的命令覆盖dockerfile中指定的命令，只有追加的生效
ENTRYPOINT 追加的命令是append,依然生效

```

### 4.2  制作tomcat镜像
+ 1、编写dockerfile文件
```
FROM centos:7

MAINTAINER syc<syuanceng@163.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u271-linux-x64.tar.gz /usr/local
ADD apache-tomcat-8.5.81.tar.gz /usr/local

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

ENV JAVA_HOME /usr/local/jdk1.8.0_271
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-8.5.81
ENV CATALINA_BASH /usr/local/apache-tomcat-8.5.81
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 80

CMD /usr/local/apache-tomcat-8.5.81/bin/startup.sh && tail -f /usr/local/apache-tomcat-8.5.81/logs/catalina.out
```
+ 2、执行编译dockerfile
```
docker build -t mytomcat:0.1 .
```
  备注：Dockerfile 官方是以大写开头的，编译时可不指定-f dockerfile_path
+ 3、执行镜像
```
docker run -d -p 8081:8080 -v /opt/app/docker/tomcat/webapps/test:/usr/local/apache-tomcat-8.5.81/webapps/test -v /opt/app/docker/tomcat/webapps/testlogs:/usr/local/apache-tomcat-8.5.81/logs --name syctomcat2 diytomcat:0.1
```
+ 4、在webapps下新建test项目
```
mkdir -p /opt/app/docker/tomcat/webapps/test #创建宿主机test挂载目录
mkdir -p /opt/app/docker/tomcat/webapps/testlogs #创建日志目录
vim index.html #在/opt/app/docker/tomcat/webapps/test下
mkdir WEB_INF  #同上 
vim web.xml    #/opt/app/docker/tomcat/webapps/test/WEB_INFO下
```
+ 5、访问tomcat
```
curl localhost:8080
curl localhost:8080/test #访问刚创建的test
```

### 4.4 发布镜像
```
# 发布到dockerhub 和git提交代码很像
1、docker login #登录
2、docker push  #推送命令
3、docker logout #退出

# 发布到腾讯云或者阿里云
# 登录云服务器-->搜索镜像服务
```