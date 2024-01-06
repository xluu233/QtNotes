### 1、Qt如何保存一些临时设置

- 可以通过QSetting类创建file.ini配置文件，其可以保存一些持久化变量。参考：[QSettings读取配置信息](http://www.cleartechfei.com/2019/02/qsettingsreadiniinfo/)
- 自己创建JSON文件保存
- 通过一些kv存储三方库保存
- 数据库保存


### 2、Qt数据库

Qt原生支持SQLite和MySql,也可以通过QSqlDatabase::drivers()找到系统中所有可用的数据库驱动的名字列表。

可以使用QSqlDatabase类和QSqlQuery类来操作数据库。操作方式：
```

1. 添加数据库驱动：在.pro文件中添加QT += sql。
2. 创建数据库连接：使用QSqlDatabase::addDatabase()函数创建一个数据库连接。例如，QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL", "connection1");
3. 设置数据库信息：设置数据库服务器IP、数据库名、用户名、密码和端口号。例如，db.setHostName("127.0.0.1"); db.setDatabaseName("ceshi"); db.setUserName("root"); db.setPassword("123456"); db.setPort(3306);
4. 打开数据库：使用db.open()函数打开数据库。
5. 执行SQL查询：使用QSqlQuery类执行SQL查询。例如，QSqlQuery query(db); query.exec("create table newUser (id int primary key,username varchar (20))");。
6. 关闭数据库：使用QSqlDatabase::close()函数关闭数据库。
7. 移除数据库连接：如果不再需要数据库连接，可以使用QSqlDatabase::removeDatabase()函数移除该连接。
```

之前写的sql demo
- [MySQL_Demo](https://github.com/xluu233/MySQL_Demo)
- [SQLite_Demo](https://github.com/xluu233/SQLite_Demo)


### Qt读写XML文件

Qt读写XML的方式有两种:1.QXmlStream 2.QDomDocument
QXmlStream的优点是占用内存小，缺点是需要一部分一部分的读
QDomDocument的优点是可以把整个文件映射到内存中，缺点是占用内存大