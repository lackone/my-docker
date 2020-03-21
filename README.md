# my-docker
基于docker-compose编排的一套LNMP环境

## 用途描述
主要用于PHP开发和测试下的环境快速搭建，注意，注意，注意，切勿用于生产环境。

因为在构建php时，在Dockerfile中进行判断安装扩展操作，这会导致层过多，体积过大，建议只用于开发和测试。

## 参考链接
* [https://github.com/laradock/laradock](https://github.com/laradock/laradock)，laradock
* [https://github.com/piaohan/my-docker](https://github.com/piaohan/my-docker)，piaohan
* [https://github.com/yeszao/dnmp](https://github.com/yeszao/dnmp)，yeszao
* [https://github.com/ydtg1993/server](https://github.com/ydtg1993/server)，ydtg1993
* [https://github.com/guanguans/dnmp-plus](https://github.com/guanguans/dnmp-plus)，guanguans
* [https://github.com/khs1994-docker/lnmp](https://github.com/khs1994-docker/lnmp)，khs1994-docker
* [https://github.com/voocel/docker-lnmp](https://github.com/voocel/docker-lnmp)，voocel
* [https://github.com/duiying/Docker-LNMP](https://github.com/duiying/Docker-LNMP)，duiying
* [https://github.com/YanlongMa/docker-lnmp](https://github.com/YanlongMa/docker-lnmp)，YanlongMa

参考于上面多个项目，感谢，感谢，感谢，他们的开源项目。

## 使用方法

### 一、安装docker

1、先卸载老版本的docker
```
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine
```
2、安装必需的软件包
```
yum install -y yum-utils device-mapper-persistent-data lvm2
```
3、设置yum源(使用国内源)
```
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
4、查看docker版本
```
yum list docker-ce --showduplicates | sort -r
```
5、安装docker
```
yum install -y docker-ce-18.03.1.ce
```
6、加入开机自启动
```
systemctl enable docker
```
7、启动docker
```
systemctl start docker
```
8、配置国内镜像
```
vi /etc/docker/daemon.json
```
添加如下内容：
```
{
    "registry-mirrors": [
        "http://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://registry.docker-cn.com"
    ]
}
```
然后重启docker
```
systemctl restart docker
```

### 二、安装Docker Compose编排工具

1、二进制安装
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
```
sudo chmod +x /usr/local/bin/docker-compose
```

2、安装Compose命令补全⼯具
```
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.25.3/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```

### 三、clone项目并快速使用

```
yum install -y git
git clone https://github.com/lackone/my-docker.git
cd my-docker
```
然后运行docker-compose来构建服务，默认会装上php7.4，mysql8，nginx，redis这四个服务。
```
docker-compose up -d
```
可以使用的服务有nginx，mysql8，mysql5，php74，php73，php72，php56，redis，memcached，mongodb，mongo-express，phpmyadmin，phpredisadmin，adminmongo，rabbitmq等服务。

如果想要添加其他服务，可在docker-compose-full.yml文件中选择对应项复制到docker-compose.yml文件中。

运行docker-compose构建即可。
```
docker-compose up -d 服务名
```

### 四、目录结构

```
my-docker
    |----logs                       日志文件
    |----services                   服务构建和配置文件
            |----mongodb            mongodb配置目录
            |----mysql              mysql配置目录
            |----nginx              nginx配置目录
            |----php                php配置目录
            |----redis              redis配置目录
    |----wwwroot                    网站根目录
    |----.env                       环境配置文件
    |----docker-compose.yml         服务配置文件
    |----docker-compose-full.yml    服务配置文件(全)
    |----README.md
```

### 五、常用命令

1、构建命令
```
创建服务，并在后台运行容器
docker-compose up -d
指定创建服务，并在后台运行容器
docker-compose up -d 服务名1 服务名2 服务名3
启动服务
docker-compose start 服务名
停止服务
docker-compose stop 服务名
重启服务
docker-compose restart 服务名
重新构建服务
docker-compose build 服务名
删除服务
docker-compose rm 服务名
停止并删除容器
docker-compose down
```

2、进入容器内部
```
docker exec -it 服务名 /bin/sh
```

### 六、注意事项

1、修改配置文件

如果你修改了.env或Dockerfile文件，需要重新构建服务，生成镜像。
```
docker-compose build 服务名
```
注意这时候，你只是重新构建了镜像，但是已经运行的实例是不会变的，你需要先stop它，然后rm它。再重新up它。
```
docker-compose stop 服务名
docker-compose rm 服务名
docker-compose up -d 服务名
```

2、修改容器名

如果你修改了容器名container_name，或者你使用其他版本的php或mysql，注意连接时要使用对应的容器名。

比如nginx目录下conf.d/default.conf中的
```
fastcgi_pass   php:9000;
```
这里的php就需要改成对应容器名称，比如：php73，php72，php56。

还有在php连接mysql时，这里的host要写成对应的容器名称，比如：mysql5，mysql8。不然会无法连上。
```
$dbh = new PDO('mysql:host=mysql;dbname=mysql', 'root', '123456');
```