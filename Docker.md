### Docker

~~~
集装箱 标准化 隔离
镜像 image 仓库 repository 容器 container
stack  services Container
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



~~~
How To Install and Use Docker on CentOS 7
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-centos-7

https://docs.docker.com/get-started/

docker docs
https://docs.docker-cn.com/
~~~


##### docker commands

```sbtshell
docker pull [OPTIONS] NAME[:TAG]
docker images [OPTIONS][REPOSITORY[:TAG]] 
docker run [OPTIONS] IMAGE [:TAG][C?OMMAND][ARG...]

docker build -t images .  # Create image using this directory's Dockerfile
docker run -p 4000:80 images  # Run "images" mapping port 4000 to 80
docker run -d -p 4000:80 images            # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry                                            
```

```sbtshell
docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager
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

