## Kubernetes-卷

所有的应用程序不免都需要和存储打交道，不管是Nginx应用访问所需要提供的Html资源还是后台程序需要打印日志到底层的磁盘环境中，还是数据库应用将数据持久化到磁盘中。但是我们的容器是运行在不同的Pod中，而且Pod的生命周期是会随时进行改变的，这个时候就需要将不同的容器以及不同的Pod挂载到同一个存储空间中，这样才会使得功能正常的运行。

>   卷的定义：
>
>   卷是Pod的一个组成部分，并不是一个独立的对象，同时也不可以单独进行创建和删除。Pod中所有容器都可以使用卷，但是必须要卷挂载到每个需要单独使用的机器上。在每个容器中都可以在其文件系统中的任意位置挂载卷。
>
>   因此如果重新启动容器的时候，卷中的内容不会发生改变，因为卷和容器是在同一个级别上，同时多个容器可以同时连接同一个卷来共享卷中的空间。由于卷位于Pod中，当Pod被删除的时候，卷的生命周期也同时结束，不会在下一次创建Pod的时候保留之前的存储化内容。

### 使用的卷类型：

-   emptyDir—用于存储临时的数据，不会进行本地化
-   hostPath—将目录从工作节点的文件系统挂载到Pod中
-   gitRepo—通过获取git中的目录来加入到卷中，一般是只读类型的
-   nfs—挂载到Pod中的NFS共享卷
-   各个云厂商的文件系统都是支持的
-   persistentVolumeClaim—一种使用预置或者动态配置的持久化存储类型

### 1.emptyDir

![image-20220215211052935](https://yili979.oss-cn-beijing.aliyuncs.com/img/image-20220215211052935.png)

不同的容器挂载在相同的卷上，所以当一个容器生成html代码之后另一个Ningx容器就会读取html代码，这样就可以对容器进行访问了。

由于不同的介质存在速度差异，所以在kubernetes中可以使用SSD和Memory来对卷中的信息进行存储。

### 2.gitRepo

使用Git仓库来作为存储卷，通过Pod在启动的时候克隆Git仓库中的文件来构建一个存储卷。但是当Git仓库中的信息放生更新的时候，卷中的数据却不会发生更新，只有当Pod重新创建过的时候卷中的内容才会发生更新。

![image-20220215212957992](https://yili979.oss-cn-beijing.aliyuncs.com/img/image-20220215212957992.png)

其实gitRepo便是一个官方实现的类Sidecar，如果用户存在更多的关于对git仓库的需求，可以自己实现一个sidecar，比如来同步git仓库的更新同时支持ssh来连接是由库来保证私有应用的部署。

### 3.hostPath

hostPath是连接到当前节点的文件系统（一般节点都是Linux云主机，所以使用的Linux的文件格式。有些时候我们不得不了解到当前文件系统的生产状态，比如获取当前节点的日志文件或者CA证书。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # 宿主上目录位置
      path: /data
      # 此字段为可选
      type: Directory
```

具体可以参考 [HostPath](https://kubernetes.io/zh/docs/concepts/storage/volumes/#hostpath) 的实现方式

### 4.持久化存储

想要了解持久化存储，可以先对比一下大数据方向的HDFS，就是在一个集群中的文件系统，在不同节点上的Pod都可以保存并使用相同的存储空间，其生命周期不同步与Pod，也就是说当一个Pod是刚刚启动也可以对持久化存储中的数据进行访问。不同于HDFS将数据共享到所有的节点中，持久化存储其实是位于NAS系统或者各个云厂商的云服务中。

一般使用持久化存储来部署文件类型的数据库，通过将数据库中的值和外部的持久化存储进行挂钩，可以在数据库挂掉之后仍然不会失去数据中的数据文件，同时可以部署集群来增加数据库的承载量（虽然对数据库集群一般不使用这种方式来进行部署）

下图是持久卷和命名空间和Pod之间的关系：

![image-20220215223100177](https://yili979.oss-cn-beijing.aliyuncs.com/img/image-20220215223100177.png)

用户可以直接使用Pod来连接持久卷同时也可以通过持久卷声明来连接持久卷，支持直接连接持久卷的持久化服务有：

-   AWSElasticBlockStore 亚马逊服务
-   AzureDisk 微软云服务
-   CSI
-   FC （光纤通道）
-   GCEPersistentDisk 谷歌云服务
-   iSCSI 
-   Local 卷
-   OpenStack Cinder
-   RBD （Ceph 块设备）
-   VsphereVolume 虚拟化云服务

### 总结：

卷就是Kubernetes对文件系统的一种实现，我们可以使用emotyDir这种资源，也可以使用gitRepo这种通过git仓库进行获取，甚至可以直接使用持久化存储来构建应用

