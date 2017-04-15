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
# Mongodb文档操作   
文档的数据结构和JSON基本一样,所有存储在集合中的数据都是BSON格式。      
BSON是一种类json的一种二进制形式的存储格式,简称Binary JSON。  

## 插入方法：         
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
### 如果不指定`_id` 字段 `save() 方法`类似于 `insert() 方法`。如果指定` _id` 字段，则会`更新`该 `_id`的数据。              
## 更新文档           
更新方法：             
`db.collectionName.update()`                    
`db.collectionName.save()`          
插入格式：         
```
db.collectionName.update(
   { query},
   {update},
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
参数说明：
query : update的查询条件。
update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
writeConcern :可选，抛出异常的级别        
```
例如：
```
修改name==zhangsan 的age为20   
> db.one.update({"name":"zhangsan"},{$set:{"age":20}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.one.find()
{ "_id" : ObjectId("578b4fc554ec6d203080b398"), "name" : "lisi", "age" : 12 }
{ "_id" : ObjectId("578b503154ec6d203080b399"), "name" : "zhangsan", "age" : 20 }
>


给_id为ObjectId("578b503154ec6d203080b399")的文档添加一个"gender"
> db.one.save({"_id": ObjectId("578b503154ec6d203080b399"), "name":"zhangsan", "age":20, "gender":"male" })
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.one.find()
{ "_id" : ObjectId("578b4fc554ec6d203080b398"), "name" : "lisi", "age" : 12 }
{ "_id" : ObjectId("578b503154ec6d203080b399"), "name" : "zhangsan", "age" : 20, "gender" : "male" }//更新后
```
### 其他更新操作
```json
//更新第一条符合条件
db.one.update({"name":"zhangsan"}, {$set:{"age": 30}})

//更新所有符合条件
db.one.update({"name":"zhangsan"}, {$set:{"age": 30}}, false,true)

//没有符合条件的,添加一条文档进去
db.one.update({"name":"zhangsan"}, {$set:{"age": 30}}, true,true)

//将所有name=zhangsan的文档的age增加5  
db.one.update({"name":"zhangsan"}, {$inc:{"age": 5}}, false,true)

//将所有name=zhangsan的文档的age和course字段删除   
db.one.update({"name":"zhangsan"}, {$unset: {"age":1, "course":1}}, false,true)

//在一个数组域中添加一个数据并且将size域的值加１
db.one.update({"age":10}, {$push:{"course":"html"}, $inc:{"size": 1}}, false, false)

//在一个数组域中添加多个数据
db.one.update({"age":10}, {$pushAll:{"course":["html", "python"]}}, false, false)

//删除数组域中的一个数据
db.firstClass.update({"age":20},{$pop:{"course":-1}},false,false) //删除course第一个
db.firstClass.update({"age":20},{$pop:{"course":1}},false,false) //删除course最后一个

//删除数组域中的某个特定数据
db.firstClass.update({"age":20}, {$pull: {"course": "python"}}, false,false)

//删除数组域中的多个数据
db.firstClass.update({"age":20}, {$pullAll: {"course": ["python", "c"]}}, false,false)

//修改某个域的名字
db.firstClass.update({"age":20}, {$rename: {"course": "class"}}, false,false)

//在某个数组域中添加数据(数据不存在时，才会添加)
db.firstClass.update({"age":20}, {$addToSet: {"course": "python"}}, false,true)
```
## 删除文档      
#### 删除文档：    
`db.collectionName.remove()`  
删除格式  
```
db.collectionName.remove(
   {query},
   {
     justOne: boolean,
     writeConcern: document
   }
)
参数说明：
query:  删除指定条件的文档　
justOne:  设置为true或者１时，仅仅删除第一个符合条件的文档, 默认删除所有符合条件的
writeConcern: 抛出异常　　　

> db.firstClass.find()
{ "_id" : ObjectId("578c2a7b7909b4c6e08a7cbe"), "name" : "lisi", "age" : 10 }
{ "_id" : ObjectId("578c2b6c7909b4c6e08a7cbf"), "name" : "lisi1", "age" : 10 }
{ "_id" : ObjectId("578c2b707909b4c6e08a7cc0"), "name" : "lisi2", "age" : 10 }
> db.firstClass.remove({"age":10},1)   //删除了第一条符合条件的文档
WriteResult({ "nRemoved" : 1 })
> db.firstClass.find()
{ "_id" : ObjectId("578c2b6c7909b4c6e08a7cbf"), "name" : "lisi1", "age" : 10 }
{ "_id" : ObjectId("578c2b707909b4c6e08a7cc0"), "name" : "lisi2", "age" : 10 }
>

> db.firstClass.remove({})　//删除该集合中所有的文档　
WriteResult({ "nRemoved" : 2 })

```
