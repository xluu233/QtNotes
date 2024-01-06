### 什么是QML？

**QML（Qt Markup Language 或 Qt Meta Language 或 Qt Modeling Language）**

- **声明式编程**：是一种基于JavaScript的、声明式的编程语言，主要用于设计用户界面UI。在QML中，你可以运行有效的标准JavaScript功能，如条件运算符、数组、变量设置和循环。但同时也有一些限制不能实现完整的JS语法。

- **数据绑定和信号槽机制**：QML支持各种类型的数据绑定和信号槽机制，以实现高效和易于维护的代码。

- **Qt Quick**：QML是Qt Quick的一部分，Qt Quick是QML类型和功能的标准库，包括可视化类型、交互式类型、动画功能、模型和视图，特效等。

- **适用领域**：QML主要用于移动应用程序，注重于触控输入、流畅的动画（60帧/秒）和用户体验。

- **性能**：QML能够更好地利用显卡加速，在动画与高频刷新的场景表现优秀。硬件性能越好，GPU越强劲，QML的综合性能越强。



**QWidget和QML的技术本质和使用上，有什么区别？**

- QWidget： 基于C++开发，使用传统.ui布局，使用的是面向对象的思路，使用cpu渲染
- QML： 基于声明式技术的思路，结合JavaScript语法，基于GPU渲染


### C++代码与qml界面相互通信

c++代码与qml之间进行通信，主要有以下几种形式：
- qml访问c++代码
- c++访问qml方法
- qml与c++之间信号槽连接

**C++和QML相关的一些宏定义**

- **Q_OBJECT**：注册到qml中的c++类必须集成自public QObject
- **Q_INVOKABLE**：将c++类中的函数注册到qml
- **Q_PROPERTY**：将c++类中的成员注册到qml，并且重新定义成员的READ、WRITE方法，也可以定义SIGNAL方法监听成员的变化。
- **Q_ENUM**：注册enum枚举类型到qml

参考：
- [Qml与C++交互(通信)的几种方式](https://zhuanlan.zhihu.com/p/640147540)
- [从 C++ 定义 QML 类型](https://blog.csdn.net/kenfan1647/article/details/121345747)


### 如何新建界面、实现界面/控件切换
- 新建页面也有通过Qt.createComponent("xx.qml")实现
- 通过Loader实现控件切换
- 设置控件/页面的visible熟悉，来显示/影藏


参考：
- [QML 界面切换的几种方法（带示例代码）](https://www.cnblogs.com/linuxAndMcu/p/13566502.html)

---

### QWidget界面与嵌套qml界面相互嵌套



- [QWidgets 与 Qml 相互嵌入方法，以及其中的一些坑](https://blog.csdn.net/u011283226/article/details/117398629)



### QML鼠标与事件处理？

在 QML 中，可以通过鼠标与事件处理来响应用户的操作。常见的鼠标事件包括：

- 点击事件（MouseArea）
- 悬停事件（hover）
- 按下事件（pressed）
- 松开事件（released）
- 移动事件（MouseArea） 在 QML 中，可以使用 MouseArea 来处理鼠标事件。例如：

```
Rectangle {
    width: 100
    height: 100
    color: "blue"
    MouseArea {
        anchors.fill: parent
        onClicked: {
            console.log("Clicked!")
        }
    }
}
```

上面的代码创建了一个蓝色的矩形，并在其上添加了一个 MouseArea 来处理鼠标事件。当用户点击矩形时，会在控制台输出一条消息。

### QML布局

在 QML 中，可以使用各种布局来管理控件的位置和大小。常见的布局包括：

- ColumnLayout
- RowLayout
- GridLayout
- StackLayout 以 ColumnLayout 为例：

```
ColumnLayout {
    spacing: 10
    Rectangle {
        width: 100
        height: 50
        color: "red"
    }
    Rectangle {
        width: 100
        height: 50
        color: "green"
    }
    Rectangle {
        width: 100
        height: 50
        color: "blue"
    }
}
```

上面的代码创建了一个 ColumnLayout，并在其中添加了三个矩形。这些矩形会依次排列在垂直方向上，并且它们之间的间距为 10。

### Loader 动态加载组件

在 QML 中，可以使用 Loader 组件来动态加载其他组件。例如：

```
Loader {
    sourceComponent: Rectangle {
        width: 100
        height: 100
        color: "red"
    }
}
```

上面的代码创建了一个 Loader，并将其 sourceComponent 属性设置为一个红色的矩形。在运行时，Loader 会动态加载这个矩形，并显示在其内部。可以通过改变 sourceComponent 属性来动态加载不同的组件。 

### QML热更新实现方案