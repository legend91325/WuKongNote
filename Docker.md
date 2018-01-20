### Docker

~~~
镜像 image 仓库 repository 容器 container
Build Ship Run
~~~

镜像存储方式
linux 联合文件系统
unionfs

镜像最上层 容器 可以读可写

仓库
镜像仓库
hub.docker.com 默认 
c.163.com 网易蜂巢
自搭建镜像中心仓库

```sbtshell
docker pull [OPTIONS] NAME[:TAG]
docker images [OPTIONS][REPOSITORY[:TAG]] 
docker run [OPTIONS] IMAGE [:TAG][C?OMMAND][ARG...]
```

##### Sample

~~~
Nginx
持久运行容器
前台挂起&后台运行
进入容器内部

后台运行 nginx



docker exec 
~~~

```sbtshell
后台运行 nginx 容器
docker run -d nginx

查看当前运行的容器
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
138b58662d0e        nginx               "nginx -g 'daemon of…"   25 seconds ago      Up 23 seconds       80/tcp              jovial_nightingale

进入nginx 容器
docker exec -it 138b58662d0e bash 
```

~~~
How To Install and Use Docker on CentOS 7
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-centos-7

https://docs.docker.com/get-started/
~~~

