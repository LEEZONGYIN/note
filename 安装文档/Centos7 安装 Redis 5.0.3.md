# Centos7 安装 Redis 5.0.3

## 1.进入目录

cd /usr/local

## 2.下载5.0.3

wget http://download.redis.io/releases/redis-5.0.3.tar.gz

## 3.解压到当前目录

```
tar -xvzf redis-5.0.3.tar.gz
```



## 4.删除压缩包

`rm redis-5.0.3.tar.gz`

## 5.进入解压出来的目录

`cd redis-5.0.3`

## 6.编译源码

`make`

## 7.修改配置文件

`vi redis.conf`

## 8.修改以下配置

### a.远程访问需要把bind注释掉

  bind 127.0.0.1 修改为 # bind 127.0.0.1

### b.默认启动时为后台启动

  daemonize yes

### c.设置密码，找到

requirepass foobared 字符

 修改为
 requirepass password

## 9.进入 src 文件夹以配置文件的形式启动项目

`cd src`
`./redis-server ../redis.conf`

## 10.查看是否启动成功，如果有 6379 端口的服务代表启动成功

`netstat -nlpt`

### 11.打开客户端

`./redis-cli`

进入客户端交互模式后输入

`auth password`

## 12.测试是否可用

`set test test`
get test

## 13.设置redis 开机自启动

### a.在/etc目录下新建redis目录

```
cd /etc
mkdir redis
```

### b.将/usr/local/redis-5.0.3/redis.conf 文件复制一份到/etc/redis目录下，并命名为6379.conf

`cp /usr/local/redis-5.0.3/redis.conf /etc/redis/6379.conf`

### c.将redis的启动脚本复制一份放到/etc/init.d目录下

`cp /usr/local/redis-5.0.3/utils/redis_init_script /etc/init.d/redisd`

### d.切换到/etc/init.d目录下 然后执行自启命令

```
cd /etc/init.d/
chkconfig redisd on
```

看结果是否支持chkconfig 如果报service redisd does not support chkconfig 错误 

则修改redisd 文件
vi redisd 在第一行加入如下两行注释，保存退出

```
chkconfig:   2345 90 10

description:  Redis is a persistent key-value database
```



### e.启动 关闭 redis

```
service redisd start
service redisd stop
```




如果stop失败 报

```
(error) NOAUTH Authentication required.
     Waiting for Redis to shutdown ...
     Waiting for Redis to shutdown ...
     Waiting for Redis to shutdown ...
```




修改启动脚本 关闭时的密码

```
vi redisd
将 $CLIEXEC -p $REDISPORT shutdown 修改为
$CLIEXEC -a "password" -p $REDISPORT shutdown
```




如果stop失败 报

`/usr/local/bin/redis-cli: No such file or directory`

需要修改启动脚本 资源包路径

```
将
EXEC=/usr/local/bin/redis-server
CLIEXEC=/usr/local/bin/redis-cli
修改为
EXEC=/usr/local/redis-5.0.3/src/redis-server
CLIEXEC=/usr/local/redis-5.0.3/src/redis-cli
```

## 14.在阿里云部署

完成以上步骤后，

可通过Redis Desktop Manager工具测试：

![1571662108794](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571662108794.png)

若连接不成功

a、检测配置文件

将protected-mode yes 改为 protected-mode no

b、检测密码

auth password 是否成功

c、修改完配置文件后

进到Redis 的 src文件，按下面命令开启，这样配置文件才会生效

```
./redis-server ../redis.conf
```

d、阿里云对应的端口应该打开