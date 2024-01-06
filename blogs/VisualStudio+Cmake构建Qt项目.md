## VisualStudio创建QT项目

这个没啥讲的，配置好Qt项目之后，创建的时候选择CMAKE方式就行，一般直接运行就可以跑了，如果出错看下是否配置正确。


## Cmake管理Qt项目

 CMake是一个跨平台的自动化构建系统，它使用名为CMakeLists.txt的文本文件来描述项目的构建过程。这个文件可以通过编写简单的指令来创建编译过程，并且可以在不同的平台上使用相同的CMakeLists.txt文件来构建项目。
 
### cmake-simple

我们先创建一个简单的项目，来看cmake是如何构建项目的，首先创建test目录，在其下面创建CmakeLists.txt文件和main.cpp文件

项目结构如下：
```
-test
    - CmakeLists.txt
    - main.cpp
```

CmakeLists.txt如下：
```
cmake_minimum_required(VERSION 3.26)
project(test)

set(CMAKE_CXX_STANDARD 17)

add_executable(test main.cpp)
```
main.cpp：
```
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}

```
在其目录下执行`cmake -B build `就会开始编译，在其目录下生产build目录和编译文件

关于具体的编译细节，参考：[使用CMake的命令行小技巧](https://zhuanlan.zhihu.com/p/631112052)

关于Cmake系统性学习，可以参考: [CMake菜谱（CMake Cookbook中文版）](https://www.bookstack.cn/read/CMake-Cookbook/README.md)


### Qt & cmake

如下是我创建一个完整qt项目的cmake，我们解析下具体信息：
```
#cmake最低版本要求
cmake_minimum_required(VERSION 3.16)

#项目名称
project(QtWidgetsApplication2 LANGUAGES CXX)

include(qt.cmake)

set(CMAKE_CXX_STANDARD 17)  #使用C++17标准
set(CMAKE_CXX_STANDARD_REQUIRED ON) #CMAKE_CXX_STANDARD_REQUIRED设置成True表示必须使用指定的C++标准版本进行编译，否则将会出现编译错误


#设置模块的cMake路径
#set(CAMKE_PREFIX_PATH "D:/xxx/lib/cmake")

#set(CMAKE_CXX_FLAGS "-fPIC")
#set(CMAKE_BUILD_TYPE "Debug")
 
# 增加子目录ChartWidget的源码到变量SOURCES
#aux_source_directory(ChartWidget SOURCES)
 
# 增加子目录Model的源码到变量SOURCES
#aux_source_directory(Model SOURCES)
 
# 设置QT安装路径
#set(CMAKE_PREFIX_PATH "/usr/local/Trolltech/Qt5.10.1/5.10.1/gcc_64")
 
#开启MOC支持
#set(CMAKE_AUTOMOC ON)

#开启RCC支持
#set(CMAKE_AUTORCC ON)

#开启UIC支持
#set(CMAKE_AUTOUIC ON)

#设置Qt安装目录
#set(CMAKE_PREFIX_PATH "Qt to path") #bin lib include目录层

#查找Qt相关包
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core)
find_package(Qt${QT_VERSION_MAJOR}
        COMPONENTS
        Core
        Gui
        Widgets
)


qt_standard_project_setup()

#添加所有cpp文件
set(PROJECT_SOURCES
    main.cpp
    QtWidgetsApplication2.cpp
)

#添加所有头文件
set(PROJECT_HEADER
    QtWidgetsApplication2.h
)

#添加所有资源文件
set(PROJECT_SRC
    QtWidgetsApplication2.ui
)

#将所有可编译文件添加到QtWidgetsApplication2可执行文件
qt_add_executable(QtWidgetsApplication2 ${PROJECT_SOURCES}  ${PROJECT_HEADER} ${PROJECT_SRC} )
add_compile_options(-Werror)
#add_compile_options(-O3)

set_target_properties(QtWidgetsApplication2
        PROPERTIES
        WIN32_EXECUTABLE TRUE
)

#指定版本信息
set(QT_VERSION_MAJOR 5)
set(QT_VERSION_MINOR 15)
set(QT_VERSION_PATCH 0)
set(QT_VERSION "${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}.${QT_VERSION_PATCH}")

#指定编译信息
project(QtWidgetsApplication2
    VERSION ${QT_VERSION}
    DESCRIPTION "test"
    LANGUAGES CXX
)

#链接库文件
target_link_libraries(QtWidgetsApplication2
    PUBLIC
        Qt::Core
        Qt::Gui
        Qt::Widgets
)

```


一个典型的CMakeLists.txt文件通常包含以下内容：

**项目的名称和版本号**
```
#cmake最低版本要求
cmake_minimum_required(VERSION 3.16)

#项目名称
project(QtWidgetsApplication2 LANGUAGES CXX)
```

**设置项目编译选项**

```
set(CMAKE_CXX_STANDARD 17)  #使用C++17标准
set(CMAKE_CXX_STANDARD_REQUIRED ON) #CMAKE_CXX_STANDARD_REQUIRED设置成True表示必须使用指定的C++标准版本进行编译，否则将会出现编译错误
```


**qt.cmake**
这是一个子cmake文件，其内容如下：主要是对不同平台不同版本的兼容处理
```
if(QT_VERSION VERSION_LESS 6.3)
    macro(qt_standard_project_setup)
        set(CMAKE_AUTOMOC ON)
        set(CMAKE_AUTORCC ON)
        set(CMAKE_AUTOUIC ON)
    endmacro()
endif()

if(QT_VERSION VERSION_LESS 6.0)
    macro(qt_add_executable name)
         if(ANDROID)
            add_library(name SHARED ${ARGN})
        else()
            add_executable(${ARGV})
        endif()
    endmacro()
endif()
```

**添加包含目录和链接库目录**
```
include_directories(include)
link_directories(lib)
```

**添加Qt模块**
```
#查找Qt相关包
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core)
find_package(Qt${QT_VERSION_MAJOR}
        COMPONENTS
        Core
        Gui
        Widgets
)

```

**添加源文件**
```
#添加所有cpp文件
set(PROJECT_SOURCES
    main.cpp
    QtWidgetsApplication2.cpp
)

#添加所有头文件
set(PROJECT_HEADER
    QtWidgetsApplication2.h
)

#添加所有资源文件
set(PROJECT_SRC
    QtWidgetsApplication2.ui
)

#将所有可编译文件添加到QtWidgetsApplication2可执行文件
qt_add_executable(QtWidgetsApplication2 ${PROJECT_SOURCES}  ${PROJECT_HEADER} ${PROJECT_SRC} )
```

**开启编译优化**
```
add_compile_options(-Werror)
#add_compile_options(-O3)

```

**指定编译信息**
```
#指定版本信息
set(QT_VERSION_MAJOR 5)
set(QT_VERSION_MINOR 15)
set(QT_VERSION_PATCH 0)
set(QT_VERSION "${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}.${QT_VERSION_PATCH}")

#指定编译信息
project(QtWidgetsApplication2
    VERSION ${QT_VERSION}
    DESCRIPTION "test"
    LANGUAGES CXX
)
```

**链接库文件**
```
set_target_properties(QtWidgetsApplication2
        PROPERTIES
        WIN32_EXECUTABLE TRUE
)

#链接库文件
target_link_libraries(QtWidgetsApplication2
    PUBLIC
        Qt::Core
        Qt::Gui
        Qt::Widgets
)

```

参考：
- [QT:使用VS搭建qt环境](https://blog.csdn.net/qq_59470001/article/details/130799429)