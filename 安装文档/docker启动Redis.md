# docker启动Redis

## 1.查看当前是否在运行

docker ps

![1571643468230](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571643468230.png)

说明启动成功

## 2.启动

由于项目开发时启动的Redis容器都是后台型的，无法直接实践Redis命令。因此需要另外启动一个链接了Redis服务的Docker容器。

```
# 启动链接到redis上的服务器(it:交互型容器 link:链接两个容器)
docker run --rm -it --link redis-master:redis redis /bin/bash

# 用redis-cli工具访问redis(h:主机 p:端口)，这里的端口不指定，它也会默认去访问主机的6379端口
redis-cli -h redis -p 6379
```

如下图所示，启动了交互型容器，就可以直接在命令行窗口实践redis命令了：

![1571643655200](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571643655200.png)