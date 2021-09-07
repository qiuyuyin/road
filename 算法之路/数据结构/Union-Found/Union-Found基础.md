## Union-Found基础

<img src="Union-Found%E5%9F%BA%E7%A1%80.assets/image-20210906113935292.png" alt="image-20210906113935292" style="zoom:67%;" />

这是一个十分巧妙的算法，可以将关联的两个数字划分为一个区域内。

而unioin() 方法可以两个分量进行归并，find()方法可返回给定触点所在的标识符，通过比较两个分量的标识符即可判断这两个分量是否被连接。

下图的所有联通分量为3:

<img src="Union-Found%E5%9F%BA%E7%A1%80.assets/image-20210906144537636.png" alt="image-20210906144537636" style="zoom:67%;" />

**所以整理所需要的方法：**

- **union()**
- **find()**
- **count()**
- **connected()**

**有了最初的UnionFound类：**

```java
//Quick-find
public class UnionFound {
    private int[] ids;
    private int count;
    //构造函数
    public UnionFound(int count){
        this.count = count;
        ids = new int[count];
        for (int i = 0; i < count; i++) {
            ids[i] = i;
        }
    }
    //判断两个数字是否已经连接
    public boolean connected(int p,int q){
        return find(p) == find(q);
    }
    //得到count的总数
    public int getCount() {
        return count;
    }
    //查找一个数的连接方式
    public int find(int index){
        return ids[index];
    }
    //将两个数字进行连接
    public void union(int p,int q){
        int pID = find(p);
        int qID = find(q);

        if(pID == qID) return;

        for (int i = 0; i < ids.length; i++) {
            if(ids[i] == pID) ids[i] = qID;
        }
        count--;
    }
}
```

上述实现是基于QuickFound来进行实现的，也就是说能够快速访问但是不能实现常数级别的union函数，所以还需要对find以及union函数进行修改：

-------

开始考虑引入新的结构：**树形结构**。

<img src="Union-Found%E5%9F%BA%E7%A1%80.assets/image-20210906145451836.png" alt="image-20210906145451836" style="zoom:67%;" />

那么问题来了，如何构成一个树形的算法？

《算法4》书籍中给了一个很巧妙的算法，仅改变find函数便将这个数组的结构改变成为了树形结构。

```java
//查找一个数的连接方式
public int find(int index){
    while (ids[index] != index) index = ids[index];
    return index;
}
//将两个数字进行连接
public void union(int p,int q){
    //直接寻找到这俩的根结点
    int pRoot = find(p);
    int qRoot = find(q);
    if (pRoot == qRoot) return;
    ids[pRoot] = qRoot;

    count--;
}
```

但是即使是树形结构也可能会出现极端的情况，就是树直接化作了一个链表，在进行寻找的时候会花费较多的时间。所以如何才能将时间复杂度压缩为常数级别？

很简单，只需要对find函数进行改变即可。

```java
//进行状态压缩，保证这个树的层数最多只有3，保证find的时间复杂度为O（1）
while (ids[index] != index){
    ids[index] = ids[ids[index]];
    index = ids[index];
}
return index;
```

根据修改可以将树的层数转为3层以下。

### 加权

虽然层数已经转为3层以下，但是还是可以将效率进一步提高，通过添加一个加权值，可以在union的时候对数据进行整理，就类似于平衡二叉树。在加入的时候对数据进行平衡调整。

```java
public void union(int p,int q){
    int pID = find(p);
    int qID = find(q);

    if(pID == qID) return;

    if(sz[pID] < sz[qID]){
        ids[pID] = qID;
        sz[qID] += sz[pID];
    }else {
        ids[qID] = pID;
        sz[pID] += sz[qID];
    }
    count--;
}
```

