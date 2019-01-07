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

