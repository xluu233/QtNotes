
## Qt数据类型

由于Qt特有的元对象系统，Qt维护了一套自己的数据结构，

### 基本类型

| qt类型 | c++标准类型 | 备注 |
| --- | --- | --- |
qint8 |	signed char	 |有符号8位数据类型
qint16|	signed short|	有符号16位数据类型
qint32|	signed int|	有符号32位数据类型
qint64|	long long int（或__int64）|	有符号64位数据类型
qintptr|	qint32 或 qint64|	指针类型，用于带符号整型。 （32位系统为qint32、64位系统为qint64）
qlonglong|	long long int 或(__int64)|	和qint64定义一样
qptrdiff|	qint32 或 qint64	|表示指针差异的整型。32位系统为qint32、64位系统为qint64
qreal|	double|	除非配置了-qreal float选项，否则默认为double
quint8|	unsigned char|	无符号8位数据类型
quint16|	unsigned short	|无符号16位数据类型
quint32	|unsigned int|	无符号32位数据类型
quint64|	unsigned long long int 或 (unsigned __int64)|	无符号64位数据类型，Windows中定义为unsigned __int64
quintptr|	quint32 或 quint64	|指针类型，用于无符号整型。32位系统为quint32、64位系统为quint64
qulonglong|	unsigned long long int 或 (unsigned __int64)|	和quint64定义一样
uchar|	unsigned char|	无符号字符类型
uint|	unsigned int|	无符号整型
ulong|	unsigned long|	无符号长整型
ushort|	unsigned short	|无符号短整型

### 封装类型

| 类型 | 备注 |
| --- | --- |
| QByteArray  | 字节数组
| QString | Unicode字符的字符串，类似std::string
| QStringList | 字符串视图，不直接对string操作，类似std::string_view
| QVector | QVector是Qt对所有数组的封装，可变大小
| QList | 动态扩容的数组，类似std::vector
| QVector | 动态数组，本身和QList区别很小，在qt6后和QList统一了，QVector的内部实现就是QList
| QLinedList | 链表，qt6中去掉了, 建议使用std::list
| QMap | 存储key-value键值对，内部使用平衡二叉树（红黑树）
| QHash | 存储key-value键值对，内部使用哈希表
| QBuffer | QBuffer类是一个操作QByteArray的输入、输出设备的接口。
| QVariant | QVariant变量是没有数据类型的，可以进行任意类型转换。

### 关于Qt数据类型的一些疑惑

#### 1、QByteArray和QString和char*之间区别

- **QByteArray**：提供一个字节数组,可用于存储原始字节`byte`（包括“\ 0” ）和传统的8位 “\ 0” 端接字符串。使用QByteArray比使用char数组更方便。在IO操作中，c语言常使用`const char*`，而Qt中常使用QByteArray


- **QString**：QString类提供了一个Unicode字符字符串，注意是Unicode字符的字符串。QByteArray与QString互转极为简单，二者从本质上类似，都是连续存储，区别是前者可以存无法显示的字符，后者只存可显示的字符


 - **char**：c/c++中的char类型原生数组



```
//QByteArray转为QString示例：
QByteArray ba("abc123"); 
QString str = ba; //或str.prepend(ba); 
qDebug()<<str ; //输出："abc123"


//QString转为QByteArray示例：
QString str("abc123"); 
QByteArray ba = str.toLatin1(); 
qDebug()<<ba; //输出："abc123"
```


#### 2、QMap与QHash的差别

- QMap和QHash都是存储键值对的数据结构，QMap是基于红黑树，QHash是基于hash表。
- QHash具有比QMap更快的查找速度
- QHash以任意的顺序存储数据项，而QMap总是按照键Key顺序存储数据
- QHash的键类型Key必须提供operator==()和一个全局的qHash(Key)函数，而QMap的键类型Key必须提供

#### 3、QList和QVector差别

> 由于QT存在众多历史版本，在之前的时候c++标准库并不完善，QT的容器类也是一步一步完善的，所以QList和QVector之前存在很多历史渊源。

**网上很多版本说QList和QVector区别，各执己见，更有甚者说QList是一个链表，维护一个指针数组，指针指向存储的给定类型的值。还有说QList是一个特殊的数组，根据存储对象不同选择不同的方式。**


我们不讨论Qt4版本，仅以Qt5高版本和Qt6来看,在我的实际测试中，在Qt5.14版本

- QList和QVector都是一个动态扩容的数组，
- QList是不支持移动赋值，只有拷贝赋值。
- QVector是支持移动赋值和拷贝赋值的。


**在Qt官方介绍中，QVector效率是优于QList的：**

QList就像是一个vector和list的结合体一样。在对象占用内存大的时候（大于sizeof(void*)），它每次会从堆中分配内存。然后将内存的起始地址放入数组中。对于小内存对象，他直接将其存储到数组中，这样不需要经过堆分配内存。

相对于QVector慢的原因是它每次都要经过堆分配内存，而QVector可以预分配内存从而提高push_back的运行效率，所以相对于大内存QList本身也有很大的性能提升，但是由于它每次需要用到sizeof(void*)的内存，也会导致更多的内存分配，所以运行效率还是不如QVector。

**QList的优点：**
当对占用大内存对象进行重新排续的时候，QVector只能进行大量内存移动，而QList则只需要移动对象指针即可。相对于std::list，QList在单纯的push_back和枚举的时候也有不错的表现。


**Qt6中的QList变化**

**QVector和QList统一了，不在有选择困难症了，[参考文档](https://www.qt.io/blog/qlist-changes-in-qt-6#commento)**

在此之前，Qt为这两个容器提供了非常不同的实现：QVector是一个自然而直观的类似数组的容器，而QList在其实现中非常特殊，以很好地适应Qt本身定义和使用的类型。通过对现有类型的Qt6更新，在以前版本中已经完成的工作的支持下，类之间的实现差异似乎没有什么好处。此外，在许多情况下，QVector被证明是一个更好的选择。

因此，在Qt 6中，QVector和QList是统一的，并以QVectory模型作为底层实现。对于更高级的框架用户来说，这意味着QT5 QList对泛型类型的额外间接级别已经消失，元素总是直接存储在分配的内存中。

然而，我们决定QList应该是真正的类，而QVector应该只是QList的别名。这样做的目的是：
- 简化移植工作，因为许多QTAPI使用QList而不是QVector.我们认为用户代码也是如此，至少在与Qt的交互级别上是这样
- 明确说明QVector2D和朋友与QVector2D无关
- 与QStringList和QByteArrayList命名约定同步

**新增特性：**
- QList可能会在元素移除时缩小
- QList的内存不受2GiB的限制

#### 4、QLinkedList

`QLinkedList`是qt封装的链表数据类型，但是在QT6中被删除了，不知道为什么。

#### 5、QVariant

QVariant是Qt中的一个通用的值容器，它可以存储任意类型的数据，例如整数、字符串、列表等等。

```
    // 存储数据
    QVariant v1 = 10;  // 存储整数
    QVariant v2 = "hello";  // 存储字符串
    QVariant v3 = QList<int>() << 1 << 2 << 3;  // 存储整数列表

    // 获取数据
    int n = v1.toInt();  // 将整数转换为int类型
    QString str = v2.toString();  // 将字符串转换为QString类型
    QList<int> list = v3.toList();  // 将整数列表转换为QList<int>类型

    qDebug() << "v1 = " << n;  // 输出整数
    qDebug() << "v2 = " << str;  // 输出字符串
    qDebug() << "v3 = " << list;  // 输出整数列表
```

### Qt中的MVD

Qt的MVD包含三个部分Model（模型），View（视图），代理（Delegate）。
- Model否则保存数据
- View负责展示数据
- Delegate负责Item样式绘制或处理输入

这三部分通过信号槽来进行通信，当Model中数据发生变化时将会发送信号到View，在View中编辑数据时，Delegate负责将编辑状态发送给Model层。基类分别为QAbstractItemModel、QAbstractItemView、QAbstractItemDelegate。Qt中提供了默认实现的MVD类，如QTableWidget、QListWidget、QTreeWidget等。 

Qt中的View主要有三种QListView，QTreeView, QTabelView
对应的Model是：QStringListModel, QAbstractItemModel , QStandardItemModel。

### 智能指针

**标准库中的三种智能指针：**

-  `std::shared_ptr`：使用引用计数，每一个shared_ptr的拷贝都指向相同的内存，每次拷贝都会触发引用计数+1，每次生命周期结束析构的时候引用计数-1，在最后一个shared_ptr析构的时候，内存才会释放。
- `std::weak_ptr`：用来监视shared_ptr的生命周期，它不管理shared_ptr内部的指针，它的拷贝析构都不会影响引用计数，纯粹是作为一个旁观者监视shared_ptr中管理的资源是否存在，可以用来返回this指针和解决循环引用问题。
- `std::unique_ptr`：独占型的智能指针，它不允许其它智能指针共享其内部指针，也不允许unique_ptr的拷贝和赋值。

**Qt中的智能指针：**

- `QPointer`，提供了指向QObject的保护指针
> QPointer只能用于指向QObject及派生类的对象。当一个QObject或派生类对象被删除后，QPointer能自动将其内部的指针设置为0，这样在使用QPointer之前就可以判断一下是否有效。QPointer对象超出作用域时，并不会删除它指向的内存对象。

- `QScopedPointer`，和std::unique_ptr其概念是一样的
- `QSharedPointer`，和std::shared_ptr其作用是一样的
- `QWeakPointer`，和std::weak_ptr功能是一样的



### 空指针，野指针，垂悬指针，无效指针

- **空指针**：指针值为`NULL` `nullptr` `0`
- **野指针**：指针未初始化地址
- **垂悬指针**：指向已删除（或释放）的内存位置的指针称为悬空指针
- **无效指针**：指针指向内存的类型为`void*`,是一种特殊类型指针

### Qt的D指针（`d_ptr`）与Q指针（`q_ptr`）

**D指针**

- PIMPL模式，指向一个包含所有数据的私有数据结构体。
- 私有的结构体可以随意改变，而不需要重新编译整个工程项目
- 隐藏实现细节
- 头文件中没有任何实现细节，可以作为API使用
- 原本在头文件的实现部分转移到乐源文件，所以编译速度有所提高

**Q指针**

- 私有的结构体中储存一个指向公有类的Q指针。

**总结**

- Qt中的一个类常用一个PrivateXXX类来处理内部逻辑，使得内部逻辑与外部接口分开，这个PrivateXXX对象通过D指针来访问；在PrivateXXX中有需要引用Owner的内容，通过Q指针来访问。
- 由于D和Q指针是从基类继承下来的，子类中由于继承导致类型发生变化，需要通过`static_cast`类型转化，所以`DPTR()`与`QPTR()`宏定义实现了转换。