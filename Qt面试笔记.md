### 1、Qt三大核心机制

-  信号槽
- 元对象系统
- 事件模型


### 2、Qt对象树

QT提供了对象树机制，能够自动、有效的组织和管理继承自QObject的对象。

每个继承自QObject类的对象通过它的对象链表(QObjectList)来管理子类对象，当用户创建一个子对象时，其对象链表相应更新子类对象的信息，对象链表可通过children()获取。

当父类对象析构的时候，其对象链表中的所有(子类)对象也会被析构，父对象会自动，将其从父对象列表中删除，QT保证没有对象会被delete两次。开发中手动回收资源时建议使用deleteLater代替delete,因为deleteLater多次是安全的。 


### 3、描述Qt中的文件流(QTextStream)和数据流(QDataStream)的区别

在Qt中，QTextStream和QDataStream都是用于读写数据的类，但它们的使用场景和读写的数据类型不同。 
- QTextStream是用于读写文本数据的类，可以将QString、QByteArray等文本数据以类似于流的方式读写到文件、套接字等设备中。QTextStream提供了一系列方便的方法，如readLine()、readAll()、operator<<()、operator>>()等，可以方便地读写文本文件和套接字等设备。 
- QDataStream是用于读写二进制数据的类，可以将各种类型的二进制数据（如int、float、double、QString等）以二进制方式读写到文件、套接字等设备中。QDataStream通过内部的序列化机制实现了数据的高效读写，可以保证在不同平台上的数据兼容性。QDataStream提供了一系列方便的方法，如writeRawData()、writeInt()、readRawData()、readInt()等，可以方便地读写二进制文件和套接字等设备。 

因此，QTextStream和QDataStream的主要区别在于它们所处理的数据类型不同，QTextStream主要处理文本数据，而QDataStream主要处理二进制数据。


### 4、动态库和静态库

静态库：在链接阶段将汇编生成的目标文件.o与引用库一起链接打包到可执行文件中，可简单看成（.o或者.obj文件的集合）。
（1）对函数库的链接是放在编译时期完成的
（2）程序在运行时与函数库没有瓜葛，移植方便
（3）浪费空间和资源

动态库：
（1）将库函数的链接载入推迟到程序运行时期
（2）可以实现进程间的资源共享（因此也称为共享库）
（3）将一些程序升级变得简单
（4）可以真正的做到链接载入完全由程序员在程序代码中控制（显示调用）

动态库一般也会有个lib文件，那么和静态库lib文件有什么区别？

动态库中的.lib文件叫做导入库，对于导入库而言，其实际的执行代码位于动态库中，导入库只包含了地址符号表等，确保程序找到对应函数的一些基本地址信息。

静态库中的.lib叫做静态库，本身就包含了实际执行代码、符号表等等。 

参考：
- https://blog.csdn.net/weixin_71478434/article/details/126588174

### 5、Qt内存管理机制

1. 所有继承自QOBJECT类的类，如果在new的时候指定了父亲，那么它的清理时在父亲被delete的时候delete的，所以如果一个程序中，所有的QOBJECT类都指定了父亲，那么他们是会一级级的在最上面的父亲清理时被清理，而不用自己清理；
2. 如果自行delete掉这些QT接管负责销毁的指针了会出现什么情况呢，如果时这样的话，正常情况下QT的拥有这个对象的那个父亲会知道这件事情，它会直到它的儿子被你直接DELETE了，这样它会将这个儿子移出它的列表，并且重新构建显示内容，但是直接这样做时有风险的！也就是要说的下一条。
3. 当一个QOBJECT正在接受事件队列时如果中途被你DELETE掉了，就是出现问题了，所以QT中建议大家不要直接DELETE掉一个 QOBJECT，如果一定要这样做，要使用QOBJECT的deleteLater()函数，它会让所有事件都发送完一切处理好后马上清除这片内存，而且就算调用多次的deletelater也不会有问题。
4. QT不建议在一个QOBJECT 的父亲的范围之外持有对这个QOBJECT的指针，因为如果这样外面的指针很可能不会察觉这个QOBJECT被释放，会出现错误，如果一定要这样，就要记住你在哪这样做了，然后抓住那个被你违规使用的QOBJECT的destroyed（）信号，当它没有时赶快置零你的外部指针。当然我认为这样做是及其麻烦也不符合高效率编程规范的，所以如果要这样在外部持有QOBJECT的指针，建议使用引用或者用智能指针，如QT就提供了智能指针针对这些情况，见***一条。***


### 6、QApplication的主要作用是什么？
QApplication对象管理QtGui应用程序的控制流程和主要的设置参数


### 7、Qt都提供哪些标准对话框以供使用，他们实现什么功能

- QColorDialog颜色对话框，能够允许用户选择颜色、
- QErrorMessage显示错误信息
- QFileDialog文件对话框，能够允许用户选的一个或者多个文件以及目录
- QFontDialog字体对话框,允许用户选择/设置字体
- QInputDialog输入对话框,允许用户进行简单的输入
- QPageSetupDialog叶设置对话框，配置与页相关的打印机选项
- QProgressDialog进度对话框指示一个长时间操作的工作进度,以提示用户该操作是否已经停止
- QPrintDialog打印对话框，配置打印机，可以允许用户选择可用的打印机
- QMessageBox消息提示


### 8、Qt数据库

在项目.pro文件中新增sql模块，Qt中封装了一些工具类统一不同数据库接口，它提供了一系列的函数来连接、操作、查询和管理数据库。

在Qt中可以使用：
- QSqlDatabase类，数据库对象
- QSqlQuery类来查询数据库
- QSqlTableModel类来操作数据库表
- QSqlRelationalTableModel类来管理关系数据库表
- QSqlError类来检测错误
- QSqlDriver类来检查数据库驱动程序
- QSqlIndex类来创建索引
- QSqlRecord类来操作数据库记录
- QSqlResult类来执行查询

### Qt中的插件是什么？

插件是Qt中用于扩展应用程序功能的一种机制。插件可以是动态链接库或静态链接库，包含了一些特定的功能。Qt提供了QPluginLoader类和QFactoryInterface类用于管理插件。通过插件，可以将应用程序的功能分解为多个独立的部分，方便开发和维护。

### Qt操作INI文件、JSON 文件、XML文件？

Qt提供了三种常用的文件格式处理方式，分别是INI文件、JSON文件和XML文件。下面分别介绍如何在Qt中操作这三种文件格式。

1. 操作INI文件 INI文件是一种常用的配置文件格式，在Qt中可以通过QSettings类来读写INI文件。以下是一个例子：

```
QSettings settings("myApp.ini", QSettings::IniFormat);
// 写入配置项
settings.setValue("General/Name", "Tom");
settings.setValue("General/Age", 20);
// 读取配置项
QString name = settings.value("General/Name").toString();
int age = settings.value("General/Age").toInt();
```

2.操作JSON文件 JSON是一种轻量级的数据交换格式，在Qt中可以通过QJsonDocument类和QJsonObject类来读写JSON文件。以下是一个例子：

```
// 读取JSON文件
QFile file("data.json");
if (file.open(QIODevice::ReadOnly)) {
    QJsonParseError error;
    QJsonDocument doc = QJsonDocument::fromJson(file.readAll(), &error);
    if (error.error == QJsonParseError::NoError && doc.isObject()) {
        QJsonObject obj = doc.object();
        QString name = obj.value("name").toString();
        int age = obj.value("age").toInt();
    }
    file.close();
}
// 写入JSON文件
QJsonObject obj;
obj.insert("name", "Tom");
obj.insert("age", 20);
QJsonDocument doc(obj);
QFile file("data.json");
if (file.open(QIODevice::WriteOnly)) {
    file.write(doc.toJson());
    file.close();
}
```

3.操作XML文件 XML是一种常用的文本格式，用于表示结构化的数据，在Qt中可以通过QXmlStreamReader类和QXmlStreamWriter类来读写XML文件。以下是一个例子：

```
// 读取XML文件
QFile file("data.xml");
if (file.open(QIODevice::ReadOnly)) {
    QXmlStreamReader reader(&file);
    while (!reader.atEnd()) {
        reader.readNext();
        if (reader.isStartElement() && reader.name() == "person") {
            QString name = reader.attributes().value("name").toString();
            int age = reader.attributes().value("age").toInt();
            // 处理读取到的数据
        }
    }
    file.close();
}
// 写入XML文件
QFile file("data.xml");
if (file.open(QIODevice::WriteOnly)) {
    QXmlStreamWriter writer(&file);
    writer.setAutoFormatting(true);
    writer.writeStartDocument();
    writer.writeStartElement("persons");
    writer.writeStartElement("person");
    writer.writeAttribute("name", "Tom");
    writer.writeAttribute("age", "20");
    writer.writeEndElement();
    writer.writeEndElement();
    writer.writeEndDocument();
    file.close();
}
```

以上是Qt中操作INI文件、JSON文件和XML文件的简单示例，通过这些方法，我们可以方便地读写和处理各种数据格式的文件。 


### Qt相机和视频处理技术？
Qt相机和视频处理技术是Qt提供的一组API和库，用于在Qt应用程序中访问摄像头和处理视频。以下是一些常用的Qt相机和视频处理技术：

1. QtMultimedia模块：提供了访问音频、视频、相机等多媒体设备的API。可以用来在Qt应用程序中访问相机，捕捉视频和音频流，以及播放音频和视频文件。
2. QtCamera模块：是QtMultimedia模块的一部分，提供了访问相机的API。可以用来获取相机的设备信息、预览相机的图像、捕捉相机的图像和视频等。
3. QtAV库：是一个基于Qt的多媒体框架，提供了高性能的视频和音频处理能力。可以用来播放各种格式的视频和音频文件，以及进行视频编解码和处理。
4. OpenCV库：是一个开源的计算机视觉库，提供了丰富的图像和视频处理算法。可以用来在Qt应用程序中实现图像和视频的处理，例如人脸识别、目标跟踪、图像滤波等。
5. FFmpeg库：是一个开源的音视频编解码库，支持多种音视频格式和编解码器。可以用来在Qt应用程序中实现音视频的录制、播放和转码等功能。 通过使用这些技术，开发者可以方便地在Qt应用程序中访问相机和处理视频，实现各种有趣的功能，例如人脸识别、视频监控、视频编辑等。 
