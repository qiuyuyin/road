如果需要从docker的仓库中pull一个image进行使用：

使用 docker pull 命令即可，比如pull一个redis来进行使用：

```sh
root@iZ2zegk0d8ne76yp0ctjxlZ:~# docker pull redis
Using default tag: latest
latest: Pulling from library/redis
bd897bb914af: Pull complete 
24daa36e9c8d: Pull complete 
fae61c888a3a: Pull complete 
e498372c1890: Pull complete 
61691b3c8cfe: Pull complete 
ffd564714c18: Pull complete 
Digest: sha256:0c0484b1d1ff36faace984fe9d8e0fe58892ecc34a4859b97171045b9cd343e1
Status: Downloaded newer image for redis:latest
docker.io/library/redis:latest
```



 接下来就是十分常用的run命令了，使用run命令可以将你下载下来的mysql(根据推荐，最好不要将mysql安装到docker中，回存在性能瓶颈)但是这里只是进行测试，所以将mysql安装在docker中。

```sh
-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
-d: 后台运行容器，并返回容器ID；
-i: 以交互模式运行容器，通常与 -t 同时使用；
-P: 随机端口映射，容器内部端口随机映射到主机的高端口
-p: 指定端口映射，格式为：主机(宿主)端口:容器端口
-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
--name="nginx-lb": 为容器指定一个名称；
--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；
--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；
-h "mars": 指定容器的hostname；
-e username="ritchie": 设置环境变量；
--env-file=[]: 从指定文件读入环境变量；
--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；
-m :设置容器使用内存最大值；
--net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
--link=[]: 添加链接到另一个容器；
--expose=[]: 开放一个端口或一组端口；
```

一般比较常用的有--name、-p、-d、-it，记住这些就可以了。

docker安装mysql步骤：

```sh
docker pull mysql
```

然后使用run命令在后台运行：（最好自己写一遍加深印象）

```sh
$ docker run -d --name mysql-a -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

使用docker ps -a即可查看mysql是否启动，使用navcat可以测试出这个mysql是否启动成功：

```sh
$ docker ps -a
5bb94dcc5b9e   mysql     "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   0.0.0.0:3306->3306/tcp, 33060/tcp   mysql-a
```

如果你不想使用这个mysql了，使用以下命令进行关闭：

```sh
$ docker stop mysql-a
mysql-a
```

如果你想删除这个mysql，使用

```sh
$ docker rm -f mysql-a 
mysql-a
```

这个容器就已经删除了。

docker入门参考文章：https://www.docker.org.cn/docker/docker-206.html