docker是一个容器,感觉就是一个部署应用服务的虚拟环境,有点像轻量级的虚拟机的角色.常见的应用都是部署在服务器上,比如Java web程序,你需要将Java项目打包成jar或者war包,然后再在安装了mysql,redis等相关应用的服务器上运行这个包.而docker感觉则是将jar,mysql,redis等都打包到了一起,这样无论你换了什么机器,你都不用重新安装mysql,redis等程序,这样就会很方便.

docker有不同的产品,其不同的特性为:
| 序号 | 产品           | 特性                                  |
| ---- | -------------- | ------------------------------------- |
| 1    | docker Desktop | 全家桶,包括Engine,Build,Compose等组件 |
| 2    | Docker Engine  | 以server-client的应用程序             |
| 3    | Docker Build   | Docker Engine的组成部分               |
| 4    | Docker Compose | 一个运行多容器docker的工具            |

# docker基本概念
image就好比源代码,容器更像是进行,一份源代码可以产生多个进行,进程之间使用同一份源代码.互不干扰.

## image-镜像
docker将应用程序及其依赖打包在image文件中,通过这个文件生成容器,由容器实现隔离,使得不同应用之间互不干扰.一个image可以生成多个容器.

镜像是通用的,可以在仓库中查到,docker的官方仓库为:docker hub.


## 容器
每一个容器之间是相互隔离的,相互之间没有任何联系.容器将不同的进程及其依赖相互隔离开,互不影响.

# image管理
| 命令                            | 参数                               | 作用                            |
| ------------------------------- | ---------------------------------- |
| docker image pull {GROUP/IMAGE} | 参数为image文件所在组及image的名称 | 将 image 文件从仓库抓取到本地。 |
| docker image ls                 |                                    | 列出本机所有的image文件         |

## docker image ls
```
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   12 months ago   13.3kB
ubuntu        16.04     b6f507652425   13 months ago   135MB
```

需要注意这个`IMAGE ID`,很多的操作都是基于这个`IMAGE ID`


# container管理
| 命令                                              | 参数     | 作用                            | output |
| ------------------------------------------------- | -------- | ------------------------------- | ------ |
| docker container ls                               |          | 列出本机正在运行的容器          |        |
|                                                   | ls --all | 列出所有容器,包括终止运行的容器 |        |
| docker container kill [CONTAINER ID]              |          | 杀死容器                        |
| docker container run [-it] [IMAGE NAME] [COMMAND] | -it:     |

## docker container run
| 参数       | 作用                         | EXAMPLE                                                   |
| ---------- | ---------------------------- | --------------------------------------------------------- |
| -it        | 将容器的shell映射到当前shell |
| IMAGE NAME | 本地image的名字              | ubuntu-16.04                                              |
| [COMMAND]  | 容器启动后执行的第一个命令   | /bin/bash :启动bash,确保可以使用-it将shell映射到当前shell |
## docker container ls --all
```
user@host$docker container ls --all
CONTAINER ID   IMAGE          COMMAND       CREATED        STATUS                      PORTS     NAMES
06cf267e050d   ubuntu:16.04   "/bin/bash"   14 hours ago   Exited (0) 14 hours ago               adoring_noyce
60c31928c3b9   hello-world    "/hello"      14 hours ago   Exited (0) 14 hours ago               dazzling_kirch
59e5ee09075f   ubuntu:16.04   "/bin/bash"   17 hours ago   Exited (255) 14 hours ago             custom
df6fd679f518   hello-world    "/hello"      17 hours ago   Exited (0) 17 hours ago               beautiful_euclid
```