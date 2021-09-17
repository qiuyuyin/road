# NIO

## 1.多路复用模型

我们试想一下这样的现实场景:

一个餐厅同时有100位客人到店，当然到店后第一件要做的事情就是点菜。但是问题来了，餐厅老板为了节约人力成本目前只有一位大堂服务员拿着唯一的一本菜单等待客人进行服务。

- 那么最笨(但是最简单)的方法是(方法A)，无论有多少客人等待点餐，服务员都把仅有的一份菜单递给其中一位客人，然后站在客人身旁等待这个客人完成点菜过程。在记录客人点菜内容后，把点菜记录交给后堂厨师。然后是第二位客人。。。。然后是第三位客人。很明显，只有脑袋被门夹过的老板，才会这样设置服务流程。因为随后的80位客人，再等待超时后就会离店(还会给差评)。
- 于是还有一种办法(方法B)，老板马上新雇佣99名服务员，同时印制99本新的菜单。每一名服务员手持一本菜单负责一位客人(关键不只在于服务员，还在于菜单。因为没有菜单客人也无法点菜)。在客人点完菜后，记录点菜内容交给后堂厨师(当然为了更高效，后堂厨师最好也有100名)。这样每一位客人享受的就是VIP服务咯，当然客人不会走，但是人力成本可是一个大头哦(亏死你)。
- 另外一种办法(方法C)，就是改进点菜的方式，当客人到店后，自己申请一本菜单。想好自己要点的才后，就呼叫服务员。服务员站在自己身边后记录客人的菜单内容。将菜单递给厨师的过程也要进行改进，并不是每一份菜单记录好以后，都要交给后堂厨师。服务员可以记录号多份菜单后，同时交给厨师就行了。那么这种方式，对于老板来说人力成本是最低的；对于客人来说，虽然不再享受VIP服务并且要进行一定的等待，但是这些都是可接受的；对于服务员来说，基本上她的时间都没有浪费，基本上被老板压杆了最后一滴油水。

如果您是老板，您会采用哪种方式呢?

到店情况: 并发量。到店情况不理想时，一个服务员一本菜单，当然是足够了。所以不同的老板在不同的场合下，将会灵活选择服务员和菜单的配置。

- 客人: 客户端请求
- 点餐内容: 客户端发送的实际数据
- 老板: 操作系统
- 人力成本: 系统资源
- 菜单: 文件状态描述符。操作系统对于一个进程能够同时持有的文件状态描述符的个数是有限制的，在linux系统中$ulimit -n查看这个限制值，当然也是可以(并且应该)进行内核参数调整的。
- 服务员: 操作系统内核用于IO操作的线程(内核线程)
- 厨师: 应用程序线程(当然厨房就是应用程序进程咯)
- 餐单传递方式: 包括了阻塞式和非阻塞式两种。
  - 方法A: 阻塞式/非阻塞式 同步IO
  - 方法B: 使用线程进行处理的 阻塞式/非阻塞式 同步IO
  - 方法C: 阻塞式/非阻塞式 多路复用IO，也就是要使用的NIO操作

## 2.三个重要概念：

### channel 管道

1. ServerSocketChannel: 应用服务器程序的监听通道。只有通过这个通道，应用程序才能向操作系统注册支持“多路复用IO”的端口监听。同时支持UDP协议和TCP协议。

2. ScoketChannel: TCP Socket套接字的监听通道，一个Socket套接字对应了一个客户端IP: 端口 到 服务器IP: 端口的通信连接。

3. DatagramChannel: UDP 数据报文的监听通道。

### Buffer缓冲区

存在下列的缓冲区，除了Long类型不存在缓冲区，其他的类型都存在缓冲区。

- ByteBuffer
- CharBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer

缓冲区的父类Buffer中有几个核心属性，如下

```
// Invariants: mark <= position <= limit <= capacity
private int mark = -1;
private int position = 0;
private int limit;
private int capacity;Copy
```

- capacity：缓冲区的容量。通过构造函数赋予，一旦设置，无法更改
- limit：缓冲区的界限。位于limit 后的数据不可读写。缓冲区的限制不能为负，并且**不能大于其容量**
- position：**下一个**读写位置的索引（类似PC）。缓冲区的位置不能为负，并且**不能大于limit**
- mark：记录当前position的值。**position被改变后，可以通过调用reset() 方法恢复到mark的位置。**

以上四个属性必须满足以下要求

**mark <= position <= limit <= capacity**

#### 核心方法

##### put()方法

- put()方法可以将一个数据放入到缓冲区中。
- 进行该操作后，postition的值会+1，指向下一个可以放入的位置。capacity = limit ，为缓冲区容量的值。

[![img](NIO.assets/20201109145709.png)](https://nyimapicture.oss-cn-beijing.aliyuncs.com/img/20201109145709.png)

##### flip()方法

- flip()方法会**切换对缓冲区的操作模式**，由写->读 / 读->写
- 进行该操作后
  - 如果是写模式->读模式，position = 0 ， limit 指向最后一个元素的下一个位置，capacity不变
  - 如果是读->写，则恢复为put()方法中的值

[![img](NIO.assets/20201109145753.png)](https://nyimapicture.oss-cn-beijing.aliyuncs.com/img/20201109145753.png)

##### get()方法

- get()方法会读取缓冲区中的一个值
- 进行该操作后，position会+1，如果超过了limit则会抛出异常

[![img](NIO.assets/20201109145822.png)](https://nyimapicture.oss-cn-beijing.aliyuncs.com/img/20201109145822.png)

##### rewind()方法

- 该方法**只能在读模式下使用**
- rewind()方法后，会恢复position、limit和capacity的值，变为进行get()前的值

[![img](NIO.assets/20201109145852.png)](https://nyimapicture.oss-cn-beijing.aliyuncs.com/img/20201109145852.png)

##### clean()方法

- clean()方法会将缓冲区中的各个属性恢复为最初的状态，position = 0, capacity = limit
- **此时缓冲区的数据依然存在**，处于“被遗忘”状态，下次进行写操作时会覆盖这些数据

[![img](NIO.assets/20201109145905.png)](https://nyimapicture.oss-cn-beijing.aliyuncs.com/img/20201109145905.png)

##### mark()和reset()方法

- mark()方法会将postion的值保存到mark属性中
- reset()方法会将position的值改为mark中保存的值

### selector 选择器

**注册：**

程序需要向Selector对象注册需要它来进行关注的Channel，在实现select之后就需要在selector中注册一个Accept对象用于接受其他Socket发送来的请求。

```java
serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
```

**代理查询：**

应用层不再需要在阻塞模式下对操作系统进行查询事件有没有发生，而是使用Selector代为进行查询

![img](NIO.assets/java-io-nio-5.png)

可以看见不同的操作系统Selector的实现方式是不一样的，不同的操作系统使用不同的Selector的实现类。在Netty中也是通过这个来进行设计的。

## 程序实现

服务端实现：

```java
public class NIOServer {
    public static void main(String[] args) throws Exception{
        //打开一个selector进行操作
        Selector selector = Selector.open();

        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        serverSocketChannel.configureBlocking(false);
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        //-----------------以上基本固定不变------------------------
        ServerSocket serverSocket = serverSocketChannel.socket();
        //通过管道打开一个套接字（服务端套接字），并进行绑定。
        InetSocketAddress inetSocketAddress = new InetSocketAddress("127.0.0.1", 6666);
        serverSocket.bind(inetSocketAddress);

        //开始使用selector进行工作
        while (true){
            if(selector.select(2000)==0){
                System.out.println("连接数为0，继续等待连接");
                continue;
            }
            //这个生成一系列的keys节点
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            Iterator<SelectionKey> iterator = selectionKeys.iterator();

            while(iterator.hasNext()){
                SelectionKey next = iterator.next();
                //如果链接是可达的，服务器为这个链接分配一个channel
                SocketChannel socketChannel = null;
                if(next.isAcceptable()){
                    socketChannel = serverSocketChannel.accept();
                    socketChannel.configureBlocking(false);
                    socketChannel.register(selector,SelectionKey.OP_READ,ByteBuffer.allocate(1024));
                    System.out.println(System.nanoTime());

                }
                //需要等待客户端将数据加入到管道中
                if(next.isReadable()){
                    socketChannel = (SocketChannel) next.channel();
                    ByteBuffer byteBuffer = (ByteBuffer)next.attachment();
                    socketChannel.read(byteBuffer);

                    System.out.println(readDataFromSocketChannel(byteBuffer));
                    socketChannel.close();
                }

                iterator.remove();
            }
        }

    }
    private static String readDataFromSocketChannel(ByteBuffer byteBuffer) throws IOException {
		//使用的是Java编程思想所提供的方式来进行ByteBuffer的转化，在Netty中提供了更多的转化方式
        byteBuffer.rewind();
        String encoding = System.getProperty("file.encoding");
        return Charset.forName(encoding).decode(byteBuffer)+"";
    }
}
```

### 客户端存在两种实现的方式：

一个是不通过Channel（阻塞）来传递信息的方法：

```java
public static void main(String[] args) throws Exception{
    //客户端不必使用NIO的方式来进行传递信息。
    //开始进行连接
    Socket socket = new Socket("127.0.0.1", 6666);
    OutputStream outputStream = socket.getOutputStream();
    outputStream.write("伊离".getBytes());
    outputStream.close();
}
```

另一个是使用非阻塞来进行实现的：

```java
public class NIOClient {
    public static void main(String[] args) throws Exception{
        SocketChannel socketChannel = SocketChannel.open();
        socketChannel.configureBlocking(false);
        InetSocketAddress inetSocketAddress = new InetSocketAddress("127.0.0.1", 6666);
        System.out.println(System.currentTimeMillis());
        if(!socketChannel.connect(inetSocketAddress)){
            while (!socketChannel.finishConnect()){
                System.out.println("这个时候可以在做别的事情，并没有阻塞");
            }
        }
        ByteBuffer wrap = ByteBuffer.wrap("伊离".getBytes(StandardCharsets.UTF_8));
        socketChannel.write(wrap);//向这个管道中写数据
        socketChannel.close();
    }
}
```

