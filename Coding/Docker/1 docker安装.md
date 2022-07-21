(<font color="red">centos版</font>)

官方步骤https://docs.docker.com/engine/install/centos/
1、移除旧版本
```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2、安装依赖包
```
yum install -y yum-utils
```

3、设置镜像仓库
```
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo #默认是国外的
	
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo #推荐阿里云，因为快
```

4、更新yum软件包索引(可选操作)
```
yum makecache
```

5、安装docker（docker-ce社区版 ee-企业版）
```
# 安装最新版
yum install docker-ce docker-ce-cli containerd.io

# 查看docker-ce-cli版本
yum list docker-ce-cli --showduplicates|sort -r

# 指定版本号安装(18.09.9)
yum install -y docker-ce-18.09.9 docker-ce-cli-18.09.9 containerd.io
```

6、启动docker
```
# 启动docker
systemctl start docker

# 设置开机自启动
systemctl enable docker
```

7、用docker version 查看是否启动成功

8、运行hello-world
```
docker run hello-world
```

## 卸载docker
1、卸载依赖
```
yum remove docker-ce docker-ce-cli containerd.io
```

2、删除资源
```
rm -rf /var/lib/docker    #docker的默认工作路径
rm -rf /var/lib/containerd
```


