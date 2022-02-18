docker是一种容器化技术，与虚拟机相比，docker的启动速度更快，更轻量

> [Docker官方文档](https://www.docker.com/)
>
> [Docker中文社区](http://www.docker.org.cn/)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d8c9b1aa84cf4a948f6700c9d875d51d~tplv-k3u1fbpfcp-watermark.awebp)

## docker运行过程

从远端仓库中拉取合适的镜像到本地-->通过镜像创建容器-->启动容器

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/7/17153ad08531a560~tplv-t2oaga2asx-watermark.awebp)

镜像相当于定义好的一个类，要使用的时候要实例化为一个对象，也就是将镜像部署为机器上运行的容器。

## 常用命令

| 命令           | 说明                       |
| -------------- | -------------------------- |
| docker images  | 查看镜像                   |
| docker create  | 创建容器                   |
| docker ps      | 查看容器                   |
| docker start   | 启动容器                   |
| docker stop    | 停止容器                   |
| docker reset   | 重启容器                   |
| docker run     | 创建并启动容器             |
| docker exec    | 在运行的容器中执行命令     |
| docker rmi     | 删除容器或镜像             |
| docker inspect | 获取容器/镜像的元数据      |
| docker save    | 导出镜像                   |
| docker load    | 导入镜像                   |
| docker export  | 导出容器                   |
| docker import  | 导入容器                   |
| docker cp      | 用于容器和主机之间拷贝数据 |

关于命令的详细描述可以用`--help`查看。

## 制作镜像

### 通过docker commit制作镜像

```shell
docker commit -m '镜像名' -a '拥有者名' 镜像号
```

### 通过Dockerfile制作镜像

Dockerfile是Docker中用于定义镜像自动化构建流程的配置文件，能够明确的给定Docker镜像的制作过程。

**dockerfile参数**

| FROM    | 继承的镜像文件       |
| ------- | -------------------- |
| COPY    | 拷贝数据             |
| WORKDIR | 工作路径             |
| RUN     | 编译打包阶段运行命令 |
| EXPOSE  | 暴露的端口号         |
| CMD     | 容器运行阶段的命令   |

dockefile:

```dockerfile
FROM node                    # 基于node镜像COPY ./nodeapp /nodeapp      # 将nodeapp目录下d的文件移到容器中  WORKDIR /nodeapp             # 工作目录nodeapp，类似移到nodeapp目录中RUN npm install              # 安装依赖EXPOSE 3000                  # 因为容器是隔离的，所以需要暴露端口
```

```shell
docker build -t dockerfile路径
```

为了使容器运行后自启动需要配置好启动脚本文件，这部分需要自行编写shell脚本，使容器运行后直接搭配好相应的conda环境