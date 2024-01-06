
### 自定义控件

#### 1、自定义控件流程
1. 继承需要自定义的控件类，如QWidget, QPushButton；
1. 从外观设计上：QSS、继承绘制函数重绘、继承QStyle相关类重绘、组合拼装等等；
1. 从功能行为上：重写事件函数、添加或者修改信号和槽等等。 


#### 2、QSS平时使用的多吗？能举几个例子吗？

1. 将QSS统一写在一个文件中，通过程序给主窗口加载
2. 写成一个字符串中，通过程序给主窗口加载
3. 需要使用的地方，写一个字符串，加载给对象
4. QT Designer中填写

#### 3、使用样式表要注意的点
父控件采用样式表设置属性后，该属性会传递到其子控件上，除非子控件使用同样的方法修改属性。如：利用样式表设置父控件最小高度为x，则子控件的最小高度也为x，即使用setFixHeight()修改也无法消除，只能通过样式表重新设置子控件的高度才有效。因此一般只对子控件使用样式设置。 


### QFrame与QWidget的区别

QFrame 和 QWidget 都是 Qt 中的 GUI 组件，但是它们有一些区别：

- 继承关系：QFrame 继承自 QWidget，所以 QFrame 具有 QWidget 的所有功能。
- 功能：QFrame 提供了一个简单的框架，可以作为其他控件的容器。它还可以用来绘制简单的图形，如线条。QWidget 没有这样的功能，但是提供了基础的 GUI 组件功能，如设置尺寸和位置等。
- 外观：QFrame 可以有边框和背景颜色，因此外观更加丰富。QWidget 只有背景颜色，没有边框。
- 通常，当需要一个简单的框架时，使用 QFrame，当需要基础的 GUI 组件功能时，使用 QWidget。 


### show()和exec()的区别

在Qt中，show和exec都是用来显示对话框的方法，它们的主要区别在于它们的运行机制和返回值：

- **show显示非模态窗口（不影响用户对其他窗口操作）** show方法是在当前线程中显示对话框并立即返回，该方法不会阻塞当前线程，因此当对话框显示后，程序会继续执行后续代码。如果在对话框显示期间需要执行一些其他操作，可以在对话框关闭事件中处理，或者使用Qt的事件循环机制（如QEventLoop）来阻塞程序执行。
- **exec显示模态窗口，在当前线程中显示对话框，并且阻塞当前线程，直到用户关闭对话框为止。** 在执行exec方法后，程序会进入一个事件循环（QEventLoop），直到对话框关闭事件被触发。因此，如果需要在对话框关闭之前执行一些其他操作，需要在对话框关闭事件之前处理。
- **open半模态（阻塞其他窗口响应，但不影响后续代码执行）** 

需要注意的是，show方法和exec方法都可以用来显示对话框，但是它们的使用场景不同。show方法通常用于显示模态对话框和非模态对话框，而exec方法通常用于显示模态对话框。如果需要在对话框显示期间执行一些其他操作，建议使用show方法，如果需要等待用户关闭对话框后再执行其他操作，建议使用exec方法。


### QMainForm是从哪里派生的？
`QMainWindow::QWidget::QObject`


### Qwidget、Qobejct实现了哪些功能？

- **QObject：** QT中的基类，基于QObject实现了信号槽机制，所有的视图控件都继承于QObject, event事件模型，QObject提供了Qt中最基本的定时器

- **QWidget：** QWidget类是所有用户界面对象的基类，同时继承于QObject，Widget是用户界面的基本单元：它从窗口系统接收鼠标，键盘和其他事件，并在屏幕上绘制自己。每个Widget都是矩形的，它们按照Z-order进行排序。



### 常用的Qt布局有几种，如何自适应缩放？

1. 绝对布局：使用绝对位置和尺寸来定位和调整控件的大小；
2. 盒子布局：使用排列和填充来定位和调整控件的大小；
3. 栅格布局：使用表格排列和调整控件的大小；
4. 流式布局：使用流动排列和调整控件的大小；
5. 堆栈布局：使用堆栈排列和调整控件的大小。

Qt支持自适应缩放，可以使用Qt的布局管理器来实现。例如，可以使用QGridLayout管理器来控制窗口的尺寸和位置，使其能够根据窗口大小的变化而自动调整控件的大小和位置。 



### Qt如何实现自定义按钮，使其在光标进入、按下、离开三种状态下显示不同的图片？

Qt可以通过派生QPushButton类并重载其鼠标事件函数来实现自定义按钮，具体步骤如下：

1. 定义一个新的类，继承自QPushButton。
2. 在类的构造函数中设置按钮的三种状态下的图片。
3. 重载鼠标进入事件(QEvent::Enter)、鼠标按下事件(QEvent::MouseButtonPress)和鼠标离开事件(QEvent::Leave)函数，分别在这三个事件中设置按钮的状态图片。 示例代码：

```
class MyButton : public QPushButton
{
public:
    MyButton(QWidget *parent = nullptr) : QPushButton(parent)
    {
        // 设置按钮的三种状态图片
        setStyleSheet("QPushButton {border-image: url(normal.png);}"
                      "QPushButton:hover {border-image: url(hover.png);}"
                      "QPushButton:pressed {border-image: url(pressed.png);}");
    }
protected:
    void enterEvent(QEvent *event) override
    {
        // 鼠标进入事件，设置按钮状态为hover
        QPushButton::enterEvent(event);
        setStyleSheet("QPushButton {border-image: url(normal.png);}"
                      "QPushButton:hover {border-image: url(hover.png);}"
                      "QPushButton:pressed {border-image: url(pressed.png);}");
    }
    void mousePressEvent(QMouseEvent *event) override
    {
        // 鼠标按下事件，设置按钮状态为pressed
        QPushButton::mousePressEvent(event);
        setStyleSheet("QPushButton {border-image: url(normal.png);}"
                      "QPushButton:hover {border-image: url(hover.png);}"
                      "QPushButton:pressed {border-image: url(pressed.png);}");
    }
    void leaveEvent(QEvent *event) override
    {
        // 鼠标离开事件，设置按钮状态为normal
        QPushButton::leaveEvent(event);
        setStyleSheet("QPushButton {border-image: url(normal.png);}"
                      "QPushButton:hover {border-image: url(hover.png);}"
                      "QPushButton:pressed {border-image: url(pressed.png);}");
    }
};
```

在这个示例代码中，我们派生了一个新的类MyButton，重载了它的鼠标进入、按下和离开事件函数，根据不同的事件类型，设置按钮的状态图片。当鼠标进入按钮区域时，按钮的样式会变成hover状态，当鼠标按下按钮时，按钮的样式会变成pressed状态，当鼠标离开按钮时，按钮的样式会变成normal状态。


### 常用的控件类


1. QLabel（标签控件）：用于显示文本或图像。
2. QPushButton（按钮控件）：用于接收用户的点击事件。
3. QLineEdit（单行编辑框控件）：用于接收用户输入的单行文本。
4. QTextEdit（多行编辑框控件）：用于接收用户输入的多行文本。
5. QComboBox（下拉框控件）：用于在预定义的选项中进行选择。
6. QCheckBox（复选框控件）：用于允许用户选择一个或多个选项。
7. QRadioButton（单选按钮控件）：用于允许用户从多个选项中选择一个。
8. QSpinBox/QDoubleSpinBox（数值输入框控件）：用于接收用户输入的数值。

这些控件是Qt中非常常用的控件，常常用于创建用户界面。需要根据实际需求进行选择和使用。 



### QLayout/QStackedWidget类/QSplitter类/QDockWidget类
在Qt中，QLayout类、QStackedWidget类、QSplitter类和QDockWidget类都是用于创建用户界面的类，它们的作用如下：

1. QLayout类 QLayout类是Qt中用于管理控件布局的类。它可以将控件按照一定的布局方式进行排列，如水平布局、垂直布局、网格布局等。QLayout类通常用于创建复杂的用户界面，可以有效地管理控件的位置和大小。例如：

```
QHBoxLayout *layout = new QHBoxLayout;
layout->addWidget(button1);
layout->addWidget(button2);
layout->addWidget(button3);
setLayout(layout);
```

2. QStackedWidget类 QStackedWidget类是一种堆栈容器控件，它可以将多个子控件按照堆栈的方式进行管理，只显示当前的子控件，其它子控件被隐藏。QStackedWidget类通常用于创建多页应用程序，例如多个窗口之间的切换。例如：

```
QStackedWidget *stack = new QStackedWidget;
stack->addWidget(page1);
stack->addWidget(page2);
stack->addWidget(page3);
stack->setCurrentIndex(0);
```

3. QSplitter类 QSplitter类是一种分割窗口控件，它可以将窗口分割成多个部分，每个部分可以包含一个或多个子控件。QSplitter类通常用于创建可调整大小的用户界面，可以让用户自由调整窗口的大小和位置。例如：

```
QSplitter *splitter = new QSplitter(Qt::Horizontal);
splitter->addWidget(widget1);
splitter->addWidget(widget2);
splitter->addWidget(widget3);
```

4. QDockWidget类 QDockWidget类是一种停靠窗口控件，它可以将窗口停靠在主窗口的边缘或浮动在主窗口上方。QDockWidget类通常用于创建多文档界面（MDI）应用程序，可以让用户自由调整窗口的停靠位置和大小。例如：

```
QDockWidget *dock = new QDockWidget;
dock->setWidget(widget);
dock->setWindowTitle("Dock Window");
addDockWidget(Qt::LeftDockWidgetArea, dock);
```

需要注意的是，QLayout类、QStackedWidget类、QSplitter类和QDockWidget类都是用于创建用户界面的类，需要根据实际需求进行选择和使用。 


### Qt绘制原理双缓冲机制？

Qt绘制原理中的双缓冲机制是指在绘制过程中使用两个缓冲区，一个用于绘制，一个用于显示，从而避免了绘制过程中的闪烁等问题。具体来说，双缓冲机制的实现过程如下：

1. 创建两个缓冲区，一个用于绘制，一个用于显示；
2. 在绘制过程中，将所有的绘制操作都先绘制到绘制缓冲区中；
3. 绘制完成后，将绘制缓冲区中的内容复制到显示缓冲区中；
4. 显示缓冲区中的内容会被显示在屏幕上。

这样，由于绘制操作是在绘制缓冲区中进行的，因此不会直接影响到显示缓冲区中的内容，从而避免了闪烁等问题。 在Qt中，双缓冲机制是由QPainter类和QWidget类共同实现的。QPainter类用于在绘制缓冲区中进行绘制操作，而QWidget类则在显示缓冲区中进行显示操作。具体来说，当QWidget类的paintEvent()函数被触发时，会创建一个QPainter对象，然后在其中调用绘制函数，将所有的绘制操作都绘制到绘制缓冲区中。绘制完成后，QPainter对象会自动将绘制缓冲区中的内容复制到显示缓冲区中，然后显示缓冲区中的内容会被显示在屏幕上。 需要注意的是，双缓冲机制虽然可以避免闪烁等问题，但是也会增加内存的消耗。因此，在实际应用中需要根据具体情况进行选择和使用。


### Graphics View图形视图框架结构？
Graphics View是Qt中用于显示和处理大量图形元素的框架，其主要包括以下几个重要的类和组件：

1. QGraphicsItem和QGraphicsScene：QGraphicsItem类是Graphics View框架中所有图形项的基类，它定义了所有图形项的共同属性和行为。QGraphicsScene类是Graphics View框架中的场景类，它管理着所有的图形项，并且提供了对它们进行操作的接口。
2. QGraphicsView：QGraphicsView类是Graphics View框架中的视图类，它用于在屏幕上显示QGraphicsScene中的内容，并且提供了对场景进行缩放、平移、旋转等操作的接口。
3. QGraphicsWidget：QGraphicsWidget类是Graphics View框架中的窗口类，它可以作为一个图形项添加到QGraphicsScene中，并且支持鼠标事件、键盘事件等交互操作。
4. QGraphicsProxyWidget：QGraphicsProxyWidget类是Graphics View框架中的窗口代理类，它可以将一个QWidget对象转换为一个图形项，然后添加到QGraphicsScene中。
5. QGraphicsItemGroup：QGraphicsItemGroup类是Graphics View框架中的图形项组类，它可以将多个图形项组合成一个整体，从而方便对它们进行操作。
6. QGraphicsEffect：QGraphicsEffect类是Graphics View框架中的特效类，它可以对QGraphicsItem进行图形效果的处理，例如模糊、阴影、发光等。
7. QGraphicsPixmapItem和QGraphicsTextItem：QGraphicsPixmapItem类是Graphics View框架中的图片项类，它可以将一个QPixmap对象显示在场景中。QGraphicsTextItem类是Graphics View框架中的文本项类，它可以将一个字符串显示在场景中。 以上是Graphics View框架的主要组件和类，它们共同构成了Graphics View框架的结构。通过这些组件和类的使用，我们可以方便地实现各种复杂的图形界面和图形效果。 


### QtChart (图表、曲线图、饼状图、柱形、拆线图等) ？
QtChart是Qt自带的图表库，用于绘制各种类型的图表，例如曲线图、饼状图、柱形图、折线图等。使用QtChart可以方便地将数据可视化，并支持用户交互和定制化设置。下面介绍QtChart中常用的几种图表类型及其使用方法。

> 这个代码实现比较麻烦，参考大佬的[图表Demo](https://gitee.com/feiyangqingyun/QWidgetDemo#37-%E7%AC%AC%E4%B8%89%E6%96%B9%E7%B1%BB-third)

