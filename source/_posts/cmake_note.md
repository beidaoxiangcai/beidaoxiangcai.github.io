---
title: CMake常用操作总结
date: 2022-03-08 10:31:27
tags: Cmake
index_img: /img/page/cmake.png
---

参考：  
[https://blog.csdn.net/comedate/article/details/108544656](https://blog.csdn.net/comedate/article/details/108544656)  
[https://blog.csdn.net/qq_37431083/article/details/125885308](https://blog.csdn.net/qq_37431083/article/details/125885308)
# 一、CMake介绍
- makefile: 源文件一个一个编译太麻烦，Makefile通过编写规则文件指定工程中哪些文件需要先编译，哪些需要后编译进行批处理编译；可以指定只编译新写出的代码，而不用全部重新编译，提高了开发效率。 
- CMake：是一个开源、跨平台的编译、测试和打包工具，它使用比较简单的语言描述编译、安装的过程，**自动输出**Makefile或者project文件。
  
  为什么不自己写Makefile：对于大型工程，Makefile很复杂，cmake可自动生成Makefile。

  跨平台编译，也叫交叉编译，可在win平台上开发，编译成linux平台可执行的文件。

  Project文件（即Object文件）: 由源文件通过编译过程产生，是一种中间代码文件，其文件格式在Windows下为 .obj 文件，UNIX下是 .o 文件。大量的Object文件组合在一起（该过程称为"链接"）组成了可执行文件。

# 二、CMakeLists.txt语法
### 内部变量
|变量名|	意义|
|---|---|
PROJECT_NAME|project名称，即使用PROJECT命令设置的名称
CMAKE_CXX_COMPILER	|指定C++编译器
CMAKE_CXX_FLAGS	|编译C++文件时的选项
CMAKE_CXX_STANDARD| C++标准
add_definitions|添加编译选项
EXECUTABLE_OUTPUT_PATH	|可执行文件的存放路径
LIBRARY_OUTPUT_PATH	|库文件的存放路径
CMAKE_BUILD_TYPE	|build 类型(Debug, Release, …)，CMAKE_BUILD_TYPE=Debug
BUILD_SHARED_LIBS	|指定编译成静态库还是动态库


1. 设置最低Cmake版本要求
``` cmake
cmake_minimum_required(VERSION 3.5)  
```
2. **project()**
- PROJECT_NAME: 必选——用来设置工程名，设置后，会把设置的值存储在CMAKE_PROJECT_NAME变量中
- VERSION：可选，工程版本号，有主版本号、次版本号、补丁版本号
- DESCRIPTION：工程简单的的描述
- HOMEPAGE_URL：工程主页url
- LANGUAGES：工程使用的语言

``` cmake
# 指定项目名和编程语言,默认为C和C++（cxx），LANGUAGE关键字可省略
project(cmaketest cxx)
# 跟在其他关键字后不可省略
project (camketest VERSION 10.2.1.3 LANGUAGES “CXX”)
```
3. message 打印消息  
   `message([FATAL_ERROR] “message to display” …)`
4. set 可设置普通变量、环境变量、缓存条目  
    `set (normal_var a) #普通变量`
5. 生成可执行程序  
   #同一个文件夹下多个源文件的编译  
   `add_executable (helloDemo demo.cxx demo_b.cxx)`     
6. 指定头文件搜索路径  
   `include_directories (${HELLO_SOURCE_DIR}/Hello)`
7. 查找当前目录下所有源文件名，并将其存储到	DIR_CURR中
    `aux_source_directory(. DIR_CURR)`
8. 添加链接库，指明可执行文件helloDemo需要连接一个名为 function 的链接库  
   `target_link_libraries(helloDemo function)`
# 三、cmake命令
|参数|含义|
|--|--|  
|-S|指定源文件根目录，必须包含一个CMakeLists.txt文件|  
|-B|指定构建目录，构建生成的中间文件和目标文件的生成路径|  
|-D|使用变量，格式为-D \<var>=\<value>，-D后面的空格**可省略**,变量在CMakeLists.txt中定义|
``` shell
# 指明使用当前目录作为源文件目录，其中包含CMakeLists.txt文件；
#使用build目录作为构建目录；
#设定变量CMAKE_BUILD_TYPE的值为Debug，变量AUTHOR的值为RealCoolEngineer
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug -DAUTHOR=RealCoolEngineer
```
# 四、CMake使用  
1. 生成Makefile文件
> cmake \`CMakeLists.txt所在路径`  //会在当前文件夹生成build文件    
1. 编译:进入build文件，里面会有Makefile文件，执行make，即会生成可执行文件
> make
1. 直接输入可执行文件文件名，执行。
# 五、项目CMakeList.txt实例
项目来源：[https://github.com/CandyConfident/HighPerformanceConcurrentServer](https://github.com/CandyConfident/HighPerformanceConcurrentServer)  

out-of-source build，与in-source build相对，即将编译输出文件与源文件放到不同目录中。此项目为out-of-source build。
### build.sh
``` shell
#!/bin/sh

set -x

SOURCE_DIR=`pwd`
BUILD_DIR=${BUILD_DIR:-../build}
BUILD_TYPE=${BUILD_TYPE:-Debug} #表示是debug版本，不是release版本
DEBUG_FLAG=${DEBUG_FLAG:-1}

mkdir -p $BUILD_DIR/$BUILD_TYPE \
    && cd $BUILD_DIR/$BUILD_TYPE \
    && cmake \
            -DCMAKE_BUILD_TYPE=$BUILD_TYPE \
            -DCMAKE_DEBUG_FLAG=$DEBUG_FLAG \
            $SOURCE_DIR \
    && make $*
```
### 总CMakeLists.txt
``` cmake
cmake_minimum_required(VERSION 3.16) #指定cmake最小版本
project(MyServer CXX) #指定项目名和程序语言,cxx=c++

set(CMAKE_CXX_STANDARD 20) #指定C++标准

if(CMAKE_DEBUG_FLAG) 
    set(CMAKE_CXX_FLAGS " ${CMAKE_CXX_FLAGS} -g")
endif()

message(STATUS "CMAKE_CXX_FLAGS = " ${CMAKE_CXX_FLAGS}) #打印消息

# 添加子目录,同时将子目录下的CMakeLists.txt文件执行
add_subdirectory(log/tests)
add_subdirectory(threadpool/tests)
add_subdirectory(timer/tests)
add_subdirectory(memory/tests)
add_subdirectory(net/tests)
```
### 子CMakeLists.txt
``` cmake
aux_source_directory(.. native_source) #将当前文件夹所有源文件名添加到上一级目录中native_source中
set(SRCS  #设置SRCS变量为多个值
    test_mem.cpp
    ../../log/pr.cpp
    ../../log/log.cpp
)
list(APPEND SRCS ${native_source})

set(INCS
    ../
    ../../log
)
include_directories(${INCS}) #指定头文件路径

add_executable(mem_test ${SRCS}) #将SRCS中源文件生成可执行文件mem_test
target_link_libraries(mem_test pthread) #添加链接库

list(REMOVE_ITEM SRCS test_mem.cpp)
list(APPEND SRCS test_buf.cpp)
add_executable(buf_test ${SRCS})
target_link_libraries(buf_test pthread)
```


  

