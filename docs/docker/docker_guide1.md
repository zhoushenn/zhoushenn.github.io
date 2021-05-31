## 快速入门
直接学习 [Docker--从入门到实践](https://yeasy.gitbook.io/docker_practice/)
## 安装docker
安装环境为centos 8

```shell
#安装
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
#启动
sudo systemctl start docker
```

[官网安装参考](https://docs.docker.com/engine/install/centos/)

## 修改docker镜像代理

```shell
##使用阿里云镜像加速器
[root@localhost ~]# mkdir -p /etc/docker
[root@localhost ~]# tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://9cpn8tt6.mirror.aliyuncs.com"]
}
EOF
[root@localhost ~]# systemctl daemon-reload
[root@localhost ~]# systemctl restart docker
```



## 镜像常用命令
```powershell
#搜索镜像
docker search java
#列出镜像
docker images
#删除镜像
docker rmi java:8
#强制删除镜像
docker rmi -f java:8
#删除所有镜像
docker rmi -f ${docker images}
#删除没有被引用的镜像
docker rmi `docker imsages|grep none|awk '{print $3}'` 
```
## 容器常用命令

```powershell
#列出容器
docker ps
#列出所有容器
docker ps -a
#新建并启动容器
# -d: 后台运行 -name:容器名称 -p:端口映射
docker run -p 80:80 -name nginx -d nginx:1.17.0
#停止容器
docker stop <容器名称,通过docker ps看>
#强制停止容器
docker kill <容器名称,通过docker ps看>
#启动已停止容器
docker start <容器名称,通过docker ps看>
#删除容器
docker rm <容器名称,通过docker ps看>
#查看容器日志
docker logs <容器名称,通过docker ps看>
#动态查看容器日志
docker logs <容器名称,通过docker ps看> -f
#查看容器的ip地址
docker inspect --format '{{ .NetworkSettings.IPAddress }}' <容器名称,通过docker ps看>
#修改容器的启动方式
docker container update --restart=always <容器名称,通过docker ps看>
#同步宿主时间到容器
docker cp /etc/localtime <容器名称或者容器id>:/etc/
#指定容器时区
docker run -p 80:80 --name nginx \
-e TZ="Asia/Shanghai" \
-d nginx:1.17.0
#进入docker容器内bash
docker exec -it <容器名称> /bin/bash
#使用root账号进入docker容器内bash
docker exec --user root -it <容器名称> /bin/bash
#查看容器cpu、内存
docker stats  <容器名称>
#查看docker磁盘情况
docker system df
#容器内编辑文件
#TIP: 最好是启动容器的时候-v映射目录，这样可以直接宿主里面编辑。
apt-get update
apt-get install vim
```

## docker pull 阿里镜像
```powershell
mkdir -p /etc/docker

tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://9cpn8tt6.mirror.aliyuncs.com"]
}
EOF

systemctl daemon-reload
systemctl restart docker
```