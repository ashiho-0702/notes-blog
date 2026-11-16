# **入门**
## **介绍**
我们学习了Linux操作系统的常见命令以及如何在Linux上部署一个单体项目。大家想一想自己最大的感受是什么？

我相信，除了个别天赋异禀的同学以外，大多数同学都会有相同的感受，那就是麻烦。核心体现在三点：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令太多了，记不住</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">软件安装包名字复杂，不知道去哪里找</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">安装和部署步骤复杂，容易出错</font>

其实上述问题不仅仅是新手，即便是运维在安装、部署的时候一样会觉得麻烦、容易出错。

特别是我们即将进入微服务阶段学习，微服务项目动辄就是几十台、上百台服务需要部署，有些大型项目甚至达到数万台服务。而由于每台服务器的**运行环境不同**，你写好的安装流程、部署脚本并不一定在每个服务器都能正常运行，经常会出错。这就给系统的部署运维带来了很多困难。

那么，有没有一种技术能够避免部署对服务器环境的依赖，减少复杂的部署流程呢？

答案是肯定的，这就是我们今天要学习的**Docker**技术。

运行环境：依赖库，配置，操作系统；

程序换了环境就跑不了，那么就把程序和环境一起打包给别人，再运行。

操作系统分为用户空间和内核空间，因为程序运行在用户空间，所以不需要内核空间

物理服务器跑多个docker容器，共用一套操作系统（宿主机）；物理服务器跑多个虚拟机，每个都有自己独立的操作系统。

## **安装docker**
### **卸载旧版**
首先如果系统<font style="color:#000000;">中已经存在旧的D</font>ocker，则先卸载：

```bash
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine \
    docker-selinux 
```



### **配置Docker的yum库**
首先要安装一个yum工具

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

安装成功后，执行命令，配置Docker的yum源（<font style="background-color:rgba(255, 246, 122, 0.8);">已更新为阿里云源</font>）：

```bash
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```

更新yum，建立缓存

```bash
sudo yum makecache fast
```

### **安装Docker**
最后，执行命令，安装Docker

```bash
yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987712226-6a63f2b2-eed8-4a77-b22c-46940207353f.png" width="235" title="" crop="0,0,1,1" id="uc4cc79b0" class="ne-image" style="font-size: 14px">

检验是否安装成功

```bash
docker -v
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987712297-2780d5db-ec83-454f-b274-3a197184b7b2.png" width="332" title="" crop="0,0,1,1" id="uba038b1f" class="ne-image" style="font-size: 14px">

```bash
docker images
```

表示无法连接守护进程，说明 docker 没启动

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987712411-c5c0ab12-7a81-4cc6-acde-c93019a177ed.png" width="738" title="" crop="0,0,1,1" id="u4823e8b0" class="ne-image" style="font-size: 14px">

启动后在输入

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987712256-9e603759-c869-456f-b8da-dc548699f4c4.png" width="446" title="" crop="0,0,1,1" id="uf2a674a6" class="ne-image" style="font-size: 14px">



```bash
# 启动Docker
systemctl start docker

# 停止Docker
systemctl stop docker

# 重启
systemctl restart docker

# 设置开机自启
systemctl enable docker

# 执行docker ps命令，如果不报错，说明安装启动成功
docker ps
```



### **配置镜像加速**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阿里云镜像加速</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987712378-b0137e42-3fa2-4138-b1df-b34312da66ab.png" width="505" title="" crop="0,0,1,1" id="u4ba34dca" class="ne-image" style="font-size: 14px">

点击管理控制台

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987713165-a39cf924-fce7-434e-891e-8412ed0e9647.png" width="509" title="" crop="0,0,1,1" id="u07fd42e8" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987713252-de021315-07ab-434e-b455-b9f5ffebd84f.png" width="499" title="" crop="0,0,1,1" id="udb8c9287" class="ne-image" style="font-size: 14px">

镜像地址可能会变更，如果失效可以百度找最新的docker镜像。

配置镜像步骤如下：

```bash
# 创建目录
mkdir -p /etc/docker

# 复制内容
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://docker.mirrors.ustc.edu.cn",
    "https://hub.mirrorify.net",
    "https://r3qq78b8.mirror.aliyuncs.com",
    "https://docker.xuanyuan.me",
    "https://docker.1ms.run",
    "https://dockerproxy.com",
    "https://hub-mirror.c.163.com"
  ]
}
EOF

# 重新加载配置
systemctl daemon-reload

# 重启Docker
systemctl restart docker
```

### **Windows转到centos**
如果镜像实在不行，就用此方法。

安装 Docker Desktop

打开 PowerShell

```bash
# 拉取 nginx 镜像
docker pull nginx
# 查看镜像确认
docker images
# 打包镜像为 nginx.tar
# -o 指定输出文件，生成在当前文件夹
docker save -o nginx.tar nginx:latest
```

Linux 端选择存放目录，推荐 /root/

Windows 找到 nginx.tar，直接拖拽到 FinalShell 文件管理窗口，自动上传

```bash
# CentOS7 导入镜像
docker load -i /root/nginx.tar
# 查看镜像
docker images
```



## **部署MySQL**
Docker会自动搜索并下载MySQL。

注意：这里下载的不是安装包，而是**镜像。**镜像中不仅包含了MySQL本身，还包含了其运行所需要的环境、配置、系统库。

镜像在运行时会创建一个隔离环境，就可以跨系统运行，也不需要手动再次配置环境了。这套独立运行的隔离环境我们称为**容器**。

所以一个镜像可以启动多个容器。

在命令行输入下面的命令：

```bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=1234 \
  mysql
```

如安装失败，就是镜像的问题。

运行效果如图：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987713654-3d3173b9-b321-4d28-8d41-7260c55b9fed.png" width="583" title="" crop="0,0,1,1" id="ua29272f6" class="ne-image" style="font-size: 14px">

如出现此报错，说明原生MySQL占用了3306端口

```bash
docker: Error response from daemon: driver failed programming external connectivity on endpoint mysql (9d252d7c686153b603023082c673d04aeb3fa7ff624efe26d10cc43073963223): 
Error starting userland proxy: listen tcp4 0.0.0.0:3306: bind: address already in use.
```

关闭原生MySQL

```bash
systemctl stop mysqld
```



+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">或者改成3307端口</font>

关闭 MySQL 容器，但镜像还在。镜像相当于安装包，容器相当于运行的实例程序。

```bash
docker rm -f mysql
```

```bash
docker run -d \
  --name mysql \
  -p 3307:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=1234 \
  mysql
```

用客户端工具连接MySQL：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987713430-67a0db85-5b6b-4e34-9813-514dcc2e99ca.png" width="594" title="" crop="0,0,1,1" id="ua63fa89b" class="ne-image" style="font-size: 14px">

因此，Docker安装软件的过程，就是自动搜索下载镜像，然后创建并运行容器的过程。

Docker会根据命令中的镜像名称自动搜索并下载镜像，那么问题来了，它是去哪里搜索和下载镜像的呢？这些镜像又是谁制作的呢？

Docker官方提供了一个专门管理、存储镜像的网站，并对外开放了镜像上传、下载的权利。Docker官方提供了一些基础镜像，然后各大软件公司又在基础镜像基础上，制作了自家软件的镜像，全部都存放在这个网站。这个网站就成了Docker镜像交流的社区：

[<font style="color:rgb(0, 56, 132);">https://hub.docker.com/</font>](https://hub.docker.com/)

基本上我们常用的各种软件都能在这个网站上找到，我们甚至可以自己制作镜像上传上去。

像这种提供存储、管理Docker镜像的服务器，被称为DockerRegistry，可以翻译为镜像仓库。DockerHub网站是官方仓库，阿里云、华为云会提供一些第三方仓库，我们也可以自己搭建私有的镜像仓库。

官方仓库在国外，下载速度较慢，一般我们都会使用第三方仓库提供的镜像加速功能，提高下载速度。而企业内部的机密项目，往往会采用私有镜像仓库。

总之，镜像的来源有两种：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">基于官方基础镜像自己制作</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">直接去DockerRegistry下载</font>

**总结一下**：

Docker本身包含一个后台服务，我们可以利用Docker命令告诉Docker服务，帮助我们快速部署指定的应用。Docker服务部署应用时，首先要去搜索并下载应用对应的镜像，然后根据镜像创建并允许容器，应用就部署完成了。

用一幅图标示如下：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987713624-28f029dd-6821-482d-9775-17aad968d524.png" width="640" title="" crop="0,0,1,1" id="ufaf5f057" class="ne-image" style="font-size: 14px">

## **命令解读**
利用Docker快速的安装了MySQL，非常的方便，不过我们执行的命令到底是什么意思呢？

```bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=1234 \
  mysql
```

<font style="color:rgb(0, 0, 0);">解读：</font>

+ <font style="color:rgb(0, 0, 0);">docker run -d ：</font>**<font style="color:rgb(0, 0, 0);">创建并运行</font>**<font style="color:rgb(0, 0, 0);">一个容器，-d则是让容器以后台进程运行</font>
+ <font style="color:rgb(0, 0, 0);">--name mysql  : 给容器起个名字叫mysql，必须唯一，同一台 Docker 里，容器名称不允许重复。</font>
+ <font style="color:rgb(0, 0, 0);">-p 3306:3306 : 设置端口映射。</font>
    - **<font style="color:rgb(0, 0, 0);">容器是隔离环境</font>**<font style="color:rgb(0, 0, 0);">，外界不可访问。但是可以</font>**<font style="color:rgb(0, 0, 0);">将宿主机端口映射容器内到端口</font>**<font style="color:rgb(0, 0, 0);">，当访问宿主机指定端口时，就是在访问容器内的端口了。</font>
    - <font style="color:rgb(0, 0, 0);">容器内端口往往是由容器内的进程决定，例如MySQL进程默认端口是3306，因此容器内端口一定是3306；而宿主机端口则可以任意指定，一般与容器内保持一致。</font>
    - <font style="color:rgb(0, 0, 0);">格式： -p 宿主机端口:容器内端口，示例中就是将宿主机的3306映射到容器内的3306端口</font>
+ <font style="color:rgb(0, 0, 0);">-e TZ=Asia/Shanghai : 配置容器内进程运行时的一些参数，即设置环境变量</font>
    - <font style="color:rgb(0, 0, 0);">格式：-e KEY=VALUE，KEY和VALUE都由容器内进程决定</font>
    - <font style="color:rgb(0, 0, 0);">案例中，TZ=Asia/Shanghai是设置时区；MYSQL_ROOT_PASSWORD=1234是设置MySQL默认密码</font>
+ <font style="color:rgb(0, 0, 0);">mysql : 设置</font>**<font style="color:rgb(0, 0, 0);">镜像</font>**<font style="color:rgb(0, 0, 0);">名称，Docker会根据这个名字搜索并下载镜像</font>
    - <font style="color:rgb(0, 0, 0);">格式：REPOSITORY:TAG，例如mysql:8.0，其中REPOSITORY可以理解为镜像名，TAG是版本号</font>
    - <font style="color:rgb(0, 0, 0);">在未指定TAG的情况下，默认是最新版本，也就是mysql:latest</font>

<font style="color:rgb(0, 0, 0);"></font>

镜像的名称不是随意的，而是要到DockerRegistry中寻找，镜像运行时的配置也不是随意的，要参考镜像的帮助文档，这些在DockerHub网站或者软件的官方网站中都能找到。

如果我们要安装其它软件，也可以到DockerRegistry中寻找对应的镜像名称和版本，阅读相关配置即可。

# **Docker基础**
## **常见命令**
### **命令介绍**
其中，比较常见的命令有：

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">说明</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">文档地址</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker pull</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拉取镜像，但不会创建并运行容器</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker pull</font>](https://docs.docker.com/engine/reference/commandline/pull/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker push</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">推送镜像到DockerRegistry</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker push</font>](https://docs.docker.com/engine/reference/commandline/push/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker images</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看本地镜像</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker images</font>](https://docs.docker.com/engine/reference/commandline/images/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker rmi</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除本地镜像</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker rmi</font>](https://docs.docker.com/engine/reference/commandline/rmi/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker run</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">创建并运行容器（不能重复创建）</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker run</font>](https://docs.docker.com/engine/reference/commandline/run/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker stop</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">停止指定容器</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker stop</font>](https://docs.docker.com/engine/reference/commandline/stop/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker start</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">启动指定容器</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker start</font>](https://docs.docker.com/engine/reference/commandline/start/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker restart</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">重新启动容器</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker restart</font>](https://docs.docker.com/engine/reference/commandline/restart/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker rm</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除指定容器</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docs.docker.com</font>](https://docs.docker.com/engine/reference/commandline/rm/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker ps</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看容器</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker ps</font>](https://docs.docker.com/engine/reference/commandline/ps/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker logs</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看容器运行日志</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker logs</font>](https://docs.docker.com/engine/reference/commandline/logs/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker exec</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">进入容器</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker exec</font>](https://docs.docker.com/engine/reference/commandline/exec/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker save</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">保存镜像到本地压缩文件</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker save</font>](https://docs.docker.com/engine/reference/commandline/save/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker load</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">加载本地压缩文件到镜像</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker load</font>](https://docs.docker.com/engine/reference/commandline/load/) |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker inspect</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看容器详细信息</font> | [<font style="color:rgb(0, 56, 132);background-color:rgba(0, 0, 0, 0);">docker inspect</font>](https://docs.docker.com/engine/reference/commandline/inspect/) |

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1784987714198-1886edcb-44fe-4ab4-af96-9cb2158f4906.png" width="739" title="" crop="0,0,1,1" id="u3fe22982" class="ne-image" style="font-size: 14px">

补充：默认情况下，每次重启虚拟机我们都需要手动启动Docker和Docker中的容器。通过命令可以实现开机自启：

```bash
# 开机自启
systemctl enable docker

# Docker容器开机自启
docker update --restart=always [容器名/容器id]
```

容器名和容器 id 如何查看：

```bash
docker ps
```

输出示例：

```bash
CONTAINER ID   IMAGE         STATUS    NAMES
08fa35e29671   mysql:8.0.36  Up 10m    mysql
```

CONTAINER ID：容器 ID（只用前几位就能识别，比如 08fa35e29671 可以简写 08fa）

NAMES：容器名字，你创建时 --name mysql 就是这里

重点：docker update 后面容器 ID 或者 容器名称二选一都能用

### **nginx**
以Nginx为例给大家演示上述命令。

```bash
# 第1步，去DockerHub搜索nginx镜像仓库，查看镜像名称
# https://hub.docker.com/_/nginx
# 第2步，拉取Nginx镜像
docker pull nginx

# 第3步，查看镜像
docker images
# 结果如下：
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    605c77e624dd   16 months ago   141MB
mysql        latest    3218b38490ce   17 months ago   516MB

# 第4步，创建并允许Nginx容器
docker run -d --name nginx -p 80:80 nginx

# 第5步，查看运行中容器
docker ps
# 也可以加格式化方式访问，格式会更加清爽
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第6步，访问网页，地址：http://虚拟机地址

# 第7步，停止容器
docker stop nginx

# 第8步，查看所有容器
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第9步，再次启动nginx容器
docker start nginx

# 第10步，再次查看容器
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第11步，查看容器详细信息
docker inspect nginx

# 第12步，进入容器,查看容器内目录
docker exec -it nginx bash
# 或者，可以进入MySQL
docker exec -it mysql mysql -uroot -p

# 第13步，删除容器
docker rm nginx
# 发现无法删除，因为容器运行中，强制删除容器
docker rm -f nginx
```

### **命令别名**
给常用Docker命令起别名，方便我们访问：

```bash
# 修改/root/.bashrc文件
vim /root/.bashrc
内容如下：
# .bashrc

# User specific aliases and functions

# 删除每一个文件前，都会弹窗询问
# 生产环境谨慎配置，大量批量删除任务会疯狂弹窗。
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
alias dis='docker images'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```

然后，执行命令使别名生效

```bash
source /root/.bashrc
```

接下来，试试看新的命令吧。



## **数据卷**
### **介绍**
**数据卷（volume）**是一个虚拟目录，是**容器内目录**与**宿主机目录**之间映射的桥梁。

Docker 挂载主要分为 bind mount（绑定宿主机本地目录）和 volume（数据卷）

以Nginx为例，我们知道Nginx中有两个关键的目录：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">html：放置一些静态资源</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">conf：放置配置文件</font>

如果我们要让Nginx代理我们的静态资源，最好是放到html目录；如果我们要修改Nginx的配置，最好是找到conf下的nginx.conf文件。

但遗憾的是，容器运行的Nginx所有的文件都在容器内部。所以我们必须利用数据卷将两个目录与宿主机目录关联，方便我们操作。如图：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120745052-95f08cef-9904-4809-a538-3081bdc28ec7.png" width="624" title="" crop="0,0,1,1" id="ue8b7babb" class="ne-image" style="font-size: 14px">

在上图中：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">我们创建了两个数据卷：conf、html</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Nginx容器内部的 conf 目录和 html 目录分别与两个数据卷关联。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">而数据卷 conf 和 html 分别指向了宿主机的 /var/lib/docker/volumes/conf/_data 目录和 /var/lib/docker/volumes/html/_data 目录</font>

这样以来，容器内的conf和html目录就 与宿主机的conf和html目录关联起来，我们称为**挂载**。此时，我们操作宿主机的/var/lib/docker/volumes/html/_data就是在操作容器内的/usr/share/nginx/html/_data目录。只要我们将静态资源放入宿主机对应目录，就可以被Nginx代理了。

**小提示**：

/var/lib/docker/volumes这个目录就是默认的存放所有容器数据卷的目录，其下再根据数据卷名称创建新目录，格式为/数据卷名/_data。

**为什么不让容器目录直接指向宿主机目录，而是指向数据卷**？

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">因为直接指向宿主机目录就与宿主机强耦合了，如果切换了环境，宿主机目录就可能发生改变了。由于容器一旦创建，目录挂载就无法修改，这样容器就无法正常工作了。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">但是容器指向数据卷，一个逻辑名称，而数据卷再指向宿主机目录，就不存在强耦合。如果宿主机目录发生改变，只要改变数据卷与宿主机目录之间的映射关系即可。</font>

不过，我们通过由于数据卷目录比较深，不好寻找，通常我们也**允许让容器直接与宿主机目录挂载而不使用数据卷**，具体参考挂载本地目录或文件章节。

### **命令**
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">说明</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker volume create</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">创建数据卷</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker volume ls</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看所有数据卷</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker volume rm</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除指定数据卷</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker volume inspect</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看某个数据卷的详情</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker volume prune</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除未使用的数据卷</font> |


注意：容器与数据卷的挂载要在创建容器时配置，对于创建好的容器，是不能设置数据卷的。而且**创建容器的过程中，数据卷会自动创建**。

教学**演示环节**：演示一下nginx的html目录挂载

/var/lib/docker/volumes/html/_data 对应的宿主机目录，html 是虚拟目录 ，/usr/share/nginx/html 容器内的对应目录

```bash
# 1.首先创建容器并指定数据卷，注意通过 -v 参数来指定数据卷
docker rm -f nginx
# 数据卷:容器内目录
docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx

# 2.然后查看数据卷
docker volume ls
# 结果
DRIVER    VOLUME NAME
local     29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f
local     html

# 3.查看数据卷详情
docker volume inspect html
# 结果
[
    {
        "CreatedAt": "2024-05-17T19:57:08+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/html/_data",
        "Name": "html",
        "Options": null,
        "Scope": "local"
    }
]

# 4.查看/var/lib/docker/volumes/html/_data目录
ll /var/lib/docker/volumes/html/_data
# 可以看到与nginx的html目录内容一样，结果如下：
总用量 8
-rw-r--r--. 1 root root 497 12月 28 2021 50x.html
-rw-r--r--. 1 root root 615 12月 28 2021 index.html

# 5.进入该目录，并随意修改index.html内容
cd /var/lib/docker/volumes/html/_data
vim index.html

# 6.打开页面，查看效果

# 7.进入容器内部，查看/usr/share/nginx/html目录内的文件是否变化
docker exec -it nginx bash
```

#### **匿名数据卷**
第一个 MySQL 容器产生的匿名卷，把第一个容器删了，匿名卷不会被跟着删除。

再运行第二个容器，那第二个容器是不是还会产生一个新的匿名卷，所以存在两个匿名卷。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">演示一下MySQL的匿名数据卷</font>

```bash
# 1.查看MySQL容器详细信息
docker inspect mysql
# 关注其中.Config.Volumes部分和.Mounts部分
```

我们关注两部分内容，第一是.Config.Volumes部分：

```bash
{
  "Config": {
    // ... 略
    "Volumes": {
      "/var/lib/mysql": {}
    }
    // ... 略
  }
}
```

可以发现这个容器声明了一个本地目录，需要挂载数据卷，但是**数据卷未定义**。容器运行时自动创建的卷，就是匿名卷。

然后，我们再看结果中的.Mounts部分：

```bash
{
  "Mounts": [
    {
      "Type": "volume",
      "Name": "33796e6c8e822a5f28c44a9279915f5b1e966ce2c6e81d341e7c63ba53835af9",
      "Source": "/var/lib/docker/volumes/33796e6c8e822a5f28c44a9279915f5b1e966ce2c6e81d341e7c63ba53835af9/_data",
      "Destination": "/var/lib/mysql",
      "Driver": "local",
    }
  ]
}
```

对比 nginx 的 Mounts

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120745099-ae1e8db9-737d-4df8-b8a7-6e9716b14fdf.png" width="445" title="" crop="0,0,1,1" id="uba2ba13f" class="ne-image" style="font-size: 14px">

可以发现，其中有几个关键属性：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Name：数据卷名称。由于定义容器未设置容器名，这里的就是匿名卷自动生成的名字，一串hash值。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Source：宿主机目录</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Destination : 容器内的目录</font>

上述配置是将容器内的/var/lib/mysql这个目录，与数据卷33796e6c8e822a5f28c44a9279915f5b1e966ce2c6e81d341e7c63ba53835af9 挂载。于是在宿主机中就有了/var/lib/docker/volumes/33796e6c8e822a5f28c44a9279915f5b1e966ce2c6e81d341e7c63ba53835af9/_data这个目录。这就是匿名数据卷对应的目录，其使用方式与普通数据卷没有差别。

接下来，可以查看该目录下的 MySQL 的 data 文件：

```bash
ls -l /var/lib/docker/volumes/33796e6c8e822a5f28c44a9279915f5b1e966ce2c6e81d341e7c63ba53835af9/_data
```

注意：每一个不同的镜像，将来创建容器后内部有哪些目录可以挂载，可以参考DockerHub对应的页面

### **挂载本地目录或文件**
可以发现，数据卷的目录结构较深，如果我们去操作数据卷目录会不太方便。且容器内部文件默认都存在镜像分层 + 容器读写层，容器删掉，里面产生的数据跟着消失。

如挂载到本地目录，就算容器被删除，文件依然存在，数据也存在。

在很多情况下，我们会直接将容器目录与宿主机指定目录挂载。挂载语法与数据卷类似：

```bash
# 挂载本地目录
-v 本地目录:容器内目录
# 挂载本地文件
-v 本地文件:容器内文件
```

**注意**：本地目录或文件必须以 / 或 ./开头，如果直接以名字开头，会被识别为数据卷名而非本地目录名。

例如：

```bash
-v mysql:/var/lib/mysql # 会被识别为一个数据卷叫mysql，运行时会自动创建这个数据卷
-v ./mysql:/var/lib/mysql # 会被识别为当前目录下的mysql目录，运行时如果不存在会创建目录
```

**演示**删除并重新创建 mysql 容器，并完成本地目录挂载：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">挂载</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/root/mysql/data</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">到容器内的</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/var/lib/mysql</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">目录</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">挂载</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/root/mysql/init</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">到容器内的</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/docker-entrypoint-initdb.d</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">目录（初始化的SQL脚本目录）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">挂载</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/root/mysql/conf</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">到容器内的</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/etc/mysql/conf.d</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">目录（这个是MySQL配置文件目录）</font>

在课前资料中已经准备好了mysql的init目录和conf目录：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120745106-fa2e08dc-50b3-48f9-8015-68a1f5843828.png" width="548" title="" crop="0,0,1,1" id="u0aa325ab" class="ne-image" style="font-size: 14px">

以及对应的初始化SQL脚本和配置文件：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120745178-ea7e157c-52da-47e1-9edd-a2967ce214d8.png" width="559" title="" crop="0,0,1,1" id="ua8020aa9" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120745123-4cf8c4f9-43f2-4bb3-a902-b37bebc2995d.png" width="596" title="" crop="0,0,1,1" id="ub8f02cd1" class="ne-image" style="font-size: 14px">

其中，hm.cnf主要是配置了MySQL的默认编码，改为utf8mb4；而hmall.sql则是后面我们要用到的黑马商城项目的初始化SQL脚本。

我们直接将整个mysql目录上传至虚拟机的/root目录下：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120746107-d1059012-dc97-45c6-91e8-4495a05e0a6e.png" width="703" title="" crop="0,0,1,1" id="u962f6d75" class="ne-image" style="font-size: 14px">

接下来，我们演示本地目录挂载：

```bash
# 1.删除原来的MySQL容器
docker rm -f mysql

# 2.进入root目录
cd ~

# 3.创建并运行新mysql容器，挂载本地目录
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=1234 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  mysql

# 4.查看root目录，可以发现~/mysql/data目录已经自动创建好了
ls -l mysql
# 结果：
总用量 4
drwxr-xr-x. 2 root    root   20 5月  19 15:11 conf
drwxr-xr-x. 7 polkitd root 4096 5月  19 15:11 data
drwxr-xr-x. 2 root    root   23 5月  19 15:11 init

# 查看data目录，会发现里面有大量数据库数据，说明数据库完成了初始化
ls -l data

# 5.查看MySQL容器内数据
# 5.1.进入MySQL
docker exec -it mysql mysql -uroot -p1234
# 5.2.查看编码表
show variables like "%char%";
# 5.3.结果，发现编码是utf8mb4没有问题
+--------------------------+--------------------------------+
| Variable_name            | Value                          |
+--------------------------+--------------------------------+
| character_set_client     | utf8mb4                        |
| character_set_connection | utf8mb4                        |
| character_set_database   | utf8mb4                        |
| character_set_filesystem | binary                         |
| character_set_results    | utf8mb4                        |
| character_set_server     | utf8mb4                        |
| character_set_system     | utf8mb3                        |
| character_sets_dir       | /usr/share/mysql-8.0/charsets/ |
+--------------------------+--------------------------------+

# 6.查看数据
# 6.1.查看数据库
show databases;
# 结果，hmall是黑马商城数据库
+--------------------+
| Database           |
+--------------------+
| hmall              |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
# 6.2.切换到hmall数据库
use hmall;
# 6.3.查看表
show tables;
# 结果：
+-----------------+
| Tables_in_hmall |
+-----------------+
| address         |
| cart            |
| item            |
| order           |
| order_detail    |
| order_logistics |
| pay_order       |
| user            |
+-----------------+
# 6.4.查看address表数据
select * from address;
+----+---------+----------+--------+----------+-------------+---------------+-----------+------------+-------+
| id | user_id | province | city   | town     | mobile      | street        | contact   | is_default | notes |
+----+---------+----------+--------+----------+-------------+---------------+-----------+------------+-------+
| 59 |       1 | 北京     | 北京   | 朝阳区    | 13900112222 | 金燕龙办公楼   | 李佳诚    | 0          | NULL  |
| 60 |       1 | 北京     | 北京   | 朝阳区    | 13700221122 | 修正大厦       | 李佳红    | 0          | NULL  |
| 61 |       1 | 上海     | 上海   | 浦东新区  | 13301212233 | 航头镇航头路   | 李佳星    | 1          | NULL  |
| 63 |       1 | 广东     | 佛山   | 永春      | 13301212233 | 永春武馆       | 李晓龙    | 0          | NULL  |
+----+---------+----------+--------+----------+-------------+---------------+-----------+------------+-------+
4 rows in set (0.00 sec)
```

## **镜像**
### **镜像结构**
之前我们说过，镜像之所以能让我们快速跨操作系统部署应用而忽略其运行环境、配置，就是因为镜像中包含了程序运行需要的系统函数库、环境、配置、依赖。

因此，自定义镜像本质就是依次准备好程序运行的基础环境、依赖、应用本身、运行配置等文件，并且打包而成。

举个例子，我们要从0部署一个Java应用，大概流程是这样：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">准备一个linux服务（CentOS或者Ubuntu均可）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">安装并配置JDK</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">上传Jar包</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">运行jar包</font>

那因此，我们打包镜像也是分成这么几步：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">准备Linux运行环境（java项目并不需要完整的操作系统，仅仅是基础运行环境即可）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">安装并配置JDK</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝jar包</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">配置启动脚本</font>

上述步骤中的每一次操作其实都是在生产一些文件（系统运行环境、函数库、配置最终都是磁盘文件），所以**镜像就是一堆文件的集合**。

但需要注意的是，镜像文件不是随意堆放的，而是按照操作的步骤分层叠加而成，每一层形成的文件都会单独打包并标记一个唯一id，称为**Layer**（**层**）。这样，如果我们构建时用到的某些层其他人已经制作过，就可以直接拷贝使用这些层，而不用重复制作。

例如，第一步中需要的Linux运行环境，通用性就很强，所以Docker官方就制作了这样的只包含Linux运行环境的镜像。

我们在制作java镜像时，就无需重复制作，直接使用Docker官方提供的CentOS或Ubuntu镜像作为基础镜像。

然后再搭建其它层即可，这样逐层搭建，最终整个Java项目的镜像结构如图所示：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120935481-087351f4-a429-457c-a3c7-883f6391880f.png" width="626" title="" crop="0,0,1,1" id="u966ee4c4" class="ne-image" style="font-size: 14px">





### **Dockerfile**
在构建镜像的时候需要 Dockerfile 文件，但是构建好的镜像中是不含DockerFile的。DockerFile只参与构建，不参与产物。

由于制作镜像的过程中，需要逐层处理和打包，比较复杂，所以Docker就提供了自动打包镜像的功能。我们只需要将打包的过程，每一层要做的事情用固定的语法写下来，交给Docker去执行即可。

而这种记录镜像结构的文件就称为**Dockerfile**，其对应的语法可以参考官方文档：

[<font style="color:rgb(0, 56, 132);">https://docs.docker.com/engine/reference/builder/</font>](https://docs.docker.com/engine/reference/builder/)

其中的语法比较多，比较常用的有：

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">指令</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">说明</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">示例</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">FROM</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">指定基础镜像</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">FROM centos:6</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ENV</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">设置环境变量，可在后面指令使用</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ENV key=value</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">COPY</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝本地文件到镜像的指定目录</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">COPY ./xx.jar /tmp/app.jar</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">RUN</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">执行Linux的shell命令，一般是安装过程的命令</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">RUN yum install gcc</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">EXPOSE</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">指定容器运行时监听的端口，是给镜像使用者看的</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">EXPOSE 8080</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ENTRYPOINT</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">镜像中应用的启动命令，容器运行时调用</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ENTRYPOINT java -jar xx.jar</font> |


例如，要基于Ubuntu镜像来构建一个Java应用，其Dockerfile内容如下：

```bash
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，JDK的安装目录、容器内时区
ENV JAVA_DIR=/usr/local
ENV TZ=Asia/Shanghai
# 拷贝jdk和java项目的包
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar
# 设定时区
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 安装JDK
RUN cd $JAVA_DIR \
 && tar -xf ./jdk8.tar.gz \
 && mv ./jdk1.8.0_144 ./java8
# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin
# 指定项目监听的端口
EXPOSE 8080
# 入口，java项目的启动命令
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

同学们思考一下：以后我们会有很多很多java项目需要打包为镜像，他们都需要Linux系统环境、JDK环境这两层，只有上面的3层不同（因为jar包不同）。如果每次制作java镜像都重复制作前两层镜像，是不是很麻烦。

所以，就有人提供了基础的系统加JDK环境，我们在此基础上制作 java 镜像，就可以省去JDK的配置了：

```bash
# 基础镜像
FROM openjdk:11.0-jre-buster
# 设定时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 拷贝jar包
COPY docker-demo.jar /app.jar
# 入口
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120746922-eeecd4e3-9562-4e2e-b1bc-2599df627b13.png" width="1852" title="" crop="0,0,1,1" id="u02a8994f" class="ne-image" style="font-size: 14px">

### **构建镜像**
当Dockerfile文件写好以后，就可以利用命令来构建镜像了。

在课前资料中，我们准备好了一个demo项目及对应的Dockerfile：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120746308-ef56a778-af3d-4711-848a-ba626ffc97c1.png" width="603" title="" crop="0,0,1,1" id="ua8b427d6" class="ne-image" style="font-size: 14px">

首先，我们将课前资料提供的docker-demo.jar包以及Dockerfile拷贝到虚拟机的/root/demo目录：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120746337-331a95bb-266a-4a2d-9bd4-fc5a1d32b77a.png" width="605" title="" crop="0,0,1,1" id="u0591348f" class="ne-image" style="font-size: 14px">

把 jdk.tar 镜像的压缩包拉到 /root，再执行：

```bash
docker load -i jdk.tar
```

然后，执行命令，构建镜像：

```bash
# 进入镜像目录
cd /root/demo
# 该命令会自动执行Dockerfile中的指令
docker build -t docker-demo:1.0 .
```

命令说明：

+ `<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker build </font>`<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">: 就是构建一个docker镜像</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-t docker-demo:1.0 </font>`<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">：-t参数是指定镜像的名称（repository和tag）</font>
+ `<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">. </font>`<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">: 最后的点是指Dockerfile所在路径，由于我们进入了demo目录，所以指定的是.代表当前目录，也可以直接指定Dockerfile目录：</font>

```bash
# 直接指定Dockerfile目录
docker build -t docker-demo:1.0 /root/demo
```

结果：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120746587-3dfb1918-6fdc-4afb-9406-d8376876173f.png" width="1567" title="" crop="0,0,1,1" id="u6ce6ef27" class="ne-image" style="font-size: 14px">

查看镜像列表：

```bash
# 查看镜像列表：
docker images
# 结果
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
docker-demo   1.0       d6ab0b9e64b9   27 minutes ago   327MB
nginx         latest    605c77e624dd   16 months ago    141MB
mysql         latest    3218b38490ce   17 months ago    516MB
```

然后尝试运行该镜像：

```bash
# 1.创建并运行容器
docker run -d --name dd -p 8080:8080 docker-demo:1.0
# 2.查看容器
docker ps
# 结果
CONTAINER ID   IMAGE             PORTS                                                  STATUS         NAMES
78a000447b49   docker-demo:1.0   0.0.0.0:8080->8080/tcp, :::8090->8090/tcp              Up 2 seconds   dd
f63cfead8502   mysql             0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   Up 2 hours     mysql

# 3.访问
curl localhost:8080/hello/count
# 结果：
<h5>欢迎访问黑马商城, 这是您第1次访问<h5>
# 查看日志
docker logs -f dd
```

## **网络**
默认情况下，所有容器都是以 bridge 方式连接到 Docker 的一个虚拟网桥上：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785121205792-81dfbdd0-cd46-4181-863d-9ad039666276.png" width="726" title="" crop="0,0,1,1" id="u14c76bea" class="ne-image" style="font-size: 14px">

我们创建了一个Java项目的容器，而Java项目往往需要访问其它各种中间件，例如MySQL、Redis等。

现在，我们的容器之间能否互相访问呢？我们来测试一下

首先，我们查看下MySQL容器的详细信息，重点关注其中的网络IP地址：

```bash
# 1.用基本命令，寻找Networks.bridge.IPAddress属性
docker inspect mysql
# 也可以使用format过滤结果
docker inspect --format='{{range .NetworkSettings.Networks}}{{println .IPAddress}}{{end}}' mysql
# 得到IP地址如下：
172.17.0.3

# 2.然后通过命令进入dd容器
docker exec -it dd bash

# 3.在容器内，通过ping命令测试网络
ping 172.17.0.2
# 结果
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.053 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.059 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.058 ms
```

发现可以互联，没有问题。

但是，容器的网络IP其实是一个虚拟的IP，其值并不固定与某一个容器绑定，如果我们在开发时写死某个IP，而在部署时很可能MySQL容器的IP会发生变化，连接会失败。

所以，我们必须借助于docker的网络功能来解决这个问题，官方文档：

[<font style="color:rgb(0, 56, 132);">https://docs.docker.com/engine/reference/commandline/network/</font>](https://docs.docker.com/engine/reference/commandline/network/)

常见命令有：

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">说明</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker network create</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">创建一个网络</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker network ls</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看所有网络</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker network rm</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除指定网络</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker network prune</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">清除未使用的网络</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker network connect</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">使指定容器连接加入某网络</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker network disconnect</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">使指定容器连接离开某网络</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker network inspect</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看网络详细信息</font> |


+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">自定义网络</font>

```bash
# 1.首先通过命令创建一个网络
docker network create hmall

# 2.然后查看网络
docker network ls
# 结果：
NETWORK ID     NAME      DRIVER    SCOPE
639bc44d0a87   bridge    bridge    local
403f16ec62a2   hmall     bridge    local
0dc0f72a0fbb   host      host      local
cd8d3e8df47b   none      null      local
# 其中，除了hmall以外，其它都是默认的网络

# 3.让dd和mysql都加入该网络，注意，在加入网络时可以通过--alias给容器起别名
# 这样该网络内的其它容器可以用别名互相访问！
# 3.1.mysql容器，指定别名为db，另外每一个容器都有一个别名是容器名
docker network connect hmall mysql --alias db
# 3.2.db容器，也就是我们的java项目
docker network connect hmall dd
# 也可以在创建容器时用--network加入网络
docker run -d --name dd -p 8080:8080 --network hmall docker-demo:1.0

# 4.进入dd容器，尝试利用别名访问db
# 4.1.进入容器
docker exec -it dd bash
# 4.2.用db别名访问
ping db
# 结果
PING db (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.056 ms
# 4.3.用容器名访问
ping mysql
# 结果：
PING mysql (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.044 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.054 ms
```

OK，现在无需记住IP地址也可以实现容器互联了。

**总结**：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">在自定义网络中，可以给容器起多个别名，默认的别名是容器名本身</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">在同一个自定义网络中的容器，可以通过别名互相访问</font>

# **项目部署**
项目说明：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall：商城的后端代码</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall-portal：商城用户端的前端代码</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall-admin：商城管理端的前端代码</font>

部署的容器及端口说明：

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">项目</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">容器名</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">端口</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">备注</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">8080</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">黑马商城后端API入口</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall-portal</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">nginx</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">18080</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">黑马商城用户端入口</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall-admin</font> |                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">18081</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">黑马商城管理端入口</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">mysql</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">mysql</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">3306</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据库</font> |


在正式部署前，我们先删除之前的nginx、dd两个容器：

```bash
docker rm -f nginx dd
```

mysql容器中已经准备好了商城的数据，所以就不再删除了。

## **部署Java项目**
hmall项目是一个maven聚合项目，使用IDEA打开hmall项目，查看项目结构如图：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785121276464-ea3a1b98-0823-45c8-bbde-9f662820e05e.png" width="864" title="" crop="0,0,1,1" id="u87e12fa5" class="ne-image" style="font-size: 14px">



我们要部署的就是其中的hm-service，其中的配置文件采用了多环境的方式：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120747636-3ee60960-639a-4395-98e3-c5c0dce41a2f.png" width="898" title="" crop="0,0,1,1" id="u623d313a" class="ne-image" style="font-size: 14px">

其中的`application-dev.yaml`是部署到开发环境的配置，`application-local.yaml`是本地运行时的配置。

查看application.yaml，你会发现其中的JDBC地址并未写死，而是读取变量：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120747725-68b47b09-abbc-4ddd-8eb7-ba1aa134e2bd.png" width="1405" title="" crop="0,0,1,1" id="uaf108e40" class="ne-image" style="font-size: 14px">

这两个变量在application-dev.yaml和application-local.yaml中并不相同：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120747814-a29b6369-9371-403a-a4dc-b8000c6a95e5.png" width="1049" title="" crop="0,0,1,1" id="u990178ca" class="ne-image" style="font-size: 14px">

在dev开发环境（也就是Docker部署时）采用了mysql作为地址，刚好是我们的mysql容器名，只要两者在一个网络，就一定能互相访问。

我们将项目打包：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120748286-f06d0612-fc2e-4907-b8b7-a01a7e843b2d.png" width="924" title="" crop="0,0,1,1" id="uff67ae2a" class="ne-image" style="font-size: 14px">

结果：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785121315002-aa142c2e-f822-494c-bac0-1784469e5067.png" width="797" title="" crop="0,0,1,1" id="u3da18c83" class="ne-image" style="font-size: 14px">

将hm-service目录下的Dockerfile和hm-service/target目录下的hm-service.jar一起上传到虚拟机的root目录：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785121314847-2864ad87-e960-4700-9db9-e25e5b1f4610.png" width="1016" title="" crop="0,0,1,1" id="u102d78a7" class="ne-image" style="font-size: 14px">



部署项目：

```bash
# 1.构建项目镜像，不指定tag，则默认为latest
docker build -t hmall .

# 2.查看镜像
docker images
# 结果
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
hmall         latest    0bb07b2c34b9   43 seconds ago   362MB
docker-demo   1.0       49743484da68   24 hours ago     327MB
nginx         latest    605c77e624dd   16 months ago    141MB
mysql         latest    3218b38490ce   17 months ago    516MB

# 3.创建并运行容器，并通过--network将其加入hmall网络，这样才能通过容器名访问mysql
docker run -d --name hmall --network hmall -p 8080:8080 hmall
```

测试，通过浏览器访问：http://你的虚拟机地址:8080/search/list

项目结构：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120749322-f5c6b43d-368e-4ff4-a506-773fb503b5b3.png" width="432" title="" crop="0,0,1,1" id="uc6b977c8" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120748912-9c278211-3851-4374-b944-b5bb98b4f7e8.png" width="476" title="" crop="0,0,1,1" id="u6688bb04" class="ne-image" style="font-size: 14px">

dockerfile 用 FROM openjdk:11.0-jre-buster 就行

## **部署前端**
hmall-portal和hmall-admin是前端代码，需要基于nginx部署。在课前资料中已经给大家提供了nginx的部署目录：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120749002-91db7735-7f70-4c77-8399-3b5d08a9e89d.png" width="714" title="" crop="0,0,1,1" id="u175bf7ca" class="ne-image" style="font-size: 14px">

其中：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">html</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">是静态资源目录，我们需要把</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall-portal</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">以及</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hmall-admin</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">都复制进去</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">nginx.conf</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">是nginx的配置文件，主要是完成对</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">html</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">下的两个静态资源目录做代理</font>

我们现在要做的就是把整个nginx目录上传到虚拟机的/root目录下：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120749008-a52ad206-b973-4423-b415-5d5da91713b1.png" width="701" title="" crop="0,0,1,1" id="u78fcdb01" class="ne-image" style="font-size: 14px">

然后创建nginx容器并完成两个挂载：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">把</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/root/nginx/nginx.conf</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">挂载到</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/etc/nginx/nginx.conf</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">把</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/root/nginx/html</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">挂载到</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">/usr/share/nginx/html</font>

由于需要让 nginx 同时代理 hmall-portal 和 hmall-admin 两套前端资源，因此我们需要暴露两个端口：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">18080：对应hmall-portal</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">18081：对应hmall-admin</font>

命令如下：

```bash
docker run -d \
  --name nginx \
  -p 18080:18080 \
  -p 18081:18081 \
  -v /root/nginx/html:/usr/share/nginx/html \
  -v /root/nginx/nginx.conf:/etc/nginx/nginx.conf \
  --network hmall \
  nginx
```

测试，通过浏览器访问：http://你的虚拟机ip:18080

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785120750318-1b3680e9-566c-46b8-86e0-956196ffceeb.png" width="649" title="" crop="0,0,1,1" id="u1ecfec75" class="ne-image" style="font-size: 14px">

## **DockerCompose**
大家可以看到，我们部署一个简单的java项目，其中包含3个容器：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">MySQL</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Nginx</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Java项目</font>

而稍微复杂的项目，其中还会有各种各样的其它中间件，需要部署的东西远不止3个。如果还像之前那样手动的逐一部署，就太麻烦了。

而Docker Compose就可以帮助我们实现**多个相互关联的Docker容器的快速部署**。

它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器。

### **基本语法**
docker-compose.yml文件的基本语法可以参考官方文档：

[<font style="color:rgb(0, 56, 132);">https://docs.docker.com/compose/compose-file/compose-file-v3/</font>](https://docs.docker.com/compose/compose-file/compose-file-v3/)

docker-compose文件中可以定义多个相互关联的应用容器，每一个应用容器被称为一个服务（service）。由于service就是在定义某个应用的运行时参数，因此与docker run参数非常相似。

举例来说，用docker run部署MySQL的命令如下：

```bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  --network hmall
  mysql
```

如果用docker-compose.yml文件来定义，就是这样：

```bash
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
    networks:
      - new
networks:
  new:
    name: hmall
```

对比如下：

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker run 参数</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">docker compose 指令</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">说明</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">--name</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">container_name</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">容器名称</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-p</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ports</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">端口映射</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-e</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">environment</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">环境变量</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-v</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">volumes</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据卷配置</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">--network</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">networks</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">网络</font> |


明白了其中的对应关系，相信编写docker-compose文件应该难不倒大家。

黑马商城部署文件：

```bash
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 1234
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    networks:
      - hm-net
  hmall:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: hmall
    ports:
      - "8080:8080"
    networks:
      - hm-net
    depends_on:
      - mysql
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "18080:18080"
      - "18081:18081"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    depends_on:
      - hmall
    networks:
      - hm-net
networks:
  hm-net:
    name: hmall
```

### **基础命令**
编写好docker-compose.yml文件，就可以部署项目了。常见的命令：

[<font style="color:rgb(0, 56, 132);">https://docs.docker.com/compose/reference/</font>](https://docs.docker.com/compose/reference/)

基本语法如下：

```bash
docker compose [OPTIONS] [COMMAND]
```

其中，OPTIONS和COMMAND都是可选参数，比较常见的有：

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">类型</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">参数或指令</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">说明</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Options</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-f</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">指定compose文件的路径和名称</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-p</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">指定project名称。project就是当前compose文件中设置的多个service的集合，是逻辑概念</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Commands</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">up</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">创建并启动所有service容器</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">down</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">停止并移除所有容器、网络</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ps</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">列出所有启动的容器</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">logs</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看指定容器的日志</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">stop</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">停止容器</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">start</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">启动容器</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">restart</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">重启容器</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">top</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查看运行的进程</font> |
|                                                              | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">exec</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">在指定的运行中容器中执行命令</font> |


教学演示：

```bash
# 1.进入root目录
cd /root

# 2.删除旧容器
docker rm -f $(docker ps -qa)

# 3.删除hmall镜像
docker rmi hmall

# 4.清空MySQL数据
rm -rf mysql/data

# 5.启动所有, -d 参数是后台启动
docker compose up -d
# 结果：
[+] Building 15.5s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                    0.0s
 => => transferring dockerfile: 358B                                                    0.0s
 => [internal] load .dockerignore                                                       0.0s
 => => transferring context: 2B                                                         0.0s
 => [internal] load metadata for docker.io/library/openjdk:11.0-jre-buster             15.4s
 => [1/3] FROM docker.io/library/openjdk:11.0-jre-buster@sha256:3546a17e6fb4ff4fa681c3  0.0s
 => [internal] load build context                                                       0.0s
 => => transferring context: 98B                                                        0.0s
 => CACHED [2/3] RUN ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo   0.0s
 => CACHED [3/3] COPY hm-service.jar /app.jar                                           0.0s
 => exporting to image                                                                  0.0s
 => => exporting layers                                                                 0.0s
 => => writing image sha256:32eebee16acde22550232f2eb80c69d2ce813ed099640e4cfed2193f71  0.0s
 => => naming to docker.io/library/root-hmall                                           0.0s
[+] Running 4/4
 ✔ Network hmall    Created                                                             0.2s
 ✔ Container mysql  Started                                                             0.5s
 ✔ Container hmall  Started                                                             0.9s
 ✔ Container nginx  Started                                                             1.5s

# 6.查看镜像
docker compose images
# 结果
CONTAINER           REPOSITORY          TAG                 IMAGE ID            SIZE
hmall               root-hmall          latest              32eebee16acd        362MB
mysql               mysql               latest              3218b38490ce        516MB
nginx               nginx               latest              605c77e624dd        141MB

# 7.查看容器
docker compose ps
# 结果
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
hmall               root-hmall          "java -jar /app.jar"     hmall               54 seconds ago      Up 52 seconds       0.0.0.0:8080->8080/tcp, :::8080->8080/tcp
mysql               mysql               "docker-entrypoint.s…"   mysql               54 seconds ago      Up 53 seconds       0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp
nginx               nginx               "/docker-entrypoint.…"   nginx               54 seconds ago      Up 52 seconds       80/tcp, 0.0.0.0:18080-18081->18080-18081/tcp, :::18080-18081->18080-18081/tcp
```

打开浏览器，访问：http://yourIp:8080