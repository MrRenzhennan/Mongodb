# Mongodb 的安装----->Ubuntu下
## 第一步：添加公共的keyserver
下载源文件
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```

## 第二步：配置命令版本
下面这条命令是 创建`/etc/apt/sources.list.d/mongodb-org-3.4.list`文件，  
将`deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse`写入这个文件(echo 原样输出)  
这样做的作用是mongodb的命令在ubuntu下适用
```
$ echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```
## 第三步：重新加载本地包数据库
更新本地数据，安装mongodb的最新的稳定版本
```
$ sudo apt-get update
```
## 第四步：安装mongodb包
```
$ sudo apt-get install -y mongodb-org
```

## 测试
如果按顺序执行上面四条命令，那么mongodb已经安装完毕  
输入
```
mongo
```
看到下面的样式
```
MongoDB shell version v3.4.3
connecting to: mongodb://127.0.0.1:27017
2017-04-15T10:11:12.975+0800 W NETWORK  [thread1] Failed to connect to 127.0.0.1:27017, in(checking socket for error after poll), reason: Connection refused
2017-04-15T10:11:12.990+0800 E QUERY    [thread1] Error: couldn't connect to server 127.0.0.1:27017, connection attempt failed :
connect@src/mongo/shell/mongo.js:237:13
@(connect):1:6
exception: connect failed

```
