
## 多线程

### 如何使用多线程

**方法一：**
1. 创建一个类从QThread类派生
1. 在子线程类中重写 run 函数, 将处理操作写入该函数中 
1. 在主线程中创建子线程对象, 启动子线程, 调用start()函数

```
class MyThread : public QThread
{
    Q_OBJECT
public:
    void run() override
    {
        // 在新线程中执行的代码
    }
};

MyThread thread;
thread.start();
```

**方法二：**
1. 将业务处理抽象成一个业务类, 在该类中创建一个业务处理函数
1. 在主线程中创建一QThread类对象 
1. 在主线程中创建一个业务类对象 
1. 将业务类对象移动到子线程中 
1. 在主线程中启动子线程 
1. 通过信号槽的方式, 执行业务类中的业务处理函数
2. 

```
class MyObject : public QObject
{
    Q_OBJECT
public slots:
    void doWork()
    {
        // 在新线程中执行的代码
    }
};

QThread thread;
MyObject *obj = new MyObject();
obj->moveToThread(&thread);

QObject::connect(&thread, &QThread::started, obj, &MyObject::doWork);
thread.start();
```

**多线程使用注意事项:**
1. 业务对象, 构造的时候不能指定父对象
2. 子线程中不能处理ui窗口(ui相关的类)
3. 子线程中只能处理一些数据相关的操作, 不能涉及窗口


### 如何安全结束线程

**QThread结束的几种方式：**
- QThread::terminate() 不安全
- QThread::exit(int returnCode = 0) 正确使用才安全
- QThread::quit() 正确使用才安全


**安全的退出方式: **
使用QThread的finished信号绑定QObject的deleteLater函数实现资源自动释放，也可以绑定一个函数。退出时只需要调用quit()函数即可，如果绑定的是函数，则可以在适当的位置释放thread对象和Object对象

**创建：**
```
	TestObject* object = new TestObject;
	QThread* thread = new QThread;
	object->moveToThread(thread );
	connect(thread,&QThread::finished,object,&TestObject::deleteLater);	// 退出后释放TestObject对象资源
	connect(thread,&QThread::finished,thread,&QThread::deleteLater);	// 退出后释放QThread对象资源
	thread->start();
```
**退出:**
```
	thread->quit();	// 也可以使用thread->exit(0);
	thread->wait(); // wait函数是个阻塞的接口，意思是线程必须真的退出了，才会执行wait之后的语句，否则将会一直阻塞在这里，如果在界面上使用，需要保证线程中代码的合理性。
	thread = nullptr;
	object = nullptr;
```

### Qt线程同步的方法

1. **互斥量（QMutex）**

```
QMutex m_Mutex;
m_Mutex.lock();
...
m_Mutex.unlock();
```

2. **互斥锁（QMutexLocker）**

`QMutexLocker mutexLocker(&m_Mutex);` 
    
从声明处开始（在构造函数中加锁），出了作用域自动解锁（在析构函数中解锁）。

3. **等待条件（QWaitCondition）**

```
QWaitCondtion m_WaitCondition; 
m_WaitConditon.wait(&m_muxtex, time);                 
m_WaitCondition.wakeAll();
```

4. **QReadWriteLock类**

- 一个线程试图对一个加了读锁的互斥量进行上读锁，允许；
- 一个线程试图对一个加了读锁的互斥量进行上写锁，阻塞；
- 一个线程试图对一个加了写锁的互斥量进行上读锁，阻塞；
- 一个线程试图对一个加了写锁的互斥量进行上写锁，阻塞。

读写锁比较适用的情况是：需要多次对共享的数据进行读操作的阅读线程。

QReadWriterLock 与QMutex相似，除了它对"read","write"访问进行区别对待。它使得多个读者可以共时访问数据。使用QReadWriteLock而不是QMutex，可以使得多线程程序更具有并发性。

5. **信号量QSemaphore**

但是还有些互斥量（资源）的数量并不止一个，比如一个电脑安装了2个打印机，我已经申请了一个，但是我不能霸占这两个，你来访问的时候如果发现还有空闲的仍然可以申请到的。于是这个互斥量可以分为两部分，已使用和未使用。

6. **QReadLocker便利类和QWriteLocker便利类对QReadWriteLock进行加解锁**

### QThread线程通信

可以基于信号槽机制通信：https://www.cnblogs.com/findumars/p/4973139.html

### Qt的多线程，哪些是只有Qthread能实现，QtConcurrent办不到的？
1、QThread可以使用信号和槽机制，而QtConcurrent不支持。
2、QThread可以设置线程优先级，而QtConcurrent不支持。
3、QThread可以实现跨平台多线程，而QtConcurrent只能在支持C++11的平台上实现。
4、QThread可以实现更复杂的多线程任务，而QtConcurrent只能实现简单的多线程任务。



### UI线程
UI线程是Android应用程序中的主线程，它负责绘制UI界面，处理用户交互，以及调度其他相关任务。如果UI线程被阻塞，用户界面将会停止响应，甚至可能会出现应用程序崩溃的情况。 

在Qt开发中也应该避免在主线程执行耗时操作。


### Qt线程池

> c++标准库中没有提供线程池，只有thread，但是Qt中提供了线程池工具。

Qt中提供的线程池主要包含两个类：**QThreadPool、QRunable**

**QThreadPool：**
此类为Qt提供的线程池函数，使用此类只需要配置线程池的最大线程数量、线程长时间不使用的过期时间等参数，不需要进行QThread相关的操作。此类有两种使用方式：全局线程池和局部线程池

- 全局线程池，QThreadPool提供了一个静态函数globalInstance()，使用此方法可获取一个当前进程的全局线程池，可在多个类中共同使用一个线程池。
- 局部线程池，和常规类的使用相同，可以创建QThreadPool对象的方式建立一个局部线程池，并由当前类维护，可保证此线程池仅供当前类应用

**QThreadPool类常用API：**
```
int activeThreadCount() const //当前的活动线程数量

void clear()//清除所有当前排队但未开始运行的任务

int expiryTimeout() const//线程长时间未使用将会自动退出节约资源，此函数返回等待时间

int maxThreadCount() const//线程池可维护的最大线程数量

void releaseThread()//释放被保留的线程

void reserveThread()//保留线程，此线程将不会占用最大线程数量，从而可能会引起当前活动线程数量大于最大线程数量的情况

void setExpiryTimeout(int expiryTimeout)//设置线程回收的等待时间

void setMaxThreadCount(int maxThreadCount)//设置最大线程数量

void setStackSize(uint stackSize)//此属性包含线程池工作线程的堆栈大小。

uint stackSize() const//堆大小

void start(QRunnable *runnable, int priority = 0)//加入一个运算到队列，注意start不一定立刻启动，只是插入到队列，排到了才会开始运行。需要传入QRunnable ，后续介绍

bool tryStart(QRunnable *runnable)//尝试启动一个

bool tryTake(QRunnable *runnable)//删除队列中的一个QRunnable，若当前QRunnable 未启动则返回成功，正在运行则返回失败

bool waitForDone(int?<i>msecs</i>?=?-1)//等待所有线程运行结束并退出，参数为等待时间-1表示一直等待到最后一个线程退出

```

**QRunable：**
QRunable类在Qt中是所有可运行对象的基类，代表了由run()函数表示的一个任务或一段要执行的代码，我们可以将QRunable理解为线程池中的任务；

QRunable类有run()、autoDelete()、setAutoDelete()这三个关键函数，如何autoDelete()设为true，QThreadPool会在run()运行结束后自动删除该对象。


**线程池实践：**

1、创建自定义QRunable对象
```
#ifndef MYRUNABLE_H
#define MYRUNABLE_H

#include <QObject>
#include <QRunnable>
#include <QDebug>
#include <QThread>

class MyRunable : public QObject, public QRunnable
{
    Q_OBJECT
public:
    explicit MyRunable(QObject *parent = nullptr);
    ~MyRunable();
protected:
    void run();
};

#endif // MYRUNABLE_H
```
```
#include "myrunable.h"

MyRunable::MyRunable(QObject *parent) : QObject(parent)
{
}

MyRunable::~MyRunable()
{
    qDebug()<<"delete Task";
}

void MyRunable::run()
{
    //在这里执行业务逻辑
    int i = 10;
    while(i--)
    {
        qDebug() << "线程id："<< QThread::currentThreadId()<< QString(":剩余%1").arg(i);
        QThread::sleep(1);
    }
}

```

2、调用：

```
    QThreadPool::globalInstance()->setMaxThreadCount(10);   //设置线程池最大线程数量

    MyRunable* task = new MyRunable();
    task->setAutoDelete(true);          //autoDelete属性默认为true   QThreadPool会在run（）函数运行结束后，自动删除了MyTask对象
    QThreadPool::globalInstance()->start(task);             //任务放进线程池
    QThread::sleep(1);

    MyRunable* task1 = new MyRunable();
    task1->setAutoDelete(true);
    QThreadPool::globalInstance()->start(task1);

    MyRunable* task2 = new MyRunable();
    task2->setAutoDelete(true);
    QThreadPool::globalInstance()->start(task2);

    QThreadPool::globalInstance()->waitForDone();           //等待任务结束
    qDebug() << "end";

```