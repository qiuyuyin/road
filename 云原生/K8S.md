## Kubernetes

首先理解Kubernetes的设计，可以将Kubernetes理解为一个操作系统（云原生时代的操作系统）然后在Kubernetes中进行运行的容器可以看作一个进程（线程）。

Pod：是分配资源的一个单位，会将多个容器组成一个Pod，然后放在机器上进行运行。是一个原子调度系统，超亲密关系，使用Pod来进行解决。

-   直接的文件交换
-   unix socket本地通信
-   rpc调用
-   会共享一些资源

Pod的实现机制：

多个容器之间共享资源和数据？

新建一个Infra 容器来对数据进行共享，里面的容器所看见的网络地址都是一样的，仅有一个IP地址。所有的容器共享目录，宿主机上的目录会同时进行挂载。

容器设计模式：Sidecar

-   SSH执行脚本
-   日志收集
-   Debug应用
-   应用监控

代理容器

适配器容器

自动恢复

水平伸缩（将需要更多资源的数据进行扩容）



Master

-   API Server
-   Controller 恢复和自动扩张
-   Scheduler 自动调度，将不同的容器进行调用
-   Etcd保存一些状态信息

Node

-   Pod
-   Kubelet

![image-20220205191702599](https://yili979.oss-cn-beijing.aliyuncs.com/img/image-20220205191702599.png)

Pod

-   containter
-   volume