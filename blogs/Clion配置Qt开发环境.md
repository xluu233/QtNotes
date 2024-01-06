
## 1. Qt安装

### 1.1 Qt在线安装器
Qt从5.15开始不再提供本地安装包，只能在线下载
- 官方资源：https://download.qt.io/
- 官方在线下载器：https://download.qt.io/archive/qt/

### 1.2 国内镜像源
- 中国科学技术大学：http://mirrors.ustc.edu.cn/qtproject/
- 清华大学：https://mirrors.tuna.tsinghua.edu.cn/qt/
- 北京理工大学：http://mirror.bit.edu.cn/qtproject/
- 中国互联网络信息中心：https://mirrors.cnnic.cn/qt/

### 1.3 Fiddler替换源地址
安装的时候使用fiddler代理软件替换官方源地址, 下载过程中保持fiddler软件开启
> 如果遇到下载错误可以切回官方地址，报错的文件下载完后再切回国内源地址加速下载.

- China Shanghai Jiao Tong University

```
urlreplace download.qt.io mirrors.sjtug.sjtu.edu.cn/qt/

urlreplace mirrors.sjtug.sjtu.edu.cn/qt/ download.qt.io
```

- China Tsinghua University

```
urlreplace download.qt.io mirrors.tuna.tsinghua.edu.cn/qt/

urlreplace mirrors.tuna.tsinghua.edu.cn/qt/ download.qt.io 
```

- China Alibaba Cloud
```
urlreplace download.qt.io mirrors.ustc.edu.cn/qtproject

urlreplace mirrors.ustc.edu.cn/qtproject download.qt.io 
```



## 2. Clion创建Qt项目

在Clion中新建项目的时候会有Qt项目选项，Clion默认使用cmake方式编译qt项目

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99249d12e96a4405bdcb0e665d72bafc~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1199&h=974&s=103934&e=png&b=f6f7fa)

在创建的时候可以指定Qt中Cmake路径,否则默认创建是不带这个路径的，编译会报错，需要手动加上。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9de33078809047d590f871112cdfc26e~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1199&h=974&s=167719&e=png&b=f5f7fa)

手动配置Cmake前缀路径：

```
set(CMAKE_PREFIX_PATH "C:/App/Qt/Qt6/6.4.3/mingw_64")
```

## 3. Clion中的Qt环境配置

### 3.1 在工具链中配置MinGW_Qt编译链

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fb4f3abe4aaa4a0e803a5633500789a6~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1472&h=1091&s=151855&e=png&b=f5f6f9)

> 这里路径是Qt\Tools目录下的mingw

### 3.2 在Cmake配置中选择MinGW_Qt

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6af47ca16bc545a5a603f11a2eeb18e0~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1472&h=1091&s=198226&e=png&b=f4f6f9)


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35fb26c51c23443cb70190b2f4078197~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=947&h=325&s=36821&e=png&b=ffffff)
### 3.3 编译

新建项目输出hello world！

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d49aed5bdbf64c998517e20d1fa18e08~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=468&h=307&s=7869&e=png&b=f0f0f0" alt="" width="70%" />


## 4. 配置外部工具

将QtDesigner添加到Clion的外部工具中，这样就可以在Clion中使用QtDesigner打开.ui文件了。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ced056ef5d1c498da08b41f96deb0b25~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1472&h=1091&s=139591&e=png&b=f5f6f9)

参考路径：`C:\App\Qt\Qt6\6.4.3\mingw_64\bin\designer.exe` 以自己本地安装路径为准

### 4.1 新建.ui文件

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fbf5235525824a90b9ea2c9faf972f6d~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=815&h=683&s=64357&e=png&b=f2f5fa)

我们新建了一个MainWindow的窗口，继承自QMainWindow, 创建好后会自动添加到CmakeLists中，并弹出以下提示窗口：

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbb3bc3f99b9407095c119a8d117fcd8~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=437&h=941&s=36502&e=png&b=f6f7fa" alt="" width="40%" />


右键点击.ui文件，在ExternTools->QtDesigner中即可打开。我们新增一个按钮，并修改main.cpp

```
#include <QApplication>  
#include <QPushButton>  
#include <iostream>  
#include "mainwindow.h"  
  
  
int main(int argc, char *argv[])  
{  
    QApplication a(argc, argv);  
    MainWindow mainWindow;  
    mainWindow.show();  
    return QApplication::exec();  
}
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eee1cb8cb49645fdb3d2e44cb7301fcb~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1047&h=848&s=26956&e=png&b=f0f0f0)

就这样可以愉快的开发了~~