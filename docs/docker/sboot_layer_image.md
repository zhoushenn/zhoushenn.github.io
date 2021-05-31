# springboot分层打包docker镜像

> ​	使用分层打包减少docker镜像体积和构建速度。



## demo准备

首先我们准备个demo项目。 注意springboot的版本需要>2.3.x。

> TIP:
>
> 版本 < 2.3.x  无法分层打包
>
> 版本 > 2.3.x  spring-boot-maven-plugin默认分层打包.
>
> 

![aa](sboot_layer_image/image-20210318225652291.png)

## 分层Dockerfile

编写如下dockerfile

```shell
FROM adoptopenjdk:11-jre-hotspot as builder
WORKDIR application
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} application.jar
RUN java -Djarmode=layertools -jar application.jar extract

FROM adoptopenjdk:11-jre-hotspot
WORKDIR application
COPY --from=builder application/dependencies/ ./
COPY --from=builder application/snapshot-dependencies/ ./
COPY --from=builder application/resources/ ./
COPY --from=builder application/application/ ./
ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

WORKDIR： 指定工作目录，保证docker每层都在同一个目录内工作。

RUN java -Djarmode=layertools -jar xx.jar extract：  指定jarmode，解压jar到分层目录。

两个From： Docker分层构建，减少最终的镜像体积。

dependencies，snapshot-dependencies，resources，application ： spring-boot-maven-plugin打包的分层目录。

具体说明可以参考 [spring官网对于SpringBoot多层打包](https://spring.io/blog/2020/01/27/creating-docker-images-with-spring-boot-2-3-0-m1)

> TIP: 文中关于分层配置 LAYERED_JAR， 在springboot 2.4.x以上版本已经不用配置。

## 构建

```shell
#安装open-jdk-11 （非必须步骤）
sudo yum install -y java-11-openjdk-devel

#构建
sudo docker build . --tag demo
```

## 参考

[SpringBoot2.3.0 + Docker实现分层打包](https://blog.csdn.net/ttzommed/article/details/106759670)





















