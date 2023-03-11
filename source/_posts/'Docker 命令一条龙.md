---
title: 'Docker 命令一条龙'
date: 2023-01-14 22:27:32
tags: Docker
index_img: /img/page/docker.png
---

# Docker 命令一条龙 

Docker是一种容器化技术，可以将应用程序和所有相关的依赖项打包在一个容器中，使得应用程序可以在任何环境中轻松地运行。Docker可以提高应用程序的可移植性和可靠性，并简化应用程序的部署和维护。

## 安装Docker
Docker支持Windows、Linux和MacOS等操作系统，具体安装步骤可以参考Docker官方文档。

## Docker命令
在安装完成Docker之后，我们就可以开始使用Docker命令了。Docker命令可以在终端中执行，用于构建、运行和管理Docker容器。

### 构建镜像
构建镜像是Docker的核心功能之一，它允许我们将应用程序和所有相关的依赖项打包在一个容器中。下面是构建镜像的基本步骤：

### 编写Dockerfile
在开始构建镜像之前，我们需要编写一个Dockerfile，用于描述应用程序的构建和运行过程。
Dockerfile是一个文本文件，包含了一系列的指令，用于指导Docker如何构建镜像。下面是一个简单的Dockerfile示例：
```shell
# 指定基础镜像
FROM ubuntu:latest

# 设置工作目录
WORKDIR /app

# 拷贝应用程序到容器中
COPY ./app/hello.py /app

# 安装依赖项
RUN apt-get update && apt-get install -y python3

# 设置容器默认命令
CMD ["python3", "hello.py"]
```
在这个Dockerfile中，我们指定了基础镜像为最新版本的Ubuntu，设置了工作目录为/app，拷贝应用程序到容器中，安装了Python3，并设置了容器的默认命令为python3 hello.py。 其中hello.py 脚本如下：
```python
print("Hello, World!")
```

### 构建镜像
在编写完Dockerfile之后，我们就可以使用docker build命令来构建镜像了。下面是一个简单的构建镜像的命令示例：
```shell
docker build -t hello .
```

在这个命令中，我们使用了-t参数来指定镜像的名称为hello，使用了.来表示当前目录下的Dockerfile。构建镜像的过程可能需要一些时间，取决于你的应用程序和基础镜像的大小和复杂性。

### 运行容器
运行容器是Docker的另一个核心功能，它允许我们在Docker容器中运行应用程序。下面是运行容器的基本步骤：

1. 拉取镜像
在运行容器之前，我们需要拉取已经构建好的镜像。可以使用docker pull命令来拉取镜像，例如：
```shell
docker pull hello:latest
```
2. 运行容器
在拉取完镜像之后，我们就可以使用docker run命令来运行容器了。下面是一个简单的运行容器的命令示例：

```shell
docker run -it --rm hello:latest
```
在这个命令中，我们使用了-it参数来指定容器的交互模式，--rm参数表示容器退出后会自动删除，hello:latest表示使用最新版本的hello镜像来运行容器。运行容器后，你将会进入一个类似于终端的界面，可以在其中运行命令。

3. 停止容器
在容器运行结束后，我们可以使用docker stop命令来停止容器，例如：
```shell
docker stop <container_id>
```
其中，<container_id>是容器的ID，可以使用docker ps命令来查看容器的ID。

### 查看镜像和容器
在使用Docker时，经常需要查看已经构建好的镜像和运行中的容器。可以使用docker images命令来查看已经构建好的镜像，例如：

```shell
docker images
```
这个命令将会列出所有已经构建好的镜像，包括镜像的名称、标签、大小等信息。

可以使用docker ps命令来查看运行中的容器，例如：

```shell
docker ps
```

这个命令将会列出所有运行中的容器，包括容器的ID、名称、状态等信息。如果需要查看已经停止的容器，可以使用docker ps -a命令。

### 删除镜像和容器
在使用Docker时，经常需要删除已经构建好的镜像和已经停止的容器。可以使用docker rmi命令来删除镜像，例如：\
```shell
docker rmi <image_name>
```
其中，<image_name>是镜像的名称，可以使用docker images命令来查看镜像的名称。

可以使用docker rm命令来删除容器，例如：
```shell
docker rm <container_id>
```
其中，<container_id>是容器的ID，可以使用docker ps命令来查看容器的ID。



⚠ **Note**: 转载请注明出处