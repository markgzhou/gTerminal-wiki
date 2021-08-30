# Docker cheat sheet
## Commands
列出本地镜像 Show all the local images

    docker images

在Docker Hub里搜索image/ Search for images from Docker Hub

    docker search image-name

创建container并载入image / start a container and load the image

    docker run image-name/image-id

新建container并载入image，打开并领航交互界面/Run the docker image with interactive STDIN and tty

    docker run -it image-name/image-id

新建容器并创建端口映射

    docker run -p machinPort:containerPort tomcat

挂载本地文件系统至container内部

    docker run -i -t -v Local-Path:Container-Soft-Path image-name bash

端口映射

    docker run --name tomcatContainer -d -p 8888:8080 tomcat

Copy files from/to container 复制文件

    docker cp mycontainer:/opt/file.txt /opt/local/
    docker cp /opt/local/file.txt mycontainer:/opt/

List containers

    docker ps     #查看正在运行中的容器/show all the running container
    docker ps -a  #查看所有容器/Show all the container

删除Docker本地已下载的image/ Delete local images

    docker rmi image-id

在linux操作系统中启用/禁用Docker服务使其随系统启动

    systemtctl enable docker
    systemtctl stop docker

终止指定container

    docker container stop conainer-id/conatiner-name

终止所有容器

    docker stop $(docker ps -aq)

删除制定容器

    docker rm container-name

删除所有容器

    docker rm $(docker ps -aq)

显示container的网络端口映射 / Show the container's ports information

    docker port container-id

如果运行的容器是一套完整的linux系统，通过以下方式登陆

    docker exec -it container-id/container-name bash
    docker exec -it container-id/container-name /bin/bash

退出 不终止容器

    Ctrl+P+Q

查看容器内部进程 / show container running process
    
    docker top container-name

查看容器详细信息 / Show container detail information
    
    docker inspect container-name


在Docker1.13后增加了prune指令用于清理不是用的image和容器

    docker image prune --force --all 或 docker image prune -a // 删除所有不使用的镜像
    docker container prune // 删除所有停止的容器


--------
## Build Docker Image
### From Dockerfile
将脚本保存至Dockerfile

```dockerfile
# Dockerfile
FROM debian:buster-slim

RUN set -ex; \
  if ! command -v gpg > /dev/null; then \
    apt-get update; \
    apt-get install -y \
       ca-certificates apt-transport-https apt-utils git \
       lsb-release gnupg dirmngr curl wget iputils-ping \
       openjdk-11-jdk \
       ; \
    rm -rf /var/lib/apt/lists/*; \
  fi

CMD ["bash"]
```

    docker build --tag=friendlyhello:0.0.1

运行先创建的image

    docker run -p 8888:80 friendlyhello:0.0.1
打开浏览器访问 localhost:8888 查看结果

对生成的image进行本地打包

    docker tag friendlyhello:0.0.1 hub-username/hub-project-name[:TAG]

上传到Hub

    docker push hub-username/hub-project-name[:TAG]


### From a container
下载并加载ubuntu镜像

    docker run -i -t ubuntu
下载安装jdk
    apt-get update
    apt-get install default-jdk

确认javac已经安装
    
    javac --version

退出当前容器
    
    exit

保存容器至image

    docker commit -m "MESSAGE" -a "AUTHOR STRING" <container id> <image name> //根据创建容器创建新image
    sudo docker export <容器id> > docker_app.tar

最后说点镜像的上传，镜像的管理方式非常像git，可以使用docker push命令上传自己本地镜像到仓库，默认上传到DockerHub官方仓库（需要登陆），命令格式：

    docker push NAME[:TAG]
在上传之前一般会先为自己的镜像添加带自己名字（作者信息）的标签：

docker tag testimage:lastest myHubName/testimage:lastest
docker push myHubName/testimage:lastest
有利于上传之后的区分。

我觉得无论是运维团队还是开发团队还是一个实验室，都有必要有一个自己的Docker仓库，可以存放符合自己需求的环境或系统镜像，可以实现快速部署。

-------
## FAQ
挂载宿主机已存在目录后，在容器内对其进行操作，报“Permission denied”。
可通过两种方式解决：

1> 关闭selinux。

临时关闭：# setenforce 0
永久关闭：修改/etc/sysconfig/selinux文件，将SELINUX的值设置为disabled。

2> 以特权方式启动容器

指定--privileged参数
如：# docker run -it --privileged=true -v /test:/soft centos /bin/bash



如果客户的机器离线或网络受限，无法通过Hub加载image怎么办？
> 除了从Hub加载image以外，我们还可以通过从文件加载image的方式运行。

从文件载入镜像的指令：
docker load --input 文件
docker load < 文件名

讲镜像导出只文件的方法
docker save -o 要保存的文件名 要保存的镜像
docker save -o java8.tar lwieske/java-8