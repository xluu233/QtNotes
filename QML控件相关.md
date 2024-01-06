## 一、基本元素（Basic Elements）

元素可以被分为可视化元素与非可视化元素。一个可视化元素（例如矩形框
Rectangle）有着几何形状并且可以在屏幕上显示。一个非可视化元素（例如计时
器Timer）提供了常用的功能，通常用于操作可视化元素。

### 1.1 基础元素对象（Item Element）

Item是基础元素对象，所有可视化元素都继承自Item,它自身不会有任何绘制操作，但是定义了所有可视化元素共有的属性：


| Group（分组） | Properties（属性） |
| --- | --- |
|Geometry（几何属性）|x,y（坐标）定义了元素左上角的位置，width，height（长和宽）定义元素的显示范围，z（堆叠次序）定义元素之间的重叠顺序。|
|Layout handling（布局操作）|anchors（锚定），包括左（left），右（right），上（top），下（bottom），水平与垂直居中（vertical center，horizontal center），与margins（间距）一起定义了元素与其它元素之间的位置关系。|
| Key handlikng（按键操作）| 附加属性key（按键）和keyNavigation（按键定位）属性来控制按键操作，处理输入焦点（focus）可用操作。|
| Transformation（转换）| 缩放（scale）和rotate（旋转）转换，通用的x,y,z属性列表转换（transform），旋转基点设置（transformOrigin）。
| Visual（可视化）| 不透明度（opacity）控制透明度，visible（是否可见）控制元素是否显示，clip（裁剪）用来限制元素边界的绘制，smooth（平滑）用来提高渲染质量。|
|State definition（状态定义）| states（状态列表属性）提供了元素当前所支持的状态列表，当前属性的改变也可以使用transitions（转变）属性列表来定义状态转变动画。|

### 1.2 组件（Compontents）

这些组件继承自Item,可以实现不同的基础功能，比如文本和图像的显示，可以通过其属性对其调整。

[Qt6.4( All QML Types)](https://doc.qt.io/archives/qt-6.4/qmltypes.html)

> 以下列举了常用的一些可视化组件，点击超链接可以查看其官方文档

| 组件 | 详情 |
|---|---|
| Rectangle（矩形框） | Rectangle（矩形框）是基本元素对象的一个扩展，增加了一个颜色来填充它。它还支持边界的定义，使用border.color（边界颜色），border.width（边界宽度）来自定义边界。你可以使用radius（半径）属性来创建一个圆角矩形。 |
| Text（文本） | 显示字体的元素 |
| Image（图像）  | 一个图像元素（Image Element）能够显示不同格式的图像（例如PNG,JPG,GIF,BMP） |
| Button | 点击按钮，类似的还有RoundButton（圆形按钮） ToolButton（状态栏按钮） |
| TextInput | 文本输入组件 |
| TextField（文本框） | 用于输入和显示单行文本。 |
| TextArea（多行文本框） |用于输入和显示多行文本。 |
| ComboBox（下拉框）|用于选择一个选项的下拉框。 |
| Slider（滑动条） |用于选择一个范围内的数值的一个滑动条。 |
| ListView |一个高性能的组件，用于显示大量数据的列表。 |
| GridView | 用于显示大量数据的网格布局。|
| TabView | 用于显示多个标签页的组件。|
| Repeater| 可以重复地创建其他QML元素。|
| Container|容器，用于组织和布局其他控件。
| DelayButton|延迟按钮，可以设置点击后的延迟响应。
| Dial|旋钮，可以用于选择一个范围内的值。
| DialogButtonBox|对话框按钮，常用于对话框中的确定和取消按钮。
| Dialog|对话框，用于显示一些临时的、重要的信息。
| Drawer|抽屉，可以从屏幕边缘滑出，用于显示菜单或者其他内容。
| Menu 和 MenuBar|菜单和菜单栏，用于显示一系列的选项。
| PageIndicator|页面指示器，用于指示当前页面和总页面数。
| RangeSlider|范围滑动条，可以选择一个范围内的值。
| ScrollView|滚动视图，可以显示大于其自身大小的内容。
| SpinBox|旋转框，可以通过点击按钮来增加或减少值。
| StackView|堆栈视图，可以管理一堆页面，如同堆栈一样，后进先出。
| SwipeView|滑动视图，可以通过滑动来切换页面。
| Switch|开关，用于表示开/关两种状态。
| TabBar|标签栏，用于在多个页面之间切换。
| ToolBar|工具栏，用于显示一系列的工具按钮。
| ToolTip|工具提示，当鼠标悬停在某个元素上时，会显示相关的提示信息。
| Tumbler|滚筒，可以滚动选择值，常用于日期和时间的选择。


#### 非可视化组件

非可视化组件通常用来操作可视化组件，以下是一些常用的非可视化组件：

| 组件 | 详情 |
|---|---|
| MouseArea  | 鼠标区域元素，这是一个矩形的非可视化元素对象，你可以通过它来捕捉鼠标事件。当用户与可视化端口交互时，mouseArea通常被用来与可视化元素对象一起执行命令。|
| FocusScope | 焦点区域，用于控制QML元素焦点的组件，它可以用来管理和限制焦点的传递和接收
| Component | Component是由Qt框架或开发者封装好的、只暴露了必要接口的QML类型，可以重复利用的元素。一个Component就像一个黑盒子，它通过属性、信号、函数和外部世界交互。
| Timer | Timer是一个计时器，可以在特定的时间间隔后触发一个操作。
| Connections | Connections用于创建和管理QML对象之间的连接。
| MediaPlayer | MediaPlayer组件是将媒体源连接到一个或多个输出通道的非可视项。
<!--| PropertyChanges | PropertyChanges用于在状态改变时改变一个或多个属性的值。-->
<!--| SequentialAnimation | SequentialAnimation可以按顺序运行一系列的动画。-->
<!--| ParallelAnimation | ParallelAnimation可以同时运行一系列的动画。-->
<!--| NumberAnimation | NumberAnimation可以创建一个数字的动画。-->
<!--| ColorAnimation | ColorAnimation可以创建一个颜色的动画。-->
<!--| RotationAnimation | RotationAnimation可以创建一个旋转的动画。-->



### 1.3 布局（Layout）

| 组件 | 详情 |
| --- | --- |
| Column | 行布局，从上到下 |
| Row | 列布局，从左到右 |
| Grid | 栅格布局，通过设置rows（行数）和columns（列数）将子对象排列在一个栅格中 |
| Flow | 流式布局，也可以称瀑布局部，就是根据控件的大小，可以从左到右或者从上到下自动排列。|

### 1.4 锚(anchors)

QML使用anchors（锚）对元素进行布局。anchoring（锚定）是基础元素对象的基
本属性，可以被所有的可视化QML元素使用。一个anchors（锚）就像一个协议，
并且比几何变化更加强大。**Anchors（锚）是相对关系的表达式，你通常需要与其
它元素搭配使用。**


通过设置anchors的属性，来控制控件的具体位置。**一个元素有6条锚定线（top顶，bottom底，left左，right右，horizontalCenter水平中，verticalCenter垂直中）**

### 总结

![基本元素](https://github.com/xluu233/QtNotes/blob/main/src/qml-view-1.png)
![qml元素属性](https://github.com/xluu233/QtNotes/blob/main/src/qml-view-2.png)


参考：
- [QML基础(三)-(Model-View-Delegate)](http://www.cleartechfei.com/2020/07/qml%e5%9f%ba%e7%a1%80%e4%b8%89-model-view-delegate/)
- [QML基础(四)-画布元素](http://www.cleartechfei.com/2020/07/qml%e5%9f%ba%e7%a1%80%e5%9b%9b-%e7%94%bb%e5%b8%83%e5%85%83%e7%b4%a0/)
- [QML基础(五)-粒子模拟](http://www.cleartechfei.com/2020/07/qml%e5%9f%ba%e7%a1%80%e5%85%ad-%e7%b2%92%e5%ad%90%e6%a8%a1%e6%8b%9f/)

## 二、动画

**动画被用于属性的改变。一个动画定义了属性值改变的曲线，将一个属性值变化从
一个值过渡到另一个值。动画是由一连串的目标属性活动定义的，平缓的曲线算法
能够引发一个定义时间内属性的持续变化。所有在QtQuick中的动画都由同一个计
时器来控制，因此它们始终都保持同步，这也提高了动画的性能和显示效果。**

动画控制了属性的改变，也就是值的插入。这是一个基本的概念，QML是基于元
素，属性与脚本的。每一个元素都提供了许多的属性，每一个属性都在等待使用动
画。在这本书中你将会看到这是一个壮阔的场景，你会发现你自己在看一些动画时
欣赏它们的美丽并且肯定自己的创造性想法。然后请记住：动画控制了属性的改
变，每个元素都有大量的属性供你任意使用。


### 2.1 动画元素（Animation Elements）
有几种类型的动画，每一种都在特定情况下都有最佳的效果，下面列出了一些常用
的动画：

- PropertyAnimation（属性动画）- 使用属性值改变播放的动画
- NumberAnimation（数字动画）- 使用数字改变播放的动画
- ColorAnimation（颜色动画）- 使用颜色改变播放的动画
- RotationAnimation（旋转动画）- 使用旋转改变播放的动画

除了上面这些基本和通常使用的动画元素，QtQuick还提供了一切特殊场景下使用
的动画：

- PauseAnimation（停止动画）- 运行暂停一个动画
- SequentialAnimation（顺序动画）- 允许动画有序播放
- ParallelAnimation（并行动画）- 允许动画同时播放
- AnchorAnimation（锚定动画）- 使用锚定改变播放的动画
- ParentAnimation（父元素动画）- 使用父对象改变播放的动画
- SmotthedAnimation（平滑动画）- 跟踪一个平滑值播放的动画
- SpringAnimation（弹簧动画）- 跟踪一个弹簧变换的值播放的动画
- PathAnimation（路径动画）- 跟踪一个元素对象的路径的动画
- Vector3dAnimation（3D容器动画）- 使用QVector3d值改变播放的动画

当使用更加复杂的动画时，我们可能需要在播放一个动画时中改变一个属性或者运行一个脚本。对于这个问题，QtQuick提供了一个动作元素：
- PropertyAction（属性动作）- 在播放动画时改变属性
- ScriptAction（脚本动作）- 在播放动画时运行脚本

### 2.2 缓冲曲线（Easing Curves）

属性值的改变能够通过一个动画来控制，缓冲曲线影响了一个属性值改变的插值算法。一个动画默认的缓冲曲线类型是Easing.Linear
插值曲线的类型有:Linear、InQuad、OutQuad、InOutQuad、InCubic、InSine、InCirc、InElastic、InBack、InBounce

```
NumberAnimation{
    id:anim
    target: startrun
    properties: "y"
    //修改插值曲线的方法
    easing.type: "InQuad"
    from:200
    to:40
    duration: 4000
}
```

### 2.3 动画分组（Grouped Animations）
同时操作多个属性的动画，就要把多个单独的动画组合到一起进行运行。这就形成了动画分组。有两种方法来分组:平行和连续。

- SequentialAnimation(连续动画) - 播放完一个动画之后播放另一个动画
- ParallelAnimation(平行动画) - 多个动画同时播放


### 2.4 状态和过渡（States and Transitions）

在QML中，使用State元素来定义状态：
```
Item {
    id: root
    states: [
        State {
            name: "go"
            PropertyChanges { ... }
        },
        State {
            name: "stop"
            PropertyChanges { ... }
        }
    ]
}
```
上面设置了两种状态，在合适的时候可以设置其state属性来改变状态
```
Button {
    id: goButton
    ...
    onClicked: root.state = "go"
}
```

**我们期望从状态“go”到“stop”转换时实现一个颜色改变的动画。使用
属性的from:和to:来定义状态改变的指定过渡**

```
transitions: [
    Transition {
        from: "stop"; to: "go"
        ColorAnimation { target: light1; properties: "color"; duration:
        ColorAnimation { target: light2; properties: "color"; duration:
    }
]
```


## MVD模型

- [QT中Model-View-Delegate委托代理机制用法介绍](https://blog.csdn.net/yang1fei2/article/details/125625567?spm=1001.2014.3001.5502)
- [在QML委托代理机制中使用C++数据模型](http://www.cleartechfei.com/2022/08/%e5%9c%a8qml%e5%a7%94%e6%89%98%e4%bb%a3%e7%90%86%e6%9c%ba%e5%88%b6%e4%b8%ad%e4%bd%bf%e7%94%a8c%e6%95%b0%e6%8d%ae%e6%a8%a1%e5%9e%8b/)