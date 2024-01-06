### 1、Qt网络编程

首先需要在.pro中添加`network`模块

一些相关的头文件和类

```
//HTTP请求相关
#include <QNetworkReply>
#include <QNetworkRequest>
#include <QNetworkAccessManager>

//TCP相关
#include <QTcpServer>
#include <QTcpSocket>

//UDP相关
#include <QHostAddress>
#include <QUdpSocket>
```

### 2、描述UDP 之 UdpSocket通讯

UDP（User Datagram Protocol即用户数据报协议）是一个轻量级的，不可靠的，面向数据报的无连接协议。在网络质量令人十分不满意的环境下，UDP协议数据包丢失严重。由于UDP的特性：它不属于连接型协议，因而具有资源消耗小，处理速度快的优点，所以通常音频、视频和普通数据在传送时使用UDP较多，因为它们即使偶尔丢失一两个数据包，也不会对接收结果产生太大影响。所以QQ这种对保密要求并不太高的聊天程序就是使用的UDP协议。

在Qt中提供了QUdpSocket 类来进行UDP数据报（datagrams）的发送和接收。Socket简单地说，就是一个IP地址加一个port端口 。

流程：
①创建QUdpSocket套接字对象 
②如果需要接收数据，必须绑定端口 
③发送数据用writeDatagram，接收数据用 readDatagram 。 


### 3、TCP通信

**服务端：（QTcpServer）**
①创建QTcpServer对象
②监听list需要的参数是地址和端口号
③当有新的客户端连接成功回发送newConnect信号
④在newConnection信号槽函数中，调用nextPendingConnection函数获取新连接QTcpSocket对象
⑤连接QTcpSocket对象的readRead信号
⑥在readRead信号的槽函数使用read接收数据
⑦调用write成员函数发送数据


**客户端：（QTcpSocket）**
①创建QTcpSocket对象
②当对象与Server连接成功时会发送connected 信号
③调用成员函数connectToHost连接服务器，需要的参数是地址和端口号
④connected信号的槽函数开启发送数据
⑤使用write发送数据，read接收数据 



#### Qt 网络通信中，TCP/UDP 整体流程(服务器，客户端) ？

在Qt中，TCP/UDP网络通信的整体流程主要包括以下步骤：

服务器端的创建和监听

服务器端首先需要创建一个QTcpServer或QUdpSocket对象，用于监听客户端的连接或接收数据。创建时需要指定端口号和IP地址（如果有多个网卡，则需要指定监听的网卡地址）。然后调用listen()函数开始监听客户端的连接或数据包的到来。

客户端的连接或数据发送

客户端需要创建一个QTcpSocket或QUdpSocket对象，用于连接服务器或发送数据包。对于TCP通信，客户端需要调用connectToHost()函数连接服务器；对于UDP通信，客户端可以直接使用writeDatagram()函数发送数据包。

服务器端的响应和数据处理

当客户端连接到服务器或发送数据包时，服务器端会触发相应的信号（如newConnection()、readyRead()等），在相应的槽函数中进行响应和数据处理。对于TCP通信，服务器端需要在newConnection()槽函数中调用nextPendingConnection()函数获取客户端的QTcpSocket对象，然后在客户端的QTcpSocket对象上调用read()函数读取数据；对于UDP通信，服务器端可以直接在readyRead()槽函数中调用readDatagram()函数读取数据包。

客户端的数据接收和响应

当客户端连接到服务器或发送数据包时，客户端会触发相应的信号（如connected()、readyRead()等），在相应的槽函数中进行数据接收和响应。对于TCP通信，客户端需要在connected()槽函数中调用write()函数发送数据，然后在readyRead()槽函数中调用read()函数读取服务器端的响应数据；对于UDP通信，客户端可以直接在readyRead()槽函数中调用readDatagram()函数读取服务器端的响应数据包。

断开连接和清理资源

当通信完成或出现错误时，需要断开连接并清理资源。对于TCP通信，可以在客户端或服务器端的QTcpSocket对象上调用disconnectFromHost()函数或close()函数断开连接，并在socketDisconnected()槽函数中清理资源；对于UDP通信，不需要显式地断开连接，系统会自动管理资源。

综上所述，TCP/UDP网络通信的整体流程包括服务器端的创建和监听、客户端的连接或数据发送、服务器端的响应和数据处理、客户端的数据接收和响应，以及断开连接和清理资源。根据具体的需求，可以选择使用QTcpServer、QTcpSocket、QUdpSocket等类进行开发。



### 4、Qt Socket通信的过程

Qt Socket通信的过程主要分为以下几步：

1. 创建Socket：使用QTcpSocket类创建Socket，并初始化连接参数；
2. 连接服务器：使用connectToHost()函数连接服务器；
3. 发送数据：使用write()函数发送数据；
4. 接收数据：使用read()函数接收数据；
5. 断开连接：使用disconnectFromHost()函数断开连接。 

### Socket如何传输struct？

因为在Socket中，一般传输的是char类型，在Qt中可以使用QByteArray，根据C++中char*数据与结构体之间的映射，可以实现结构体与QByteArray的转化！

比如简单定义一个结构体：
```
struct Person
{
    char name[128];
    int age;
};
```
发送端：

```
void MainWindow::on_pushButton_Send_Struct_clicked()
{
    char *ptr = ui->textEdit_Send_Name->toPlainText().toLatin1().data();
    memcpy(mPerson.name, ptr, 128);
    mPerson.age = ui->textEdit_Send_Age->toPlainText().toInt();

    QByteArray myByteArray;
    myByteArray.append(reinterpret_cast<char*>(&mPerson), sizeof (mPerson));
    socket->write(myByteArray.data(), sizeof(mPerson));
    socket->flush();
    qDebug() << "write to struct to server......";
}

代码解释：

toPlainText()返回QString，
toLatin1()是将QString转为QByteArray，
data()是将QByteArray转为指向char类型的指针，
memcpy是通用的数据拷贝函数，将数据从一个地方拷贝到另一个地方。

reinterpret_cast运算符是C++用来处理无关类型之间的转换；
reinterpret，即“重新解释”，顾名思义，这个强制类型转换的作用是提供某个变量在底层数据上的重新解释。当我们对一个变量使用reinterpretcast后，编译器将无视任何不合理行为，强行将被转换变量的内存数据重解释为某个新的类型。需要注意的是，reinterpretcast要求转换前后的类型所占用内存大小一致，否则将引发编译时错误。
```

接收端：
```
void MainWindow::socket_Read_Data()
{
    QByteArray buffer;
    buffer = socket->readAll();
    if (!buffer.isEmpty()) {
        Person * p = reinterpret_cast<Person *>(buffer.data());
        qDebug() << "read from client......" << p->name << "  " << p->age;
        ui->textEdit_Recv->setText(QString(p->name) + " " + QString::number(p->age));
    }
}

代码解释：

data()是将QByteArray转为指向char类型的指针，
然后转换成Person类型的指针。
QString()构造函数可以把char[]转为QString，
QString::number类公有函数把int转为QString。
```

### 5、Qt实现FTP文件上传和下载

QT已经封装了QNetworkAccessManager类来实现ftp协议，只需要简单的配置url/用户名/密码即可实现。

参考：
- [Qt之QNetworkAccessManager 实现FTP文件上传/下载功能](https://blog.csdn.net/ilson_/article/details/97829233)


### 6、Qt实现消息队列

- [Qt实现消息队列](http://www.cleartechfei.com/2019/03/qttaskqueue/)
- 


