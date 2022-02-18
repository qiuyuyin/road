首先假设你的Mac上已经存在docker环境：

直接使用brew安装minikube

```sh
brew install minikube
```

然后就可以直接对minikube进行运行了：

```sh
minikube start --image-repository='registry.cn-hangzhou.aliyuncs.com/google_containers'
```

使用minikube将服务进行公开：

```sh
minikube service nginx-service --url
```

Kubernetes命令：

```sh
kubectl get deployments
```

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           24m

``` sh
kubectl get pods
```

NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-fb5c67579-4sqkc   1/1     Running   0          24m
kubernetes-bootcamp-fb5c67579-d2d25   1/1     Running   0          24m
kubernetes-bootcamp-fb5c67579-pnd7r   1/1     Running   0          24m
kubernetes-bootcamp-fb5c67579-ptwfx   1/1     Running   0          24m

```sh
kubectl describe pods
```

模拟升级Pod镜像：

```
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
```

然后通过get pods查看所使用的Pods可以发现所使用的pods发生改变。

## CAP模型

CA模型：

在分布式系统中是不存在的，一般指的是单体应用

CP模型：

不要求高可用，就是强调高度一致性，每一次对数据进行操作，都需要等待所有的节点中保存的数据高度相同

AP模型：

减少一定的同步，将数据的查询性能进行提高，但是这样会存在一定的脏数据。

比如我们使用的Zookeeper、etcd。。对数据的读操作都是一致性的，但是在选举的过程中或者大半的机器都失效的时候是不可用的。

## BASE理论

BASE是基本可用、软状态、最终一致性，是对AC之间进行权衡的结果，降低对系统的要求

https://juejin.cn/post/6844903621499305997

## 2PC

两阶段提交是一种使分布式系统中所有节点在进行事务提交时的保持一致性的一种协议；因为在分布式系统中，节点通常都知道自己提交的状态，但是无法了解到别人提交的状态，所以在分布式系统中需要一个协调者来统一掌控全部的节点来指示别的节点来提交事务

运行应用：

kubectl run kubia --image=luksa/kubia --port=8081

暴露接口：

kubectl expose rc kubia --type=LoadBalancer --name kubia-http
