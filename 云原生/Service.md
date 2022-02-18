## Kubernetes-Service

由于Pod的IP不是固定的，所以当外部Pod或者外部环境想要和Pod进行交流的时候是无法通过IP加上端口号和Pod进行通信的，所以这个时候就需要通过一个中间商来连接某种类型的Pod和外界的联系，就需要使用Service来大显身手了。

当然将服务映射到外部存在多种方式：

### 1.NodePort

使用NodePort就是在Kubernetes的所有节点暴露一个端口，然后将传入进来的服务访问对应的NodePort服务。

但是通过NodePort来暴露端口存在一个缺点，就是通过节点进行访问一般都是直接到达一个节点中的Pod中的容器中，所以没有实现负载均衡的问题。

### 2.LoadBalancer

为了解决负载均衡的问题，一些云厂商提供了在Kubernetes中解决负载均衡的实现机制，就是通过这个LoadBalancer来对服务进行访问。

LoadBalancer类型的服务是 个具有额外的基础设施提供的负载平衡器 NodePort服务。

>   外部服务的一些特性：
>
>   防止不必要的跳数：比如使用负载均衡来进行展示节点Service时，访问的Pod并不属于这个节点，这个时候可以对Service进行设置externalTrafficPolicy为Local，这样就会仅对这个节点中的Pod进行访问。
>
>   但是这样也会存在一个缺点就是负载不均衡的问题，所以最好在每一个节点中设置相同的Pod数目来解决部分节点压力过大的问题。

### 3.通过Ingress暴露

这个Ingress就是一种API网关的实现，在Minikube中所使用的就是Ningx来作为API网关进行不同服务的通信。

使用Ingress可以通过不同的域名和端口来转发到不同的Service，使用过Nginx的转发功能的同学都知道其实这个Ingress就是提供一个转发功能。

![image-20220215003052123](https://yili979.oss-cn-beijing.aliyuncs.com/img/image-20220215003052123.png)

同时不同的云服务厂商以及不同的公司都会对这个形式进行一定的处理，来解决微服务的部署问题。