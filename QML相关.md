### 什么是QML？

QML是语言的名称（就像C++，那是一些其他的语言......)

QML代表Qt Meta Language或Qt Modelling Language，是一种人机界面标记语言。

QtQuick是QML的一个工具包，允许用QML语言扩展图形界面（还有其他的QML工具包，有些是图形化的，如Sailfish Silica或BlackBerry Cascade，还有一些是非图形化的，如QBS，它是QMake/CMake/make的替代品...）。


### QWidget和QML的技术本质和使用上，有什么区别？


| Col1 | Col2 |
| --- | --- |
| QWidget | 基于C++开发，使用传统.ui布局，使用的是面向对象的思路，使用cpu渲染 |
| QML | 基于声明式技术的思路，结合JavaScript语法，基于GPU渲染 |



### QML鼠标与事件处理？ QML布局？ Loader 动态加载组件？

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

QML布局

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

Loader 动态加载组件

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
