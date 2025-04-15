---
sidebar_position: 1
---

# Docker

## 一、Docker介绍

### 1. 什么是Docker

Docker 是一个**开源的应用容器引擎**，它允许开发者将应用及其依赖打包到一个标准化的**容器（Container）**中，实现“一次构建，到处运行”（Build Once, Run Anywhere）的理念。简单来说，Docker 就像是一个轻量级的虚拟机技术，但它更高效、更快速、更易于管理和部署。

Docker思想

- **集装箱**：就像海运中的集装箱一样，Docker 容器包含了应用程序及其所有依赖项，确保在任何环境中都能以相同的方式运行。
- **标准化**：运输方式、存储方式、API 接口。
- **隔离**：每个 Docker 容器都在自己的隔离环境中运行，与宿主机和其他容器隔离。

### 2. Docker容器的特点

- **轻量** : 在一台机器上运行的多个 Docker 容器可以共享这台机器的操作系统内核；它们能够迅速启动，只需占用很少的计算和内存资源。镜像是通过文件系统层进行构造的，并共享一些公共文件。这样就能尽量降低磁盘用量，并能更快地下载镜像。
- **标准** : Docker 容器基于开放式标准，能够在所有主流 Linux 版本、Microsoft Windows 以及包括 VM、裸机服务器和云在内的任何基础设施上运行。
- **安全** : Docker 赋予应用的隔离性不仅限于彼此隔离，还独立于底层的基础设施。Docker 默认提供最强的隔离，因此应用出现问题，也只是单个容器的问题，而不会波及到整台机器。

### 3. 为什么要用Docker

- Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 “这段代码在我机器上没问题啊” 这类问题；——**一致的运行环境**
- 可以做到秒级、甚至毫秒级的启动时间。大大的节约了开发、测试、部署的时间。——**更快速的启动时间**
- 避免公用的服务器，资源会容易受到其他用户的影响。——**隔离性**
- 善于处理集中爆发的服务器使用压力；——**弹性伸缩，快速扩展**
- 可以很轻易的将在一个平台上运行的应用，迁移到另一个平台上，而不用担心运行环境的变化导致应用无法正常运行的情况。——**迁移方便**
- 使用 Docker 可以通过定制应用镜像来实现持续集成、持续交付、部署。——**持续交付和部署**

## 二、Docker基本概念

### 1. 镜像-一个特殊的文件系统

> 镜像就是一个只读的模板，镜像可以用来创建 Docker 容器，一个镜像可以创建多个容器

**操作系统分为内核和用户空间**。对于 Linux 而言，内核启动后，会挂载 root 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 root 文件系统。

**Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。** 镜像不包含任何动态数据，其内容在构建之后也不会被改变。

Docker 设计时，就充分利用 **Union FS** 的技术，将其设计为**分层存储的架构** 。镜像实际是由多层文件系统联合组成。

**镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。** 比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。因此，在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。

分层存储的特征还使得镜像的复用、定制变的更为容易。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需的内容，构建新的镜像。

### 2. 容器-镜像运行时的实体

> 容器是用镜像创建的运行实例，Docker 利用容器独立运行一个或一组应用。它可以被启动、开始、停止、删除，每个容器都是相互隔离的、保证安全的平台。 可以把容器看作是一个简易的 Linux 环境和运行在其中的应用程序。容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的

镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，**容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等** 。

**容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。前面讲过镜像使用的是分层存储，容器也是如此。**

**容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。**

按照 Docker 最佳实践的要求，**容器不应该向其存储层内写入任何数据** ，容器存储层要保持无状态化。**所有的文件写入操作，都应该使用数据卷（Volume）、或者绑定宿主目录**，在这些位置的读写会跳过容器存储层，直接对宿主(或网络存储)发生读写，其性能和稳定性更高。数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此， **使用数据卷后，容器可以随意删除、重新 run ，数据却不会丢失。**

### 3. 仓库-集中存放镜像文件的地方

> 仓库是集中存放镜像文件的场所。仓库和仓库注册服务器是有区别的，仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签。 仓库分为公开仓库和私有仓库两种形式，最大的公开仓库是 DockerHub，存放了数量庞大的镜像供用户下载，国内的公开仓库有阿里云、网易云等

镜像构建完成后，可以很容易的在当前宿主上运行，但是， **如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。**

一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。所以说：**镜像仓库是 Docker 用来集中存放镜像文件的地方类似于我们之前常用的代码仓库。**

通常，**一个仓库会包含同一个软件不同版本的镜像**，而**标签就常用于对应该软件的各个版本** 。我们可以通过`<仓库名>:<标签>`的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签.。

**这里补充一下 Docker Registry 公开服务和私有 Docker Registry 的概念：**

**Docker Registry 公开服务** 是开放给用户使用、允许用户管理镜像的 Registry 服务。一般这类公开服务允许用户免费上传、下载公开的镜像，并可能提供收费服务供用户管理私有镜像。

最常使用的 Registry 公开服务是官方的 **Docker Hub** ，这也是默认的 Registry，并拥有大量的高质量的官方镜像，网址为：https://hub.docker.com/ 。官方是这样介绍 Docker Hub 的：

> Docker Hub 是 Docker 官方提供的一项服务，用于与您的团队查找和共享容器镜像。

比如我们想要搜索自己想要的镜像：

在 Docker Hub 的搜索结果中，有几项关键的信息有助于我们选择合适的镜像：

- **OFFICIAL Image**：代表镜像为 Docker 官方提供和维护，相对来说稳定性和安全性较高。
- **Stars**：和点赞差不多的意思，类似 GitHub 的 Star。
- **Downloads**：代表镜像被拉取的次数，基本上能够表示镜像被使用的频度。

在国内访问 **Docker Hub** 可能会比较慢国内也有一些云服务商提供类似于 Docker Hub 的公开服务。比如 [时速云镜像库](https://www.tenxcloud.com/)、[网易云镜像服务](https://www.163yun.com/product/repo)、[DaoCloud 镜像市场](https://www.daocloud.io/)、[阿里云镜像库](https://www.aliyun.com/product/containerservice?utm_content=se_1292836)等。

除了使用公开服务外，用户还可以在 **本地搭建私有 Docker Registry** 。Docker 官方提供了 Docker Registry 镜像，可以直接使用做为私有 Registry 服务。开源的 Docker Registry 镜像只提供了 Docker Registry API 的服务端实现，足以支持 Docker 命令，不影响使用。但不包含图形界面，以及镜像维护、用户管理、访问控制等高级功能。

### 4. 关系

![image-20250415095520281](.\assets\image-20250415095520281.png)

- Dockerfile 是一个文本文件，包含了一系列的指令和参数，用于定义如何构建一个 Docker 镜像。运行 `docker build`命令并指定一个 Dockerfile 时，Docker 会读取 Dockerfile 中的指令，逐步构建一个新的镜像，并将其保存在本地。
- `docker pull` 命令可以从指定的 Registry/Hub 下载一个镜像到本地，默认使用 Docker Hub。
- `docker run` 命令可以从本地镜像创建一个新的容器并启动它。如果本地没有镜像，Docker 会先尝试从 Registry/Hub 拉取镜像。
- `docker push` 命令可以将本地的 Docker 镜像上传到指定的 Registry/Hub。

### 5. Build, Ship, and Run

- **Build（构建镜像）**：镜像就像是集装箱包括文件以及运行环境等等资源。
- **Ship（运输镜像）**：主机和仓库间运输，这里的仓库就像是超级码头一样。
- **Run （运行镜像）**：运行的镜像就是一个容器，容器就是运行程序的地方。

Docker 运行过程也就是去仓库把镜像拉到本地，然后用一条命令把镜像运行起来变成容器。所以，我们也常常将 Docker 称为码头工人或码头装卸工，这和 Docker 的中文翻译搬运工人如出一辙。

## 三、Docker常见命令

### 1. 基本命令

```
docker version # 查看docker版本
docker images # 查看所有已下载镜像，等价于：docker image ls 命令
docker container ls # 查看所有容器
docker ps #查看正在运行的容器
docker image prune # 清理临时的、没有被使用的镜像文件。-a, --all: 删除所有没有用的镜像，而不仅仅是临时文件；
```

### 2. 拉取镜像

`docker pull` 命令默认使用的 Registry/Hub 是 Docker Hub。当你执行 docker pull 命令而没有指定任何 Registry/Hub 的地址时，Docker 会从 Docker Hub 拉取镜像。

```
docker search mysql # 查看mysql相关镜像
docker pull mysql:5.7 # 拉取mysql镜像
docker image ls # 查看所有已下载镜像
```

### 3. 构建镜像

运行 `docker build`命令并指定一个 Dockerfile 时，Docker 会读取 Dockerfile 中的指令，逐步构建一个新的镜像，并将其保存在本地。

```
# imageName 是镜像名称，1.0.0 是镜像的版本号或标签
docker build -t imageName:1.0.0 .
```

### 4. 删除镜像

```
docker rmi f6509bac4980 #  或者 docker rmi mysql
```

### 5. 镜像推送

```
# 将镜像推送到私有镜像仓库 Harbor
# harbor.example.com是私有镜像仓库的地址，ubuntu是镜像的名称，18.04是镜像的版本标签
docker push harbor.example.com/ubuntu:18.04
```

## 四、Docker数据管理

在容器中管理数据主要有两种方式：

1. 数据卷（Volumes）
2. 挂载主机目录 (Bind mounts)

数据卷是由 Docker 管理的数据存储区域，有如下这些特点：

- 可以在容器之间共享和重用。
- 即使容器被删除，数据卷中的数据也不会被自动删除，从而确保数据的持久性。
- 对数据卷的修改会立马生效。
- 对数据卷的更新，不会影响镜像。

## 五、Docker Compose

### 1. 什么是Docker Compose？有什么用？

Docker Compose 是 Docker 官方编排（Orchestration）项目之一，基于 Python 编写，负责实现对 Docker 容器集群的快速编排。通过 Docker Compose，开发者可以使用 YAML 文件来配置应用的所有服务，然后只需一个简单的命令即可创建和启动所有服务。

Docker Compose 的核心功能：

- **多容器管理**：允许用户在一个 YAML 文件中定义和管理多个容器。
- **服务编排**：配置容器间的网络和依赖关系。
- **一键部署**：通过简单的命令，如`docker-compose up`和`docker-compose down`，可以轻松地启动和停止整个应用程序。

Docker Compose 简化了多容器应用程序的开发、测试和部署过程，提高了开发团队的生产力，同时降低了应用程序的部署复杂度和管理成本。

### 2. Docker Compose文件结构

Docker Compose 文件是 Docker Compose 工具的核心，用于定义和配置多容器 Docker 应用。这个文件通常命名为 `docker-compose.yml`，采用 YAML（YAML Ain't Markup Language）格式编写。

Docker Compose 文件基本结构如下：

- **版本（version）：** 指定 Compose 文件格式的版本。版本决定了可用的配置选项。

- 服务（services）：

   定义了应用中的每个容器（服务）。每个服务可以使用不同的镜像、环境设置和依赖关系。 

  - **镜像（image）：** 从指定的镜像中启动容器，可以是存储仓库、标签以及镜像 ID。
  - **命令（command）：** 可选，覆盖容器启动后默认执行的命令。在启动服务时运行特定的命令或脚本，常用于启动应用程序、执行初始化脚本等。
  - **端口（ports）：** 可选，映射容器和宿主机的端口。
  - **依赖（depends_on）：** 依赖配置的选项，意思是如果服务启动是如果有依赖于其他服务的，先启动被依赖的服务，启动完成后在启动该服务。
  - **环境变量（environment）：** 可选，设置服务运行所需的环境变量。
  - **重启（restart）:** 可选，控制容器的重启策略。在容器退出时，根据指定的策略自动重启容器。
  - **服务卷（volumes）:** 可选，定义服务使用的卷，用于数据持久化或在容器之间共享数据。
  - **构建（build）：** 指定构建镜像的 dockerfile 的上下文路径，或者详细配置对象。

- **网络（networks）：** 定义了容器间的网络连接。

- **卷（volumes）：** 用于数据持久化和共享的数据卷定义。常用于数据库存储、配置文件、日志等数据的持久化。

```
version: "3.8" # 定义版本， 表示当前使用的 docker-compose 语法的版本
services: # 服务，可以存在多个
    servicename1: # 服务名字，它也是内部 bridge 网络可以使用的 DNS name，如果不是集群模式相当于 docker run 的时候指定的一个名称，
   #集群（Swarm）模式是多个容器的逻辑抽象
        image: # 镜像的名字
        command: # 可选，如果设置，则会覆盖默认镜像里的 CMD 命令
        environment: # 可选，等价于 docker container run 里的 --env 选项设置环境变量
        volumes: # 可选，等价于 docker container run 里的 -v 选项 绑定数据卷
        networks: # 可选，等价于 docker container run 里的 --network 选项指定网络
        ports: # 可选，等价于 docker container run 里的 -p 选项指定端口映射
        restart: # 可选，控制容器的重启策略
        build: #构建目录
        depends_on: #服务依赖配置
    servicename2:
        image:
        command:
        networks:
    	ports:
    servicename3:
    #...
volumes: # 可选，需要创建的数据卷，类似 docker volume create
  db_data:
networks: # 可选，等价于 docker network create
```

### 3. Docker Compose常见命令

```
# 启动
# 在当前目录下寻找 docker-compose.yml 文件，并根据其中定义的服务启动应用程序
docker-compose up
# 后台启动
docker-compose up -d
# 强制重新创建所有容器，即使它们已经存在
docker-compose up --force-recreate
# 重新构建镜像
docker-compose up --build
# 指定要启动的服务名称，而不是启动所有服务
# 可以同时指定多个服务，用空格分隔。
docker-compose up service_name

# 暂停
# 在当前目录下寻找 docker-compose.yml 文件
# 根据其中定义移除启动的所有容器，网络和卷。
docker-compose down
# 停止容器但不移除
docker-compose down --stop
# 指定要停止和移除的特定服务，而不是停止和移除所有服务
# 可以同时指定多个服务，用空格分隔。
docker-compose down service_name

# 查看
# 查看所有容器的状态信息
docker-compose ps
# 只显示服务名称
docker-compose ps --services
# 查看指定服务的容器
docker-compose ps service_name
```

## 六、Docker实战

### 1. 安装Docker，DockerCompose

#### 0. 更新系统

```
sudo apt update
sudo apt upgrade -y
```

#### 1. 卸载旧版本（如果有）

```
sudo apt remove docker docker-engine docker.io containerd runc
```

#### 2. 安装必要依赖

```
sudo apt install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

#### 3. 添加 Docker 的 GPG 密钥

```
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
    sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

#### 4. 添加 Docker 软件源

```
echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 5. 安装 Docker 引擎

```
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

> ✅ 安装完成后，你可以通过以下命令验证：

```
docker version
docker info
```

这样你就不需要每次运行 docker 命令时都加 `sudo`：

```
sudo usermod -aG docker $USER
```

然后**重新登录一次或重启系统**使其生效。

#### 6. 安装docker-compose

下载最新版本

```java
sudo curl -L "https://github.com/docker/compose/releases/download/v2.27.0/docker-compose-$(uname -s)-$(uname -m)" \
    -o /usr/local/bin/docker-compose
```

添加可执行权限

```
sudo chmod +x /usr/local/bin/docker-compose
```

验证安装

```
docker-compose version
```

测试是否安装成功

```
docker run hello-world
```

### 2. 编排

docker-compose编排Nginx（HTML，nginx.conf，容器的文件夹和宿主机之间的挂载）,MySQL（SQL文件）,Redis,Go-Nav（Jar包）

编写Dockerfile文件，修改application.yml

```
FROM openjdk:17-jdk
LABEL maintainer="rongxingyang@outlook.com"
# 创建应用目录
WORKDIR /app

# 将应用 JAR 包复制到容器
COPY ./gonav-server.jar app.jar

# 暴露容器的端口
EXPOSE 8080

# 设置容器启动时执行的命令
ENTRYPOINT ["java", "-jar", "app.jar"]
```

编写docker-compose.yml

```
version: '3'
services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"  # 将 Nginx 监听 80 端口
    container_name: gonav-nginx
    depends_on:
      - gonav-server
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf  # 挂载配置文件
      - ./nginx/html:/usr/share/nginx/html
    privileged: true
    restart: always

  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: gonav
    volumes:
      - ./gonav.sql:/docker-entrypoint-initdb.d/gonav.sql
    container_name: mysql

  redis:
    image: redis:latest
    ports:
      - "6379:6379"
    container_name: redis

  gonav-server:
    image: gonav-server:latest
    build:
      context: .
    ports:
      - "8080:8080"
    depends_on:
      - mysql
      - redis
    environment:
      MYSQL_USER: root
      MYSQL_PASSWORD: root
      MAIL_HOST: smtp.126.com
      MAIL_USERNAME: xxx@126.com
      MAIL_PASSWORD: xxx
      ADMIN_EMAIL: admin@example.com
      GITHUB_CLIENT_ID: your_id
      GITHUB_CLIENT_SECRET: your_secret
      LINUXDO_CLIENT_ID: your_id
      LINUXDO_CLIENT_SECRET: your_secret
    container_name: gonav-server
```

### 3. 部署项目

克隆项目

```
git clone https://github.com/dawnstaryrx/go-nav.git
```

进入docker目录

```
cd go-nav/docker
```

修改`docker-compose.yml`中的信息，和`nginx/nginx.conf`的ip

```
vim docker-compose.yml
vim nginx/nginx.conf
```

启动

```
docker-compose up -d
```

关闭

```
docker-compose down
```



