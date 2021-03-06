Docker极简教程——容器篇
--------------------------------------------------------

### 容器相关操作

--------------------------------------------------------
###### Docker是一个强大的打包工具，非常强大，能够把操作系统打包在里面，进行无差别的部署和运行
###### Docker镜像，类似操作系统的镜像，包括一个完整的可运行系统，操作系统(ubuntu)、基础组件(jvm)、应用程序(java webapp)都可以打包在一个镜像中，镜像可以被导出（tar)，移植到其他系统上
###### Docker容器是个Docker镜像的执行实例，是镜像的运行态的展现形式，它可以被启动、停止、删除，容器可以被看做是一个linux的操作系统，上面运行着某种应用（Java webapp），同一台机器上可以运行多个容器

###### **通俗来讲，镜像好比DVD，容器好比放电影。    ——涛哥** 
--------------------------------------------------------

#### 运行一个容器

##### 使用busybox运行一个echo命令
```bash
docker run busybox echo "hello world"

hello world
```

##### 以交互的方式运行echo命令
```bash
docker run -ti busybox echo "hello world"

hello world
```

##### 以交互的方式运行容器，并在容器中手工执行命令
```bash
docker run -ti ubuntu:14.04 /bin/bash

root@49334d161fa9:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@49334d161fa9:/# exit
exit
```

##### 以守护进程（后台）的方式运行容器
```bash
docker run -d nginx:1.9.1
```

##### 运行容器，并给容器命名，名字具有唯一性
```bash
docker run -d --name mynginx nginx:1.9.1
```

##### 运行容器，并暴露容器的端口
```bash
docker run -d --name mynginx -p 80:80 nginx:1.9.1
```

##### 运行容器，并使用宿主机的端口,如下情况，通过宿主机的IP和8080端口即可访问mynginx容器
```bash
docker run -d --name mynginx --net=host -p 8088:80 nginx:1.9.1 
```

##### 以自动重启的方式运行容器
```bash
docker run --restart=always -d --name mynginx --net=host -p 8080:80 nginx:1.9.1
```

##### 已挂载本地卷的方式运行容器，将nginx的日志写在宿主机的硬盘上
```bash
docker run --restart=always -d --name mynginx --net=host -p 8080:80 -v /apps/nginx/logs/:/var/log/ngxin/ nginx:1.9.1
```

##### 可以暴露多个端口，也可以挂载多个本地卷
```bash
docker run --restart=always -d --name mynginx --net=host -p 8080:80 8443:80 -v /apps/nginx/logs/:/var/log/nginx/ -v /apps/nginx/conf/:/etc/nginx/conf/ nginx:1.9.1
```

##### 查看正在运行的容器
```bash
docker ps -a 

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS                         PORTS                     NAMES
ef7773e32d67        busybox                                           "echo 'hello world'"     44 minutes ago      Exited (0) 44 minutes ago                                distracted_allen
f2b741b412a3        busybox                                           "echo 'hello world'"     About an hour ago   Exited (0) About an hour ago                             loving_hawking
5f282d28149b        ubuntu:v1                                         "/apps/product/tomcat"   5 days ago          Up 5 days                      8080/tcp                  abc.changyou.com
67e8f1fb2791        ubuntu:v1                                         "/apps/product/tomcat"   5 days ago          Created                                                  big_fermi
```

##### 根据名字查找正在运行的容器
```bash
docker ps -a | grep busybox

ef7773e32d67        busybox    "echo 'hello world'"     50 minutes ago      Exited (0) 50 minutes ago                                distracted_allen
f2b741b412a3        busybox    "echo 'hello world'"     About an hour ago   Exited (0) About an hour ago                             loving_hawking
```

##### 停止一个容器的运行
```bash
docker stop ef7773e32d67

ef7773e32d67
```

##### 启动一个容器运行
```bash
docker start ef7773e32d67

ef7773e32d67
```

##### 重新启动一个容器运行
```bash
docker restart ef7773e32d67

ef7773e32d67
```

##### 查看容器运行的日志
```bash
docker logs ef7773e32d67

hello world
```

##### 监控容器运行的状态
```bash
docker stats ef7773e32d67
```

##### 查看容器运行的细节
```bash
docker inspect ef7773e32d67
```
```json
[
{
    "Id": "ef7773e32d670b3ce47d5a7320ba0935e5d56b5a401a246eb46cd40194b1e945",
    "Created": "2016-04-26T10:00:39.744733621Z",
    "Path": "echo",
    "Args": [
        "hello world"
    ],
    "State": {
    	......
        "StartedAt": "2016-04-26T11:07:14.209156256Z",
        "FinishedAt": "2016-04-26T11:07:14.273612789Z"
    },
    "Image": "bc744c4ab376115cc45c610d53f529dd2d4249ae6b35e5d6e7a96e58863545aa",
    "HostnamePath": "/var/lib/docker/containers/ef7773e32d670b3ce47d5a7320ba0935e5d56b5a401a246eb46cd40194b1e945/hostname",
    "HostsPath": "/var/lib/docker/containers/ef7773e32d670b3ce47d5a7320ba0935e5d56b5a401a246eb46cd40194b1e945/hosts",
    "LogPath": "/var/lib/docker/containers/ef7773e32d670b3ce47d5a7320ba0935e5d56b5a401a246eb46cd40194b1e945/ef7773e32d670b3ce47d5a7320ba0935e5d56b5a401a246eb46cd40194b1e945-json.log",
    "Driver": "aufs",
    "HostConfig": {
	......
        "RestartPolicy": {
            "Name": "no",
            "MaximumRetryCount": 0
        },
        "ReadonlyRootfs": false,
        "LogConfig": {
            "Type": "json-file",
            "Config": {}
        }
    },
    "GraphDriver": {
        "Name": "aufs",
        "Data": null
    },
    "Mounts": [],
    "Config": {
        "Hostname": "ef7773e32d67",
        "Cmd": [
            "echo",
            "hello world"
        ],
        "Image": "busybox",
        "StopSignal": "SIGTERM"
	......
    },
    "NetworkSettings": {
        "Bridge": "",
        "SandboxID": "",
	......
        "Networks": {
            "bridge": {
                "EndpointID": "",
                "Gateway": "",
		......
            }
        }
    }
}
]
```

##### 向容器内拷贝文件
```bash
docker cp aaa ef7773e32d67:/tmp/
```

##### 从容器内拷贝文件
```bash
docker cp ef7773e32d67:/tmp/aaa bbb
```

##### 进入容器
```bash
docker exec -ti ef7773e32d67 /bin/sh
```

##### 删除指定容器
```bash
docker rm -f ef7773e32d67

ef7773e32d67
```

###### 将容器提交为镜像，即将此时的容器固化下来
```bash
docker commit -m "your message" ef7773e32d67 yeepay/dawei-ubuntu:14.04
```


##数据卷 
###在容器中创建一个数据卷
```
docker run -it --name web1 -v  /webapp ubuntu:14.04 /bin/bash
```
###挂载一个主机目录作为数据卷
####默认权限是读写
```
docker run -ti --name web2 -v /home/test:/home/test ubuntu:14.04 /bin/bash

docker inspect [容器id]
 "Mounts": [
        {
            "Source": "/home/test",
            "Destination": "/home/test",
            "Mode": "",
            "RW": true
        }
    ],
```  
 
####修改权限为只读
 
```
docker run -ti --name web3 -v /home/test1:/home/test1:ro ubuntu:14.04 /bin/bash

  "Mounts": [
        {
            "Source": "/home/test1",
            "Destination": "/home/test1",
            "Mode": "ro",
            "RW": false
        }
    ],
    
root@16fa0d40889c:/home/test1# echo '111111' >>text.txt 
bash: text.txt: Read-only file system
```    
###挂载一个本地主机文件作为数据卷
```
  docker run -it -v ~/.bash_history:/.bash_history  ubuntu:14.04 /bin/bash
```
###数据卷容器
####首先,创建一个命令的数据卷容器db_share
```
docker run -tid -v /share --name db_share ubuntu:14.04 /bin/bash
```
####然后,在其他容器中使用--volumes-from来挂载db_share容器中的数据卷
```
docker run -tid --volumes-from db_share --name db1 ubuntu:14.04
docker run -tid --volumes-from db_share --name db2 ubuntu:14.04
```
####还可以使用多个--volumes-from参数来从多个容器挂载多个数据卷。也可以从其他已经挂载了数据卷的容器来挂载数据卷
```
docker run -tid --name db3 --volumes-from db1 ubuntu:14.04
```

##网络
###介绍
```
host模式
container模式
none模式
bridge模式
```
###外部访问容器
####映射所有接口地址
```
docker run -tid --name website1 -v $PWD/website:/var/www/html/website -p 6667:80 xul487290/nginx nginx
```
####映射到指定地址的指定端口
```
docker run -tid --name website2 -v $PWD/website:/var/www/html/website -p 172.17.103.47:6667:80 xul487290/nginx nginx
```

####映射到指定的任意端口
```
docker run -tid --name website3 -v $PWD/website:/var/www/html/website -p 172.17.103.47::80 xul487290/nginx nginx
```
 
####查看端口映射
```
docker port website1 80
```



