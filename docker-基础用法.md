#### Centos 7 安装Docker

```bash
1、yum包：
https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
2、默认包中是docker官网的，安装速度会慢，需改为镜像站的，编辑docker-ce.repo
输入： 
:%s@https://download.docker.com@https://mirrors.tuna.tsinghua.edu.cn/docker-ce@
#把文中docker官网的改为镜像站的即可

3、安装：yum -y install docker-ce
```



#### Docker组件

```bash
Docker程序环境：
	环境配置文件：
			/etc/sysconfig/docker-network
			/etc/sysconfig/docker-storage
			/etc/sysconfig/docker
	Unit File:
			/usr/lib/systemd/system/docker.service
	Docker Registry配置文件：
			/etc/contalners/registries.conf
	DOcker-ce:
			配置文件：/etc/docker/daemon.json 
			#默认没有这个文件，第一次启动后才会有
	Docker镜像加速器：
			docker cn 
			阿里云加速器
			中国科技大学
{
  "registry-mirrors": ["https://hne2skpv.mirror.aliyuncs.com"]
}

	启动docker
	systemctl start docker.service
			
	注册阿里云账号，专用加速器地址获取路径
			https://cr.console.aliyun.com/#/accelerator
	
	物理：
		Client  <---> Daemon <---> Registry Server
		
	逻辑：
		Containers:容器
		Images:镜像、映像
		Registry：Image Repositories
	容器的状态：
		created:
		runing:
		paused:
		stopped:
	
	docker:
			images
			#查看本地镜像
			pull
			#拉取镜像
			run
			#运行镜像(容器)
			ps
			#查看当前运行的容器
	查看docker相关的信息：
			version
			#查看docker版本
			info
			#查看当前docker一些详细信息
	docker 
			images
			rmi
			#删除本地镜像
			pull
			
	容器：
    	run:创建并运行一个容器
    	create:创建一个容器
    	start:启动一个处于停止状态的容器
    	
    	创建：
    		create
    		run
    		#run创建并运行
    	
    	启动：
    		start
    		
    	停止
    		stop
    		kill
    		#强制关闭
	
		重启：
        	restart
        	
        暂停和继续：
        	pause
        	uppause
        
        删除容器：
        		rm
        		run --rm
        
        创建容器：
        	基于"镜像文件"
        		镜像文件有默认要运行的程序
        	
        	注意：
        		运行的容器内部必须有一个工作前台的运行的进程
        		docker的容器的通常也是仅为运行一个程序
        				要想在容器内运行多个程序，一般需要提供一个管控程序，例如：supervised
        				
        	run,create
        		--name:容器名称
        		--rm: 容器运行停止即可自行删除
        		--network BRIDGE :让容器加入的网络
        				默认为：docker0;
        		
        		交互式启动一个容器:
        			-i: interactive,交互式
        			-t: Allocate a pseudo-TTY
        		
        			attach:附加至牟云霞状态的容器的终端设备
        			exec: 让运行中的容器运行一个额外的程序
        			
        			查看：
        			 logs :Fetch the logs of a container,容器内部程序运行时输出到终端的信息
        			 ps:List containers
        			 		-a, --all:列出所有容器
        			 		--filter，-f:过滤条件显示
        			 				name=
        			 				status={stopped|runnig|paused}
        			 stats:动态方式显示容器的资源占用状态
        			 top: Display  the running processes of a container
    		
```



#### docker基础命令实例

```bash
docker search   nginx 
#搜索一个nginx的镜像

docker  pull nginx
#拉取nginx镜像

docker  ps 
#查看当前正在运行的容器

docker  ps -a 
#查看所有容器的状态

docker  images
#查看本地镜像

docker rmi  镜像id
#删除本地镜像

docker  rm  镜像id
#删除暂停的镜像

docker kill  镜像id
#强制结束一个运行的容器

docker restart  镜像id
#重启一个容器

docker pause   镜像id
#暂停一个容器

docker  unpause  镜像id
#取消暂停

docker  run --name nginx  -d  --rm nginx   
#创建一个容器并创建，--name:名字为nginx,查看就不用输入容器Id了(便捷)，-d:后台运行,--rm:容器停止自动删除

docker  run --name nginx -d -p 80:80  nginx 
#-p:指定映射到本地的端口

docker run --name -it nginx /bin/bash 
#-i：交互式  -t:tty(终端)   指定为：/bin/bash

docker exec -it  nginx /bin/bash
#进入容器终端模式(让运行中的容器运行一个额外的程序)

docker  inspect nginx
#查看指定容器的详细信息

docker logs nginx
#查看容器的日志，什么服务就是什么日志

docker stats nginx
#动态显示容器的资源占用状况

docker  top nginx
#查看容器负载

docker commit -p  nginx   nginx-html:v.2
#commit:打包当前正在运行的镜像   -p：暂停容器(因为容器在运行，不暂停可能会保存到写入到一半的文件)   nginx: --name参数，  最后一个nginx-html:打包并添加标签

docker commit -a "yyc" -c 'CMD ["/bin/httpd","-f","-h","/data/html/"]' nginx  nginx:1.v
#-c:更改容器默认执行的命令

docker tag   ningx   ningx-html
#在nginx的镜像上在创建一个标签为 ningx-html的镜像


docker login 
#在机器中登录到docker -hub上

docker  push  nginx 
#需现在docker上线创建仓库

docker  save -o  dabao.gz   nginx:1.1  nginx:1.2
#保存镜像文件，-o :指定输出的文件

docker  load -i  1.gz 
#导入打包的镜像

```



#### docker网络

```bash
yum  -y install bridge-utils

brctl show 
#查看

docker network inspect bridge
#查看bridge网络配置

docker run --name t1 -it --network bridge -h  zhujiming --dns 114.114.114.114  --dns-search  ilinux.io  --add-hosts  www.wanrou.io:1.1.1.1  --rm   last

docker run --name tl -d  -p 192.168.1.1::80  nginx
#:: :随机端口
docker run --name tl -d -p 80:80 nginx 
#指定都为80端口

docker  run --name tl -d -p 192.168.1.11:80:80  nginx

docker  run --name b2  --network  container:b1  -it --rm nginx
#b2跟b1共享一个ip

docker run --name b2  -it --network host nginx  
#host:跟物理机共享一个ip

docker run --name nginx -d nginx
docker run --name  nginx2 -it   --network container:nginx  nginx /bin/bash
#运行2个容器，公用一个Ip


dockerd守护进程的C/S，其默认仅监听Unix SOcket格式的地址，/var/run/docker.sock;如果使用TCP套接字， /etc/docker/daemon.json:
"hosts": ["tcp://0.0.0.0:2375", "unix:///var/run/docker.sock"] 也可向dockerd直接传递“-H|--host”选项;
#修改配置文件后，可执行 systemctl reset-failed docker.service重载下配置文件(注：修改配置文件需暂停已经开启的容器并关闭docker服务)

配置后，其他机器链接：docker -H  172.18.0.1：2375  ps #链接远程docker机器执行命令

docker  network create -d bridge --subnet "172.26.0.0/16" --gateway "172.26.0.1"  mybr0
#创建一个网络卡，并指定为bridge模式，设置了IP段和忘光，mybr0是名称
ip  link set dev  eht0    name  docker1
#需当掉ip

docker run --name t1 -it --net mybr0
#不同的网络设备需开启网络转发

ip netns add r1  
ip netns add r2

ip netns exec r1 ifconfig -a 

ip link add name veth1.1 type veth peer name veth1.2

ip. link set dev veth1.2 netns r1


ip netns exec r1 ip linke set dev  veth1.2 name eth0 
```



#### 自定义docker0桥的网络属性信息：/etc/docker/daemon.json文件

```bash
自定义docker0桥的网络属性信息:/etc/docker/daemon.json文件 {
        "bip": "192.168.1.5/24",
        "fixed-cidr": "10.20.0.0/16",
        "fixed-cidr-v6": "2001:db8::/64",
        "mtu": 1500,
        "default-gateway": "10.20.1.1",
        "default-gateway-v6": "2001:db8:abcd::89",
        "dns": ["10.20.1.2","10.20.1.3"]
}
核心选项为bip，即bridge ip之意，用于指定docker0桥自身的IP地址;其它选项可通过此地址计算得出。
```

#### docker关联目录

```bash
docker  run --name nginx -it -v  /data   nginx /bin/bash
#-v  /data/   在容器上指定data目录与本地机器关联，在本机查看关联到的目录
docker inspect nginx 

        "Mounts": [
            {
                "Type": "volume",
                "Name": "490b52ae55d66d5300ad06212662e9d5e1b9d2560768cb81ec23b0cfbd819eb9",
                "Source": "/var/lib/docker/volumes/490b52ae55d66d5300ad06212662e9d5e1b9d2560768cb81ec23b0cfbd819eb9/_data",
                "Destination": "/data",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],

关联到本地指定目录：
docker run  --name nginx -it -v /data/docker/b1:/data  nginx  /bin/bash
#容器中的data目录关联到本地机器的/data/docker/b1目录上。(注：本地目录操作什么，容器上也会有对应的数据，就算删除了这个容器，在以这样的执行数据还是在)


nginx容器：
docker  run -it --name nginx -v /docker/data/v1:/data/ nginx /bin/bash
#创建nginx容器并创建data目录跟宿主机关联到/docker/data/v1中

docker run --name wooyun -it --network container:nginx  --volumes-from nginx chanhoo/wooyun /bin/bash
#创建wooyun容器，并复制nginx的网络和volumes关联器
```

#### 从docker inspect中获取指定数据段

```bash
docker inspect  -f {{.Mounts}} nginx
或
docker inspect  -f {{.NetworkSettings.Gateway}} nginx
#注意，Gateway在NetworkSettings的代码段下，多个容器可挂载同一个宿主机目录
```



#### Dockerfile

```bash
FROM
FROM指令是最重的一个且必须为Dockerfile文件开篇的第一个非注释行，用于为映像文件构建过程指定基准镜像，后续的指令运行与此基准镜像所提供的运行环境

实践中，基准镜像可以是任何可用镜像文件，默认情况下，docker build会在docker主机上查找指定的镜像文件，在其不存在时，则会从Docker Hub Registry上拉取所需的镜像文件
如果找不到指定的镜像文件，docker build会返回一个错误信息

Syntax
FROM<repository>[:<tag>]或
FROM<repository>@<digest>
<reposotiry>:指定作为base image的名称
<tag>:base image的标签，为可选项，省略时默认作为latest


COPY
用于从Docker主机赋值文件至创建的新映像文件
Syntax
COPY <src>....<dest>或
COPY ["<src>"...."<dest>"]
	<src>:要复制的源文件或目录，支持使用通配符
	<dest>:目标路径，即正在创建的image的文件系统路径，建议为<dest>使用绝对路径，否则，
	COPY指定则以workdir为其起始路径：
注意：在路径中有空白字符时，通常使用第二种格式

文件复制准则
<src>必须是build上下文中的路径，不能是其父目录中的文件
如果<src>是目录，则其内部文件或子目录会被递归复制，但<src>目录自身不会被复制
如果指定了多个<src>，或在<src>中使用了通配符，则<dest>必须是一个目录，且必须以/结尾

如果<dest>事先不存在，它将会被自动创建，这包括其父目录路径 


ADD
ADD指令类似于COPY指令，ADD支持使用TAR文件和URL路径
Syntax：
	ADD <src>...<dest>或
	ADD ["<src>"...."<dest>"]
操作准则：
同COPY指令
如果<src>为URL且<dest>不已/结尾，则<src>指定的文件将被下载并直接被创建为<dest>,如果<dest>以/结尾，则文件名URL指定的文件将被直接下载并保存为<dest>/<filename>

如果：<src>是一个本地系统上的压缩格式的tar文件，它将被展开为一个目录，其行为类似于"tar -x"命令，而然，公告URL获取到的tar文件将不会自动展开。
如果<src>有多个，或其间接或直接使用了通配符，则<dest>必须是一个以/结尾的目录路径，如果<dest>不以/结尾，则其被视作一个普通文件，<src>的内容将被直接写入到<dest>

VOLUME
用于在image中创建一个挂载点目录，以挂载点Docker  host上的卷或其它容器上的卷

Syntax：
VOLUME <mountpoin>或
VOLUME ["<mountpoint>"]
如果挂载点目录路径下此前在文件存在，docker run 命令会在卷挂载完成后将此前的所有文件复制到新挂载的卷中

EXPOSE
用于为容器打开指定要监听的端口以实现与外部通信
Syntax
EXPOSE <port>[/<protocol>][<port>[/<protocol>]...]
<protocol>用于指定传输层协议，可为tcp或udp二者之一，默认为TCP协议

EXPOSE指令可一次指定多个端口，例如；
EXPOSE 11211/udp  11211/tcp 


WORKDIR  :指定工作目录

ENV
用于为镜像定义所需的环境变量，并可被Dockerfile文件中位于其后的其他指令(如：ENV、ADD、COPY等)所调用
调用格式为$variable_name或${variable_name}

Syntax
	ENV<key> <value>或
	ENV<key>=<value>..
第一种格式中，<key>之后的所有内容均会被视作其<value>的组成部分，因此，一次只能设置一个变量

第二种格式可用一次设置多个变量，每个变量为一个"<key>=<value>"的键值对，如果<value>中包含空格，可以以反斜线(\)进行转义，也可以通过对<value>加引号进行标识，另外，反斜线也是可用于续行

定义多个变量时，建议使用第二种方式，以便在同一层中完成所有功能


变量：
${variable:-word}  表示variable变量没有声明时或为空，用word的值，如果variables有值，那就使用它自己的值，word相当于一个默认值

${variable:+word} 表示variable变量有值则使用word值，没值那就没了


CMD 
类似于RUN指令，CMD指令也可用于裕兴任何命令或应用程序，不过，二者的运行时间点不同
RUN指令运行于映像文件构建过程中，而CMD指令运行基于Dockerfile构建出的新映像文件启动一个容器时

CMD指令的首要目的在于为启动的容器指定默认要运行的程序，且其运行结束后，容器也将终止，不过，CMD指令的命令其可以被docker run的命令行选项所覆盖
在Dockerfile中可以存在多个CMD指令，但仅最后一个会生效

Syntax:
CMD<command>或
CMD["<executable>","<param1>","<param2>"]或
CMD["<param1>","<param2>"]
前两种语法格式的意义同RUN
第三种则用于为ENTRYPOINT指令提供默认参数

RUN
用于指定docker build过程中运行的程序，其可以是任何命令
Syntax
RUN <command>或
RUN["<executable>","<param1>","param2"]
第一种格式中，<command>通常是一个shell命令，且以"/bin/sh -c"来运行它，这意味着此进程在容器中的PID不为1，不能接收Unix信号，因此，当使用docker stop <container>命令停止容器时，此进程接受不到SIGTERM信号

第二中语法格式的参数是一个JSON格式的数组，其中<executable>为要运行的命令，后面的<paramN>为传递给命令的选项或参数，然而，此种格式指定的命令不会以"/bin/sh -c"来发起，因此常见的shell操作如果变量替换以及通配符(?.*)替换将不会进行，不过，如果要运行的命令依赖于此shell特性的花，可以将其替换为类型下面的格式
RUN ["/bin/bash","-c","<executable>","param1"]


ENTRYPOINT
类型CMD指令的功能，用于为容器指定默认运行程序，从而使得容器像是一个单独的可执行程序
与CMD不同的是，由ENTAYPOINT启动的程序不会被docker run命令行指定的参数所覆盖，而且，这些命令行参数会被当做参数穿肚给ENTARPOINT指定的程序
	不过，docker run命令的--entrypoint选项的参数可覆盖ENTRYPOINT指令指定的程序
	
Sytax
	ENTRYPOINT <command>
	ENTRYPOINT ["<executable>","<param1>","param2"]
docker run 命令传入的命令参数会覆盖CMD指令的内容并且附加到ENTRYPOINT命令最后做为其参数使用
Dockerfile文件中也可以存在多个ENTRYPOINT指令，但仅有最后一个会生效


USER
用于指定允许images时的或允许Dockerfile中任何RUN、CMd或ENTRYPOINT
指令指定的程序时的用户名或UID
默认情况下，container的允许身份为root用户

Syntax
USER <UID>|<UserName>
需要注意的是，<UID>可以为任意数字，但实践中其必须为/etc/passwd中某用户的有效UID，否则，docker run 命令将运行失败
```

####  实例

```bash
FROM busybox
MAINTAINER "yyclinux"
COPY yum.repos.d/  /etc/yum.repos.d/
ENV  ROOT=/DATA/WWW/HTML/ \
     SERVER="nginx-1.12.2"
COPY index.html ${ROOT:-/DATA/WWW/HTML/}
WORKDIR /usr/local/
ADD  http://nginx.org/download/${SERVER}.tar.gz  ./src/
VOLUME /DATA/mysql/
RUN cd  /usr/local/src/ && \
    tar -xf ${SERVER}.tar.gz
```



