#### 一、Docker的安装

##### Linux安装

```bash
# Linux 安装
# 1、卸载docker
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2、安装依赖包
yum install -y yum-utils
# 3、设置镜像的仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo   
    
yum-config-manager \
    --add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo  # 安装阿里云镜像。
# 4、更新yum软件包索引
yum makeche fast
# 5、安装docker相关，  docker-ce 社区版， ee企业版
yum install docker-ce docker-ce-cli containerd.io
```

卸载docker

```shell
# 1、卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2、删除资源
rm -rf /var/lib/docker
# 3、删除容器
rm -rf /var/lib/containerd
```



##### Mac 安装

#### 二、Docker的常见命令

##### docker 镜像命令

docker images  查看所有本地的主机上的镜像

![image-20210816224502114](/Users/clear/Library/Application Support/typora-user-images/image-20210816224502114.png)

```shell
REPOSITORY  镜像的仓库源
TAG 				镜像的标签
IMAGE ID		镜像id
CREATED			镜像的创建时间
SIZE				镜像的大小
```

docker search 搜索镜像

```bash
clear@cleardeMBP ~ % docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   11281     [OK]

# 可选项，通过搜索来过滤
 --filter=STARS=3000   # 搜索stars 大于3000的mysql
 
```

docker  pull：从镜像仓库中拉取或者更新指定镜像，在未声明镜像标签时，默认标签为latest。

```bash
Usage: docker pull [OPTIONS] NAME[:TAG|@DIGEST] 
Options: 
    -a 拉取某个镜像的所有版本
    --disable-content-trust 跳过校验，默认开启
# 下载mysql
clear@cleardeMBP ~ % docker pull mysql
Using default tag: latest                  # 如果不写tag，默认就是latest
latest: Pulling from library/mysql
33847f680f63: Pull complete								# 分层下载，docker image 的核心，联合文件系统
5cb67864e624: Pull complete
1a2b594783f5: Pull complete
b30e406dd925: Pull complete
48901e306e4c: Pull complete
603d2b7147fd: Pull complete
802aa684c1c4: Pull complete
715d3c143a06: Pull complete
6978e1b7a511: Pull complete
f0d78b0ac1be: Pull complete
35a94d251ed1: Pull complete
36f75719b1a9: Pull complete
Digest: sha256:8b928a5117cf5c2238c7a09cd28c2e801ac98f91c3f8203a8938ae51f14700fd 	# 签名		
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest        # 真实地址


 example
 nacos : docker pull nacos 
```

docker rmi   删除镜像

```
docker rmi -f imageid   # 指定容器的id
docker rmi -f  容器id 容器id   # 删除多个容器
docker rmi -f $(dcoker images -aq)  # 删除全部的容器
```

##### docker容器命令

###### 基本容器命令

1. run：创建并启动一个容器

   ```bash
   Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
   Options:
     -d, --detach 后台运行容器，并输出容器ID
     -e, --env list 设置环境变量，该变量可以在容器内使用
     -h, --hostname string 指定容器的hostname
     -i, --interactive 以交互模式运行容器，通常与-t同时使用
     -l, --label list 给容器添加标签
     --name string 设置容器名称，否则会自动命名
     --network string 将容器加入指定网络
     -p, --publish list 设置容器映射端口
     -P,--publish-all 将容器设置的所有exposed端口进行随机映射(大写)
     --restart string 容器重启策略，默认为不重启
       on-failure[:max-retries]：在容器非正常退出时重启，可以设置重启次数。
       unless-stopped：总是重启，除非使用stop停止容器
       always：总是重启
     --rm 容器退出时则自动删除容器
     -t, --tty 分配一个伪终端
     -u, --user string 运行用户或者UID
     -v, --volume list 数据挂载
     -w, --workdir string 容器的工作目录
     --privileged 给容器特权
     
     example
   启动nacos：  docker run --env MODE=standalone --name nacos -d -p 8848:8848 bdf60dc2ada3
   
   centos
   安装：docker pull centos
   启动：docker run -it centos /bin/bash     # -it 进入容器中，交互的方式是/bin/bash
   
   
   ```

2. ps：列出当前正在运行的容器 

   ```sh
   可选参数
   -a  		# 列出当前正在运行的容器+历史运行过的容器
   -n=?		# 添加参数，显示最近创建的容器
   -q      # 只显示容器的编号
   ```

3. push：将容器推送到镜像仓库

4. 退出容器

   ```sh
   exit   					# 退出容器
   ctrl + P+ Q  		# 容器不停止，退出
   
   ```

5. 删除容器

   ```sh
   docker rm 容器id   										 #  删除指定的容器，不能删除正在运行的容器
   	
   docker rm -f $(docker images -aq)			# 删除所有的容器
   docker ps -a| xargs docker rm		 			# 删除所有的容器 
   ```

6. 启动和停止容器

   ```sh
   docker start   容器id			# 启动容器
   docker restart 容器id			# 重启容器
   docker stop 	 容器id			# 停止正在运行容器
   docker kill 	 容器id			# 强制停止容器
   ```

   ###### 常用的其他命令

   1. build：通过 Dockerfile 构建镜像

      ```bash
      Usage: docker build [OPTIONS] PATH | URL | -
      Options:
          -f, --file string 指定Dockerfile，默认为当前路径的Dockerfile
          -q, --quiet 安静模式，构建成功后输出镜像ID
          -t, --tag list 给镜像设置tag，name:tag
      
      ```

   2. commit：通过容器创建一个新镜像

   3. cp：在容器和宿主机之间拷贝文件

      ```sh
      # 进入容器中进行拷贝到宿主机中
      docker cp 容器id:容器内路径  宿主机目的地址
      docker cp id:/home/java.txt /home
      # 拷贝宿主机中到容器中
      docker cp 宿主机目的地址 容器id:容器内路径
      ```

   4. exec：向正在运行的容器下发命令

      ```sh
      # 我们通常容器都是使用的后台方式运行的，需要进入容器，修改一些配置
      
      # 命令
      docker exec -it  容器id  /bin/bash
      # 测试
      
      # 另一种 方式
      docker attach 容器id
      
      # docker exec    # 进入当前的容器，开启一个新的终端，可以在里面进行操作
      # docker attach  # 进入容器正在运行的终端，不会启动新的进程  
      
      ```

   5. export：将容器导出为一个tar包

   6. images：列出镜像

   7. import：通过导入tar包的方式创建镜像

   8. kill：杀死一个或多个容器

   9. load：从tar包加载一个镜像

   10. login：登录Docker镜像仓库

   11. logout：退出Docker镜像仓库

   12. logs：显示容器日志

       ```sh
       可选参数
       -f			
       -t			
       --tail	
       # 自己编写脚本
       docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1;done"
       # 查看容器
       docker ps
       # 打印日志
       docker logs -tf --tail 10 容器id   # tf：显示全部的日志     tail 显示的日志
       ```

   13. -d：后台启动

   14. 查看容器进程信息

       ```sh
       # 查看容器中的进程信息
       docker top 容器id
       ```

   15. 查看镜像的元数据

       ```sh
       docker inspect 容器id   #  查看容器的详细信息
       
        
       ```

   16. 使用docker实战，安装常见的组件

```sh
example
# 1、安装nginx
docker pull nginx   # 下载镜像
docker run -d --name nginx01 -p 3344:80 dd34e67e3371  # 运行镜像
docker exec -it nginx01 /bin/bash   # 进入容器
# 2、安装tomcat
docker pull tomcat   # 下载镜像
docker run -d -p 3355:8080 --name tomcat01 tomcat
docker exec -it tomcat01 /bin/bash   # 进入容器
# 问题：发现不能访问 127.0.0.1:3355 ，这是因为阿里云镜像把不必要的文件剔除了，就是 webapps 没有内容，需要拷贝 webapps.dist 的文件到webapps中，发现可以访问了。 
# 问题：在容器外部提供一个映射路径，在外部进行修改，内部也可以自动修改？
# 3、部署 es+ kibana
# 问题：es暴露的端口很多，十分耗内存，es的数据一般需要放置到安全的目录，挂载。
# 注意：启动es非常耗内存，会卡死。需要修改配置文件啊
docker pull elasticsearch   # 下载容器
# 启动容器
docker run -d --name elasticsearch01 -p 9200:9200 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" 容器id
# kibana 怎么连接 es？？？容器内部不是互通的。

```

##### commit镜像

```sh
docker commit # 提交容器成为一个新的副本

# 命令和 git 原理类似
doker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
# 1、安装默认的tomcat
# 2、发现默认的tomcat没有webapps应用，现在自己拷贝一个
# 3、将我们自己的容器通过commit 提交作为一个镜像，我们以后就可以使用这个镜像进行操作。
docker commit -a="clear" -m="add webapps" 6c02406075c3 tomcat02:1.0
```

#### 三、Docker基础

##### Docker出现的背景

传统开发模式：项目+环境配置（mysql+redis+），需要运维人员进行配置环境，很麻烦。

docker开发模式：项目jar+环境配置一起打包，生成镜像，运维只需要下载镜像，就可以运行项目。

docker的隔离技术可以帮助我们运行多个服务，服务之间不互通。 

##### Docker的历史

###### 1、Docker介绍

- ###### docker 是一个开源的应用容器引擎，基于Go语言并遵循apache2.0 协议开源。

- docker可以让开发者打包他们的应用以及依赖包到一个轻量级的、可移植的容器中，然后发布到Linux上，也可以虚拟化。

- 容器完全是沙箱机制，相互之间不会有任何接口，更重要的是容器的开销极低。

###### 2、Docker的应用场景

- web应用的自动化打包和发布，自动化测试和持续集成、发布。
- 在服务型中部署和调整数据库以及其他的后天应用。

###### 3、Docker的基础配置

- **docker.service** 服务配置文件
- 
- Docker daemon配置文件 **daemon.json**

```bash
1、镜像下载和上传并发数
{
 "max-concurrent-downloads": 3,
 "max-concurrent-uploads": 5 
}
2、镜像加速地址：国内直接拉去比较慢，可以配置阿里的镜像
{
 "registry-mirrors": ["https://ucjisdvf.mirror.aliyuncs.com"] 
}
3、私有仓库：Docker默认只信任 TLS 加密的仓库地址，所有非 https 仓库默认无法登陆也无法拉取镜像。
insecure-registries 字面意思为不安全的仓库，通过添加这个参数对非 https 仓库进行授信。可以设置多个 insecure-registries 地址，以数组形式书写，地址不能添加协议头 http。
{
 "insecure-registries": ["<IP>:<PORT>","<IP>:<PORT>"] 
}
4、docker存储驱动
OverlayFS 是一个新一代的联合文件系统，类似于 AUFS ，但速度更快，实现更简单。Docker为 OverlayFS 提供了两个存储驱动程序：旧版的overlay，新版的 overlay2 (更稳定)。
启用 overlay2 条件：Linux内核版本4.0或更高版本。
{ "storage-driver": "overlay2", "storage-opts": ["overlay2.override_kernel_check=true"] }
5、日志驱动
容器在运行时会产生大量日志文件，很容易占满磁盘空间。通过配置日志驱动来限制文件大小与文件的数量。
日志等级分为 debug|info|warn|error|fatal ，默认为 info
{
 "log-level": "warn",
 "log-driver": "json-file", 
"log-opts": { 
   "max-size": "100m", 
   "max-file": "3"
  }, 
}
6、数据存储路径
默认路径为 /var/lib/docker
{ 
"data-root": "/home/docker" 
}
7、docker配置demo
{
 "insecure-registries": ["192.168.2.2:8080"],
 "oom-score-adjust": -1000,
 "exec-opts": ["native.cgroupdriver=systemd"],
 "registry-mirrors": ["https://pb5bklzr.mirror.aliyuncs.com"],
 "storage-driver": "overlay2",
 "storage-opts": ["overlay2.override_kernel_check=true"], 
"log-level": "warn", 
"log-driver": "json-file", 
"log-opts": { 
     "max-size": "100m", 
     "max-file": "3" 
},
"data-root": "/home/docker" 
}
```

###### 4、Docker组成

1. 镜像（image）：docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，通过这个镜像创建多个容器。
2. 容器（container）：docker利用容器技术，独立运行一个或者一组应用，通过镜像进行创建，启动，停止，删除的基本命令
3. 仓库（repository）：仓库就是存放镜像的地方

##### Docker 安装组件

```
1、安装nacos
安装：docker pull nacos/nacos-server
启动：docker run --env MODE=standalone --name nacos -d -p 8848:8848 bdf60dc2ada3
2、安装sentinel
安装：docker pull bladex/sentinel-dashboard:latest
启动： docker run --name sentinel -d -p 8858:8858 aa398704ebd3
3、安装zipkin
安装：docker pull openzipkin/zipkin:latest
启动：docker run -name zipkin -d -p 9411:9411 9b4acc3eb019  # 镜像id


```

#### 四、容器卷

###### 什么是容器卷：将我们容器内的目录，挂载到linux上面，可持久化。

##### 使用数据卷

- [ ] 方式一：使用命令来挂载  -v

  ```sh
  docker run -it -v 主机目录:容器内目录
  
  clear@cleardeMBP docker % docker run -it -v ~/docker/ceshi:/home centos
  # 启动起来可以通过inspect 进行查看信息
  
  ```

  ![image-20210820222959692](/Users/clear/Library/Application Support/typora-user-images/image-20210820222959692.png)

  ![image-20210820223217290](/Users/clear/Library/Application Support/typora-user-images/image-20210820223217290.png)

  ```sh
  # 这样容器内的文件和主机的文件就会同步了，在本地修改文件就会同步到容器中了。
  ```

##### 安装mysql

```sh
# 1、下载容器
docker pull mysql
# 2、运行容器 启动mysql需要密码
-d 后台启动 -p 端口映射 -v 卷挂载  -e 环境配置 --name 容器名字
docker run -d -p 3310:3306 -v ~/docker/mysql/conf:/etc/mysql/conf.d -v ~/docker/mysql/data:/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql

# 启动成功之后就可以使用了。

```

##### 具名和匿名挂载

```sh
# 匿名挂载
- v 容器内地址
docker run -d -p --name nginx01 -v /etc/nginx nginx

# 查看所有的volume 的情况
docker volume ls
local     2b11baa8f6c68b86d0326afae9310ce0ef9b7cc92af96e6ae131da0bd20e7ad0

# 具名挂载  
-P 随机端口号
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
docker volume ls
local     bae8ef241d5380087aa95b2727366c0b7a325a94e19cb3c4dff3fba0f7cec198
local     juming-nginx
# 通过 -v 卷名:容器内路径
# 查看一下这个卷
clear@cleardeMBP docker % docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2021-08-20T15:04:09Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",      # 地址
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

所有的dcoker容器内的卷，没有指定目录的情况下都是在    **/var/lib/docker/volumes/xxx/_data**

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况在使用的   **具名挂载**

```sh
# 如何确定是具名挂载还是匿名挂载，，还是指定路径挂载
-v 容器内路径           	 # 匿名挂载
-v 卷名:容器内路径         # 具名挂载
-v /宿主机路径::容器内路径  # 指定路径挂载！ 

```

扩展：

```sh
# 通过 -v 容器内路径：ro   rw  改变读写权限
ro  readonly     # 只读
rw  readwrite    # 可读可写，默认值

# 一旦这个设置了容器权限，容器对我们挂载出来的内容就有限定了。

docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作！
```

##### 初始Dockerfile

dockerfile就是用来构建docker镜像的构建文件！命令脚本，先体验一下。

通过这个脚本可以生成镜像，镜像是一层一层的，脚本是一个一个的。

```sh
方式二
# 创建一个dockerfile文件，名字随便
# 文件中的内容  指令（大写）参数
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "----end-----"

CMD /bin/bash
# 这里的每个命令，就是镜像的一层

# 创建自己的容器
docker build -f /Users/clear/docker/docker-test-volume/dockerfile -t clear-centos:1.0 .

# 启动自己的容器
 docker run -it b42dcfe6b6c5 /bin/bash
# 查看挂载的外部 路径
docker ps
docker inspect 容器id       # 就可以看到
```

![image-20210822001308979](/Users/clear/Library/Application Support/typora-user-images/image-20210822001308979.png)

![image-20210822001430486](/Users/clear/Library/Application Support/typora-user-images/image-20210822001430486.png)

##### 数据卷容器

多个redis实现同步数据。

```sh
# 启动三个centos
docker01: 	docker run -it --name docker01 b42dcfe6b6c5
docker02: 	docker run -it --name docker02 --volumes-from docker01 b42dcfe6b6c5    
# 在docker01中volume01中新建一个文件，在docker02中volume01中就可以看到这个新建的文件，这样docker01和docker02就共享了。
# 通过--volumes-from 就可以实现容器之间的数据共享。
# 测试：删除docker01 ，查看docker02 和 docker03是否还可以访问这个文件
# 测试依旧存在，
```

![image-20210822101514310](/Users/clear/Library/Application Support/typora-user-images/image-20210822101514310.png)

![image-20210822102001992](/Users/clear/Library/Application Support/typora-user-images/image-20210822102001992.png)

多个mysql同步数据

```sh
# 启动第一个mysql
docker run -d -p 3310:3306 -v ~/docker/mysql/conf:/etc/mysql/conf.d -v ~/docker/mysql/data:/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql

docker run -d -p 3311:3306  -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volume-from mysql01 mysql
# 这个时候，实现两个容器数据同步
```

结论：

容器之间的配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

#### 五、DockerFile

###### 介绍

dockerfile是用来构建docker镜像的文件，命令参数脚本

构建步骤：

1、编写一个dockerfile  文件

2、docker build 构建称为一个镜像

3、docker run 运行镜像

4、docker push 发布镜像（dockerHub、阿里云）

###### Dockerfile的构建过程

**基础知识**

1. 每个保留关键字（指令）都是必须是大写字母
2. 执行从上到下顺序执行
3. #表示注释
4. 每个指令都会创建提交一个新的镜像层，并提交

![查看源图像](https://googtech.io/2020/06/26/Dockerfile%E4%BB%8B%E7%BB%8D%E5%8F%8A%E6%8C%87%E4%BB%A4%E8%AF%B4%E6%98%8E/Docker-Dockerfile-commands.jpg)

dockerfile是面向开发的，我们以后呀发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单

docker镜像逐渐成为企业交付的标准。

步骤：开发，部署，运维

Dockerfile：构建文件，定义一切的步骤，源代码

DockerImages：通过Dockerfile构建生成的镜像，最终发布和运行的产品。

Docker容器：容器就是镜像运行起来提供服务器

###### DockerFile的指令

```sh
FROM									# 基础镜像，
MAINTAINER
RUN
VOLUME
EXPOSE								# 暴露端口
CMD										# ，只有最后一个会生效，可被替代

```

###### 实战测试

```sh
# 创建自己的centos

# 1、编写dockerfile 文件
FROM centos
MAINTAINER clear<312323@qq.com>
ENV MYPATH /usr/local     
WORKDIR $MYPATH     					 #   工作目录，

RUN yum -y install vim  			 # 安装vim和net-tools
RUN yum y install net-tools

EXPOSE 80
CMD echo $MYPATH

CMD echo "------end-----"
CMD /bin/bash
# 2、构建文件  docker build -f mydockerfile-centos -t mycentos:0.1 .
clear@cleardeMBP dockerfile % docker build -f mydockerfile-centos -t mycentos:0.1 .
[+] Building 3.3s (8/8) FINISHED
 => [internal] load build definition from mydockerfile-centos                                                                                                                                          0.0s
 => => transferring dockerfile: 262B                                                                                                                                                                   0.0s
 => [internal] load .dockerignore                                                                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/centos:latest                                                                                                                                       0.0s
 => [1/4] FROM docker.io/library/centos                                                                                                                                                                0.0s
 => CACHED [2/4] WORKDIR /usr/local                                                                                                                                                                    0.0s
 => CACHED [3/4] RUN yum -y install vim                                                                                                                                                                0.0s
 => [4/4] RUN yum -y install net-tools                                                                                                                                                                 2.7s
 => exporting to image                                                                                                                                                                                 0.5s
 => => exporting layers                                                                                                                                                                                0.5s
 => => writing image sha256:e8a178372360d32d4754f1aeae132fbdb2d7f7ac74adc3badf610cf8dbb59c85                                                                                                           0.0s
 => => naming to docker.io/library/mycentos:0.1

# 3、测试运行
 docker run -it mycentos:0.1

```

###### CMD和ENTRYPOINT区别

```sh
CMD    					# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代，
ENTRYPOINT			# 指定这个容器启动 的时候要运行的命令，可以追加命令

example  docker run 容器id  -l    # 对于 CMD 是直接替换，会直接报错，对于ENTRYPOINT是进行追加，不会报错。
dockerFIle 文件
FROM centos
CMD ["ls","-a"]
ENTRYPOINT  ["ls","-a"]
```

###### 实战：Tomcat

```sh
1、准备镜像文件 tomact 压缩包，jdk压缩包
apache-tomcat-9.0.52.tar.gz	jdk-8u301-linux-x64.tar.gz
2、编写dockerfile文件，官方命令 Dockerfile ，不需要 -f 指定
dockerfile 文件

FROM centos
MAINTAINER clear<213213@qq.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u301-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.52.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_301
ENV CLASSPATH #JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.52
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.52
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$$CATALINA_HOME/bin
EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.5/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.5/bin/logs/catalina.out

3、启动容器
# 构建容器
docker build -t diytomcat .
4、 启动容器
docker run -d -p 9090:8080 --name cleartomcat -v /Users/clear/docker/tomcat/test:/usr/local/apache-tomcat-9.0.52/webapps/test -v /Users/clear/docker/tomcat/logs/:/usr/local/apache-tomcat-9.0.52/logs diytomcat

5、访问测试
6、发布项目（由于做了挂载，我们直接在本地编写项目就可以发布了）


```

###### 发布自己的镜像hub

1、地址：https://hub.docker.com/ 注册自己的账号

2、确定这个账号可以登录

3、在我们服务器上提交自己的镜像

4、登录完毕 就可以提交了    docker login -u  账号

```sh
clear@cleardeMBP tomcat % docker login -u 1947551164
Password:
Login Succeeded
```

5、docker push

```sh
# 报错
clear@cleardeMBP tomcat % docker push clear/diytomcat
Using default tag: latest
The push refers to repository [docker.io/clear/diytomcat]
An image does not exist locally with the tag: clear/diytomcat
# 解决 ，增加一个tag

docker push clear/tomcat:1.0
```

###### 发布阿里云镜像

```sh
1、登录阿里云，找到容器镜像服务
2、创建命名空间
3、创建容器镜像
4、看看页面
```

![image-20210824215643523](/Users/clear/Library/Application Support/typora-user-images/image-20210824215643523.png)

#### 六、Docker网络

#####  1、清空环境

##### 2、docker怎么网络连接

```sh
# 1、docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址， ip addr   发现会有一个 eth0 ip地址，docker分配的
# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: tunl0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
3: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN group default qlen 1000
    link/tunnel6 :: brd ::
44: eth0@if45: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# linux 可以 ping 172.17.0.2 

# 原理
# 1、没启动一个docker容器，docker就会给docker容器分配一个ip地址，我们只要安装了docker，就会有一个网卡docker0 桥接模式，使用的技术是 veth-pair技术

# 结论：容器和容器之间是可以ping通的。

# Docker 中的所有的网络接口都是虚拟的，虚拟的转发效率高（内网传递文件），只要容器删除，对应的网桥就没了。

```

##### 3、Docker link

```sh
docker --link

docker exec -it tomcat02 ping tomcat01
#	不能ping通


docker run -d -P --name tomcat03 --link tomcat02 tomcat   # 03 连接02
docker exec -it tomcat03 ping tomcat02										# 可以ping通

# 原理 在tomcat03的hosts文件中配置了tomcat02的ip
# 这种方式 已经不建议使用了，哈哈哈
```

##### 4、自定义网络

容器互联：

###### 网络模式：

bridge：桥接模式（docker默认）

none：不配置网络

host：和宿主机共享网络

container：容器网络连通（用的少！）

**测试**

```sh
# 直接启动一个 tomcat
docker run -d -P --name tomcat01 tomcat
# docker0特点：默认，域名不能访问， -- link可以打通连接

# 我们可以定义一个网络
# --dirver bridge								
# --subnet 192.168.0.0/16					
# --gateway 192.168.0.1    			
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet

docker run -d -P --name tomcat01 --net mynet tomcat  #  指定 网络

```

我们自定义的网络docker都已经帮我们维护好了对应的关系，推荐我们平时使用这样的网路

好处：

redis：不同集群使用不同的网路 ，保证了集群是安全和健康的。

mysql-：

##### 5、网络连通



##### 6、部署redis集群

```sh
# 分片 + 高可用 + 负载均衡
# 创建 6 个 redis
for port in $(seq 1 6); \
do \
mkdir -p ~/Users/clear/docker/redis/node-${port}/conf
touch ~/Users/clear/docker/redis/node-${port}/conf/redis.conf
cat << EOF >~/Users/clear/docker/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 192.168.1.1${port}
cluster-announce-port 6379
appendonly yes
EOF
done

# 启动redis
docker run -p 6371:6379 -p  16371:16379 --name redis-1 \
-v ~/Users/clear/docker/redis/node-1/dre
-v ~/Users/clear/docker/redis/node-1/conf/redis/conf:/etc/redis/redis.conf \
-d  --ip 192.168.1.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf   # 去掉 --net redis

docker run -p 6376:6379 -p  16376:16379 --name redis-6 \
-v ~/Users/clear/docker/redis/node-6/data:/data \
-v ~/Users/clear/docker/redis/node-6/conf/redis/conf:/etc/redis/redis.conf \
-d  --ip 192.168.1.16 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

# 创建集群
redis-cli --cluster create 192.168.1.11:6379 192.168.1.12:6379 192.168.1.13:6379 192.168.1.14:6379 192.168.1.15:6379 192.168.1.16:6379 --cluster-replicas 1


```

#### 七、Docker Compose

#### 八、Docker Swarm

#### 九、Docker Jenkins
