### 目标：springboot项目，打包成镜像，并启动一个容器。
```
1、自定义springboot项目，打包成jar包。

2、编写Dockerfile
FROM java:8  
MAINTAINER syc<syuanceng@163.com>  
ENV MYPATH /usr/local 
WORKDIR $MYPATH

COPY demo-0.0.1-SNAPSHOT.jar $MYPATH/app.jar  
CMD ["--sever.port=8080"]  
EXPOSE 8080  
ENTRYPOINT ["java","-jar","/usr/local/app.jar"]

3、上传jar包和Dockerfile

4、编译镜像
docker build -t spring-boot-demo:1.0 .

5、启动一个容器
docker run -d -p 8080:8080 --name spring-demo-01 spring-boot-demo:1.0

6、访问一个自定义接口，看是否成功！
curl localhost:8080/test/request

```