## Qt事件

### 1、什么是事件？

事件(event)是软件与用户交互产生的系统反应，比如鼠标、键盘、窗口发生任何动作，都会有对应事件响应。

**大致可以包括：**
- 鼠标事件
- 键盘事件
- 窗口调整事件
- 模拟事件
- 系统事件（计时器、其他信号响应）


### 2、Qt是如何处理事件的

Qt中的事件被封装成`QEvent`类，所有的事件类都继承于QEvent类。大致流程可以分为`事件接收` -> `事件循环` -> `事件分发` -> `事件过滤` -> `事件处理`

#### **2.1 事件接收**
不同的操作系统对于事件有不同的API，Qt框架帮我们接收系统事件并封装成统一的事件信号，在发送给程序的EventLoop处理。

#### **2.2 事件循环：**

> Qt事件循环是一种程序架构，它用于处理窗口系统和其他用户界面事件，以及与用户界面无关的事件，例如定时器和网络事件。 Qt事件循环以循环的方式运行，每次循环都会检查是否有新的事件，如果有，就会调用相应的处理程序来处理它们。

主事件循环通过调用QCoreApplication::exec()启动，随着QCoreApplication::exit()结束，比如在main函数中我们会调用Application初始化：
```
int main(int argc, char *argv[])
{
    QCoreApplication app(argc, argv);
    //或者QGuiApplication， 或者 QApplication
    ...
    ...
    return app.exec();
}
```
Application类中，除去启动参数、版本等相关东西后，关键就是维护了一个QEventLoop，Application的exec就是QEventLoop的exec。

不过Application中的这个EventLoop，我们称作“主事件循环”Main EventLoop。所有的事件分发、事件处理都从这里开始。
Application还提供了sendEvent和poseEvent两个函数，分别用来发送事件：

- sendEvent发出的事件会立即被处理，也就是“同步”执行。
- postEvent发送的事件会被加入事件队列，在下一轮事件循环时才处理，也就是“异步”执行。
- 还有一个特殊的sendPostedEvents，是将已经加入队列中的准备异步执行的事件立即同步执行。


#### **2.3 事件分发：**

以QWidget为例来说明，QWidget是Widget框架中大部分UI组件的基类。
QWidget类拥有一些名字为xxxEvent的虚函数,比如：
```
virtual void keyPressEvent(QKeyEvent *event)
virtual void keyReleaseEvent(QKeyEvent *event)
```
事件分发会在按键按下时调用QWidget的keyPressEvent函数，按键松开时调用QWidget的keyReleaseEvent函数。


#### **2.4 事件处理：**
有了上面的事件处理机制，我们就可以在自己的QWidget子类中，通过重载keyPressEvent、keyReleaseEvent等等事件处理函数，做一些自定义的事件处理。

除此之外：通过调用虚函数QObject::event()来处理事件, 比如监听某个按钮按下：
```
bool myWidget::event(QEvent *e)
{
    if (e->type() == QEvent::KeyPress) 
    {
        //将QEvent对象转换为真正的QKeyEvent对象
        QKeyEvent *keyEvent = static_cast<QKeyEvent *>(e);
        if (keyEvent->key() == Qt::Key_Tab) 
        {
            qDebug() << "You press tab.";
            return true;
        }
    }
    //按照原来的流程来进行事件的分发
    return QWidget::event(e);
}
```


#### **2.5 事件过滤：**
需要拦截某个组件发生的事件，让这个事件不再向其他组件进行传播，QObject有一个虚函数，原型如下
`virtual bool QObject::eventFilter ( QObject * watched, QEvent * event );`

Qt中的事件过滤器可以用于对某个对象的事件进行拦截和处理。事件过滤器是一个QObject对象，它可以安装到任何QObject派生类中，并且可以监听该对象的所有事件。当事件发生时，事件过滤器可以拦截并处理该事件，也可以将该事件转发给原始的事件接收者对象进行处理。 事件过滤器的处理方法如下：

1. 创建一个QObject派生类，实现其eventFilter()函数，该函数会在事件发生时被调用。

```
class MyEventFilter : public QObject
{
    Q_OBJECT
public:
    explicit MyEventFilter(QObject *parent = nullptr);
protected:
    bool eventFilter(QObject *watched, QEvent *event) override;
};
```

2.在需要监听的对象中，调用installEventFilter()函数安装事件过滤器。

```
MyEventFilter *eventFilter = new MyEventFilter;
QLabel *label = new QLabel("Hello, World!");
label->installEventFilter(eventFilter);
```

3.在eventFilter()函数中处理事件，可以通过判断事件类型和事件源来对事件进行不同的处理。

```
bool MyEventFilter::eventFilter(QObject *watched, QEvent *event)
{
    if (watched == label && event->type() == QEvent::MouseButtonPress) {
        QMouseEvent *mouseEvent = static_cast<QMouseEvent *>(event);
        // 处理鼠标事件
        return true;
    }
    return false;
}
```

需要注意的是，在eventFilter()函数中返回true表示该事件已经被处理，不再继续传递给事件接收者对象进行处理；返回false表示该事件仍然需要传递给事件接收者对象进行处理。 

#### **2.6 事件传递过程：**
![image](https://i.328888.xyz/2023/04/20/iGcOHa.png)

参考：https://zhuanlan.zhihu.com/p/72758194




### 3、知道QT事件机制有几种级别的事件过滤吗？能大致描述下吗？

根据对Qt事件机制的分析, 我们可以得到5种级别的事件过滤,处理办法. 以功能从弱到强, 排列如下:

1）重载特定事件处理函数.

最常见的事件处理办法就是重载象mousePressEvent(), keyPressEvent(), paintEvent() 这样的特定事件处理函数.

2）重载event()函数.

通过重载event()函数,我们可以在事件被特定的事件处理函数处理之前(象keyPressEvent())处理它. 比如, 当我们想改变tab键的默认动作时,一般要重载这个函数. 在处理一些不常见的事件(比如:LayoutDirectionChange)时,evnet()也很有用,因为这些函数没有相应的特定事件处理函数. 当我们重载event()函数时, 需要调用父类的event()函数来处理我们不需要处理或是不清楚如何处理的事件.

3） 在Qt对象上安装事件过滤器.

安装事件过滤器有两个步骤: (假设要用A来监视过滤B的事件)

首先调用B的installEventFilter( const QOject *obj ), 以A的指针作为参数. 这样所有发往B的事件都将先由A的eventFilter()处理.

然后, A要重载QObject::eventFilter()函数, 在eventFilter() 中书写对事件进行处理的代码.

4） 给QAppliction对象安装事件过滤器.

一旦我们给qApp(每个程序中唯一的QApplication对象)装上过滤器,那么所有的事件在发往任何其他的过滤器时,都要先经过当前这个 eventFilter(). 在debug的时候,这个办法就非常有用, 也常常被用来处理失效了的widget的鼠标事件,通常这些事件会被QApplication::notify()丢掉. ( 在QApplication::notify() 中, 是先调用qApp的过滤器, 再对事件进行分析, 以决定是否合并或丢弃)

5） 继承QApplication类,并重载notify()函数.

Qt 是用QApplication::notify()函数来分发事件的.想要在任何事件过滤器查看任何事件之前先得到这些事件,重载这个函数是唯一的办法. 通常来说事件过滤器更好用一些, 因为不需要去继承QApplication类. 而且可以给QApplication对象安装任意个数的事件。 


### 4、描述Windows下一个消息从触发到处理的整个路由过程

应用程序启动, 操作系统为程序创建一个对应的消息队列, 用户对创建进行操作, 产生一系列消息, 操作系统首先捕捉到这些消息, 将消息投递到对应的消息队列中, 在应用程序中对应一个消息循环 。

消息循环每次从消息队列中取出消息, 取出的消息如果是虚拟键消息, 会将其转换成标准消息, 将转换的消息再次投递到消息队列, 如果取出的是标准消息, 会将该消息发送给操作系统, 操作系统 会调用对应的窗口过程函数, 下窗口过程函数中对对用的消息进程处理.