# MongoDB的CURD

## :satellite:  本章较长，请耐心看完

## 1.Insert Documents

下面是插入单条数据，选择性的插入一个文档即可。

![MongoDB insertOne 操作的组件。](MongoDB的CURD.assets/crud-annotated-mongodb-insertOne.bakedsvg.svg)

```js
> db.yili.insertOne( { name:"sue", age:26, status:"pending" } )
{
        "acknowledged" : true,
        "insertedId" : ObjectId("61134b74b4249744caec54fd")
}
```

使用insertMany插入多行内容：

```js
db.collection.insertMany(
   [ <document 1> , <document 2>, ... ],
   {
      writeConcern: <document>,
      ordered: <boolean>
   }
)
```

下面是插入语句，在之后的查询语句中还会用到：

```js
db.inventory.insertMany([
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);
```

## 2.Query Documents

**和sql中语句类似，mongoDB在查询数据的时候同时也会带有选择性的条件**

```sql
SELECT * FROM inventory WHERE status = "D"
```

------

**不过形式有所不同，格式如下：**

```js
db.collection.find(query, projection)
```



### MongoDB 与 RDBMS Where 语句比较

如果你熟悉常规的 SQL 数据，通过下表可以更好的理解 MongoDB 的条件语句查询：

| 操作       | 格式                     | 范例                                        | RDBMS中的类似语句       |
| :--------- | :----------------------- | :------------------------------------------ | :---------------------- |
| 等于       | `{<key>:<value>`}        | `db.col.find({"by":"菜鸟教程"}).pretty()`   | `where by = '菜鸟教程'` |
| 小于       | `{<key>:{$lt:<value>}}`  | `db.col.find({"likes":{$lt:50}}).pretty()`  | `where likes < 50`      |
| 小于或等于 | `{<key>:{$lte:<value>}}` | `db.col.find({"likes":{$lte:50}}).pretty()` | `where likes <= 50`     |
| 大于       | `{<key>:{$gt:<value>}}`  | `db.col.find({"likes":{$gt:50}}).pretty()`  | `where likes > 50`      |
| 大于或等于 | `{<key>:{$gte:<value>}}` | `db.col.find({"likes":{$gte:50}}).pretty()` | `where likes >= 50`     |
| 不等于     | `{<key>:{$ne:<value>}}`  | `db.col.find({"likes":{$ne:50}}).pretty()`  | `where likes != 50`     |



**下面是对小于符号的测试：**

```js
> db.inventory.find( { qty : {$lt:40} } ).pretty()
{
        "_id" : ObjectId("61134f0bb4249744caec5501"),
        "item" : "journal",
        "qty" : 25,
        "size" : {
                "h" : 14,
                "w" : 21,
                "uom" : "cm"
        },
        "status" : "A"
}
```

### MongoDB中使用AND操作符

在mongoDB中使用and十分简单

就是使用`,`来将两个表达式分割开来，即代表使用的是and操作符。

```js
db.inventory.find( { status: "A", qty: { $lt: 30 } } )
```

**等效于sql中的语句：**

```sql
SELECT * FROM inventory WHERE status = "A" OR qty < 30
```

### MongoDB中使用OR操作符

即在最开始的位置添加一个$or符号，然后加入一个数组的符号[]，即代表后面的逗号代表的是OR符号

```js
db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
```

**等效于sql中的语句：**

```sql
SELECT * FROM inventory WHERE status = "A" OR qty < 30
```

将AND操作符和OR操作符联合进行使用：

```js
db.inventory.find( {
     status: "A",
     $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )
```



### 对数组进行查询

给予一个集合中，注意到tags和dim_cm中所包含的内容为数组的形式，那么如何对数组的形式来进行查询呢？

```
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], dim_cm: [ 14, 21 ] },
   { item: "notebook", qty: 50, tags: ["red", "blank"], dim_cm: [ 14, 21 ] },
   { item: "paper", qty: 100, tags: ["red", "blank", "plain"], dim_cm: [ 14, 21 ] },
   { item: "planner", qty: 75, tags: ["blank", "red"], dim_cm: [ 22.85, 30 ] },
   { item: "postcard", qty: 45, tags: ["blue"], dim_cm: [ 10, 15.25 ] }
]);
```

- **限定查询**

如果你使用下面的方式来进行查询，得到的是固定顺序且仅包含所查询元素的方式：

```js
db.inventory.find( { tags: ["red", "blank"] } )
{
        "_id" : ObjectId("611369afb4249744caec5507"),
        "item" : "notebook",
        "qty" : 50,
        "tags" : [
                "red",
                "blank"
        ],
        "dim_cm" : [
                14,
                21
        ]
}
```

- **全查询**

如果想要查询包含所示的元素，则需要使用`$all`操作符来进行查询：

查询到的结果是只包含这种的结果

```js
 db.inventory.find( { tags: { $all: ["red", "blank"] } },{ tags:1 } ).pretty()
{
        "_id" : ObjectId("611369afb4249744caec5506"),
        "tags" : [
                "blank",
                "red"
        ]
}
{
        "_id" : ObjectId("611369afb4249744caec5507"),
        "tags" : [
                "red",
                "blank"
        ]
}
{
        "_id" : ObjectId("611369afb4249744caec5508"),
        "tags" : [
                "red",
                "blank",
                "plain"
        ]
}
{
        "_id" : ObjectId("611369afb4249744caec5509"),
        "tags" : [
                "blank",
                "red"
        ]
}
```

- **单个查询**

对在数组中的元素进行单个查询，只需要为一个数组元素进行指定一个元素即可：

`tags: "plain"`:

```js
> db.inventory.find( { tags: "plain"  },{ tags:1} ).pretty()
{
        "_id" : ObjectId("611369afb4249744caec5508"),
        "tags" : [
                "red",
                "blank",
                "plain"
        ]
}
```

- **查询特定长度的数组**

直接使用`$size`即可得到固定长度的数组。

```js
> db.inventory.find( { tags: {$size : 3}  },{ tags:1} ).pretty()
{
        "_id" : ObjectId("611369afb4249744caec5508"),
        "tags" : [
                "red",
                "blank",
                "plain"
        ]
}
```

- **满足多个条件的数组元素的查询**

使用这个标签来进行查询`$elemMatch`:

```js
> db.inventory.find({dim_cm:{$elemMatch:{$lt:15,$gt:11}}},{"dim_cm":1}).pretty()
{ "_id" : ObjectId("611369afb4249744caec5506"), "dim_cm" : [ 14, 21 ] }
{ "_id" : ObjectId("611369afb4249744caec5507"), "dim_cm" : [ 14, 21 ] }
{ "_id" : ObjectId("611369afb4249744caec5508"), "dim_cm" : [ 14, 21 ] }
```

### 对文档数组进行查询

**给予**如下的集合结构：

```js
db.inventory.insertMany( [
   { item: "journal", instock: [ { warehouse: "A", qty: 5 }, { warehouse: "C", qty: 15 } ] },
   { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] },
   { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] },
   { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
]);
```

如果想要对文档数组进行查询，最基础的是写上其中的一个限定的对象，例如：

```js
db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } )
> db.inventory.find( { "instock": { warehouse: "A", qty: 5 } } ).pretty()
{
        "_id" : ObjectId("6113cb06b4249744caec550b"),
        "item" : "journal",
        "instock" : [
                {
                        "warehouse" : "A",
                        "qty" : 5
                },
                {
                        "warehouse" : "C",
                        "qty" : 15
                }
        ]
}
```

但是如果出现文档中的顺序不对时，是无法对这个结构进行查询的：

```js
db.inventory.find( { "instock": { qty: 5, warehouse: "A" } } )
```

- **限定符来进行查询**

下面的示例查询 instock 数组中至少有一个嵌入文档包含大于10且小于或等于20的字段 qty 的文档:

```js
db.inventory.find( { "instock": { $elemMatch: { qty: { $gt: 10, $lte: 20 } } } } )
```

如果不添加elemMatch，则表示这个数组中至少有文档满足其中的一个条件（不太常用）。

### 对空值null进行查询

首先添加带有null的文档结构：

```js
db.inventory.insertMany([
   { _id: 1, item: null },
   { _id: 2 }
])
```

但是如果直接对null值进行查询，则会直接出现问题，比如：

```js
> db.inventory.find()
{ "_id" : 1, "item" : null }
{ "_id" : 2 }
```

使用`$type:10`就是直接对类型进行判断，可以查看是否满足null。

同时也可以使用`$exists:false`来判断这个元素是否存在某个固定的值。

```js
> db.inventory.find({item:{$type:10}})
{ "_id" : 1, "item" : null }
> db.inventory.find({item:{$exists:false}})
{ "_id" : 2 }
```

### projection 参数的使用方法

```js
db.collection.find(query, projection)
```

若不指定 projection，则默认返回所有键，指定 projection 格式如下，有两种模式

```js
db.collection.find(query, {title: 1, by: 1}) // inclusion模式 指定返回的键，不返回其他键
db.collection.find(query, {title: 0, by: 0}) // exclusion模式 指定不返回的键,返回其他键
```

_id 键默认返回，需要主动指定 _id:0 才会隐藏

**两种模式不可混用**（因为这样的话无法推断其他键是否应返回）

```js
db.collection.find(query, {title: 1, by: 0}) // 错误
```

**只能全1或全0**，**除了在inclusion模式时可以指定_id为0**

```js
db.collection.find(query, {_id:0, title: 1, by: 1}) // 正确
```

## 3.Update Documents

```js
db.inventory.insertMany( [
   { item: "canvas", qty: 100, size: { h: 28, w: 35.5, uom: "cm" }, status: "A" },
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "mat", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
   { item: "mousepad", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "P" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
   { item: "sketchbook", qty: 80, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "sketch pad", qty: 95, size: { h: 22.85, w: 30.5, uom: "cm" }, status: "A" }
] );
```

**对一个文档进行更新：**

使用updateOne() 方法来进行操作：

格式：

```js
db.collection.updateOne(
   <filter>,
   <update>,
   {
     upsert: <boolean>,
     writeConcern: <document>,
     collation: <document>,
     arrayFilters: [ <filterdocument1>, ... ]
   }
)
```

最重要的是`filter>`和`update`，第一个是对所有数据进行筛选，第二个是将对应的数据进行修改。

```js
db.inventory.updateOne(
   { item: "mat" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)

```

**$currentDate: { lastModified: true }**，就是将文档中名为的元素`lastModified`修改为当前的时间。

更多的操作符可以参考 [CURD的具体操作符](CURD的具体操作符.md) ，不做过多的展示了。

