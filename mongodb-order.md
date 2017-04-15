# mongodb 的使用

## 启动mongode
获得mongodb的服务链接
```
sudo service mongod restart
```
启动mongo
```
mongo
```
## 数据库的创建
使用`use dbname`命令创建新数据库，如果dbname已经存在，那么就切换到数据库
```
> use stu
switched to db stu
```
使用`show dbs` 查看所有数据库  
注意：数据库中没有数据是看不见的
```
> show dbs
admin  0.000GB
local  0.000GB
stu    0.000GB
```
使用`db`可以查看现在所在的数据库
```
> db
stu
```

## 数据库的删除
使用`db.dropDatabase()`来删除数据库
```
> db
stu
> db.dropDatabase()
{ "dropped" : "stu", "ok" : 1 }
> show dbs
local  0.000GB
test   0.000GB
```
## mongodb集合的创建和删除
### 集合的创建
在数据库中写入数据，必须创建集合，【one,two就是集合的名字】
```
db.one.insert({"name":"lisi", "age":12})
db.two.insert({"name":"lisi", "age":12})
```
通过`show collections`或者`db.getCollectionNames()`可以看到当前数据库下所有集合的名字
```
> show collections
one
two
> db.getCollectionNames();
[ "one", "two" ]
```
使用`db.one.find()`查看指定集合下的数据
```
> db.one.find()
{ "_id" : ObjectId("58f18cb057394eff252bc86e"), "name" : "lisi", "age" : 12 }
> db.two.find()
{ "_id" : ObjectId("58f18cd957394eff252bc86f"), "name" : "lisi", "age" : 12 }
```
### 集合的删除
使用 `db.collectionName.drop()`删除集合
```
> db.one.drop()
true
```
## Mongodb文档操作
文档的数据结构和JSON基本一样,所有存储在集合中的数据都是BSON格式。  
BSON是一种类json的一种二进制形式的存储格式,简称Binary JSON。
### 插入方法：
`db.collectionName.insert(doc)`  
`db.collectionName.save(doc)`
```
直接插入：
> db.one.insert({"name":"lisi", "age":12})
WriteResult({ "nInserted" : 1 })
> db.one.find()
{ "_id" : ObjectId("578b4fc554ec6d203080b398"), "name" : "lisi", "age" : 12 }
>

先保存于变量中：
> doc = {"name":"zhangsan", "age":13};
{ "name" : "zhangsan", "age" : 13 }
> db.one.insert(doc);
WriteResult({ "nInserted" : 1 })
>
```
如果不指定`_id` 字段 `save() 方法`类似于 `insert() 方法`。如果指定` _id` 字段，则会`更新`该 `_id`的数据。

