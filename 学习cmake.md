---
title: 学习cmake
tags: [cmake]
date: 2024-01-25 10:56:40
categories: 
  - c++
  - cmake
cover: https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/acg.gy_08.jpg
---
## 1.CMake概述

c++源文件通过预处理、编译、汇编和链接。可以通过makefile编写，然后再make。makefile 通常依赖于当前的编译平台，而且编写 makefile 的工作量比较大，解决依赖关系时也容易出错。
而 CMake 恰好能解决上述问题， 其允许开发者指定整个工程的编译流程，在根据编译平台，自动生成本地化的Makefile和工程文件，最后用户只需make编译即可，所以可以把CMake看成一款自动生成 Makefile的工具。

## 2.CMake的基本使用

CMake支持大写、小写、混合大小写的命令。

### 2.1注释

1. 注释行

```cmake
# 这是一个 CMakeLists.txt 文件
cmake_minimum_required(VERSION 3.26)
```

2. 块注释

```cmake
#[[ 这是一个 CMakeLists.txt 文件。
这是一个 CMakeLists.txt 文件
这是一个 CMakeLists.txt 文件]]
cmake_minimum_required(VERSION 3.26)
```

### 2.2简单cmake案例

```
cmake_minimum_required(VERSION 3.26)
project(CMake)
add_executable(CMake main.cpp)
```

#### 2.2.1 `cmake_minimum_required()`:指定使用的 cmake 的最低版本

**可选，非必须，如果不加可能会有警告**

#### 2.2.2 `project`:定义工程名称，并可指定工程的版本、工程描述、web主页地址、支持的语言（默认情况支持所有语言），如果不需要这些都是可以忽略的，只需要指定出工程名字即可。

```
# PROJECT 指令的语法是：
project(<PROJECT-NAME> [<language-name>...])
project(<PROJECT-NAME>
       [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
       [DESCRIPTION <project-description-string>]
       [HOMEPAGE_URL <url-string>]
       [LANGUAGES <language-name>...])
```

#### 2.2.3 `add_executable`:定义工程会生成一个可执行程序

```
add_executable(可执行程序名 源文件名称)
```

+ 这里的可执行程序名和project中的项目名没有任何关系

+ 源文件名可以是一个也可以是多个，如有多个可用空格或`;`间隔

```cmake
# 样式1
add_executable(app add.c div.c main.c mult.c sub.c)
# 样式2
add_executable(app add.c;div.c;main.c;mult.c;sub.c)
```

#### 2.2.4 执行cmake命令

```shell
$ cmake CMakeLists.txt文件所在位置
```

一般我们是创建一个`build`目录，来放置生成的其他文件。在`build`目录下执行

```shell
$ cmake ..
```

再在`build`目录里执行`make`即可

### 2.3 set指令

#### 2.3.1 定义变量

上面我们使用了5个源文件，假设这五个源文件需要反复被使用，每次都直接将它们的名字写出来确实是很麻烦，此时我们就需要定义一个变量，将文件名对应的字符串存储起来，在cmake里定义变量需要使用`set`。

```cmake
# SET 指令的语法是：
# [] 中的参数为可选项, 如不需要可以不写
SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])
```

+ VAR：变量名
+ VALUE：变量值，默认是字符串类型！

```cmake
# 方式1: 各个源文件之间使用空格间隔
# set(SRC_LIST add.c div.c main.c mult.c sub.c)

# 方式2: 各个源文件之间使用分号 ; 间隔
set(SRC_LIST add.c;div.c;main.c;mult.c;sub.c)
add_executable(app  ${SRC_LIST})
```

取变量的值`${xxx}`,xxx是变量的名字

#### 2.3.2 指定c++标准

gcc编译

```shell
$ g++ *.cpp -std=c++11 -o app
```

cmake指定

1. 在 CMakeLists.txt 中通过 set 命令指定

```
#增加-std=c++11
set(CMAKE_CXX_STANDARD 11)
#增加-std=c++14
set(CMAKE_CXX_STANDARD 14)
#增加-std=c++17
set(CMAKE_CXX_STANDARD 17)
```

2. 在执行 cmake 命令的时候指定出这个宏的值
```shell
#增加-std=c++11
cmake CMakeLists.txt 文件路径 -DCMAKE_CXX_STANDARD=11
#增加-std=c++14
cmake CMakeLists.txt 文件路径 -DCMAKE_CXX_STANDARD=14
#增加-std=c++17
cmake CMakeLists.txt 文件路径 -DCMAKE_CXX_STANDARD=17
```

#### 2.3.3 指定输出路径

在CMake中指定可执行程序输出的路径，也对应一个宏，叫做`EXECUTABLE_OUTPUT_PATH`，它的值还是通过`set`命令进行设置:

```cmake
set(HOME /home/robin/Linux/Sort)
set(EXECUTABLE_OUTPUT_PATH ${HOME}/bin)
```

+ 第一行：定义一个变量用于存储一个绝对路径
+ 第二行：将拼接好的路径值设置给EXECUTABLE_OUTPUT_PATH宏
  + 如果这个路径中的子目录不存在，会自动生成，无需自己手动创建

由于可执行程序是基于 cmake 命令生成的 makefile 文件然后再执行 make 命令得到的，所以如果此处指定可执行程序生成路径的时候使用的是相对路径 ./xxx/xxx，那么这个路径中的 ./ 对应的就是 makefile 文件所在的那个目录。

**练习**

```cmake
cmake_minimum_required(VERSION 3.16)
project(CMake_Test)
set(CMAKE_CXX_STANDARD 11)
set(HOME ./exe)
set(EXECUTABLE_OUTPUT_PATH ${HOME}/bin)
set(SRC_LIST add.cpp div.cpp main.cpp mult.cpp sub.cpp)
add_executable(app ${SRC_LIST})
```

```shell
zijianye@zijianye-vm:~/LearnCMake/firstdemo/build$ tree
.
├── CMakeCache.txt
├── CMakeFiles
│   ├── 3.16.3
│   │   ├── CMakeCCompiler.cmake
│   │   ├── CMakeCXXCompiler.cmake
│   │   ├── CMakeDetermineCompilerABI_C.bin
│   │   ├── CMakeDetermineCompilerABI_CXX.bin
│   │   ├── CMakeSystem.cmake
│   │   ├── CompilerIdC
│   │   │   ├── a.out
│   │   │   ├── CMakeCCompilerId.c
│   │   │   └── tmp
│   │   └── CompilerIdCXX
│   │       ├── a.out
│   │       ├── CMakeCXXCompilerId.cpp
│   │       └── tmp
│   ├── app.dir
│   │   ├── add.cpp.o
│   │   ├── build.make
│   │   ├── cmake_clean.cmake
│   │   ├── CXX.includecache
│   │   ├── DependInfo.cmake
│   │   ├── depend.internal
│   │   ├── depend.make
│   │   ├── div.cpp.o
│   │   ├── flags.make
│   │   ├── link.txt
│   │   ├── main.cpp.o
│   │   ├── mult.cpp.o
│   │   ├── progress.make
│   │   └── sub.cpp.o
│   ├── cmake.check_cache
│   ├── CMakeDirectoryInformation.cmake
│   ├── CMakeOutput.log
│   ├── CMakeTmp
│   ├── Makefile2
│   ├── Makefile.cmake
│   ├── progress.marks
│   └── TargetDirectories.txt
├── cmake_install.cmake
├── exe
│   └── bin
│       └── app
└── Makefile

10 directories, 34 files
```

可以看到执行完`cmake ..`后build目录下生成了exe/bin目录，目录为空，再执行make命令，exe/bin目录下有生成了可执行程序app且可以正常运行

> set 可以给自己定义的变量设值，也可以给cmake提供的宏设值

### 2.4搜索命令

如果一个项目里边的源文件很多，在编写CMakeLists.txt文件的时候不可能将项目目录的各个文件一一罗列出来，这样太麻烦也不现实。所以，在CMake中为我们提供了搜索文件的命令，可以使用`aux_source_directory`命令或者`file`命令。

#### 2.4.1方式1

在 CMake 中使用aux_source_directory 命令可以查找某个路径下的所有源文件，命令格式为：

```cmake
aux_source_directory(< dir > < variable >)
```

+ dir：要搜索的目录
+ variable：将从dir目录下搜索到的源文件列表存储到该变量中

```cmake
cmake_minimum_required(VERSION 3.0)
project(CALC)
# 使用 PROJECT_SOURCE_DIR 引用项目根目录路径
message("项目根目录路径: ${PROJECT_SOURCE_DIR}")
# 使用 CMAKE_CURRENT_SOURCE_DIR 引用当前处理的 CMakeLists.txt 文件所在的目录路径
include_directories(${PROJECT_SOURCE_DIR}/include)
# 搜索 src 目录下的源文件
message("当前 CMakeLists.txt 文件所在目录路径: ${CMAKE_CURRENT_SOURCE_DIR}")
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src SRC_LIST)
add_executable(app ${SRC_LIST})
```
> aux_source_directory不支持递归搜索

#### 2.4.2方式2

如果一个项目里边的源文件很多，在编写CMakeLists.txt文件的时候不可能将项目目录的各个文件一一罗列出来，这样太麻烦了。所以，在CMake中为我们提供了搜索文件的命令，他就是file（当然，除了搜索以外通过 file 还可以做其他事情）。

```cmake
file(GLOB/GLOB_RECURSE 变量名 要搜索的文件路径和文件类型)
```

+ GLOB: 将指定目录下搜索到的满足条件的所有文件名生成一个列表，并将其存储到变量中。
+ GLOB_RECURSE：递归搜索指定目录，将搜索到的满足条件的文件名生成一个列表，并将其存储到变量中。

搜索当前目录的src目录下所有的源文件，并存储到变量中

```cmake
file(GLOB MAIN_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
file(GLOB MAIN_HEAD ${CMAKE_CURRENT_SOURCE_DIR}/include/*.h)
```

+ CMAKE_CURRENT_SOURCE_DIR 宏表示当前访问的 CMakeLists.txt 文件所在的路径。
+ 关于要搜索的文件路径和类型可加双引号，也可不加:

  + file(GLOB MAIN_HEAD "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h")

> \#include "head.h" 如果在上面是file(GLOB SRC ${PROJECT_SOURCE_DIR}/*.cpp)，不用包含头文件也能找到，因为是在当前目录下就有头文件

### 2.5包含头文件

```cmake
include_directories(headpath)
```

### 2.6动态库或静态库

#### 2.6.1静态库

```cmake
add_library(库名称 STATIC 源文件1 [源文件2] ...) 
```

> 在Linux中，静态库名字分为三部分：lib+库名字+.a，此处只需要指定出库的名字就可以了，另外两部分在生成该文件的时候会自动填充。
>
> 在Windows中虽然库名和Linux格式不同，但也只需指定出名字即可。
>

#### 2.6.2动态库

```cmake
add_library(库名称 STATIC 源文件1 [源文件2] ...) 
```

> 在Linux中，动态库名字分为三部分：lib+库名字+.so，此处只需要指定出库的名字就可以了，另外两部分在生成该文件的时候会自动填充。
>
> 在Windows中虽然库名和Linux格式不同，但也只需指定出名字即可
>

**练习**

当前文件结构：

```shell
zijianye@zijianye-vm:~/LearnCMake/demo-v3$ tree
.
├── build
├── CMakeLists.txt
├── include
│   └── head.h
├── main.cpp
└── src
    ├── add.cpp
    ├── div.cpp
    ├── mult.cpp
    └── sub.cpp

3 directories, 7 files
```

制作计算器静态库/动态库

```cmake
cmake_minimum_required(VERSION 3.16)
project(CMake_Test)
# 处理头文件
include_directories(${PROJECT_SOURCE_DIR}/include)
# 指定静态库
file(GLOB SRC ${PROJECT_SOURCE_DIR}/src/*.cpp)
# add_executable(app ${SRC})
add_library(calc STATIC ${SRC})
```

![image-20240125191726530](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/image-20240125191726530.png)

> 动态库是绿色的，有可执行权限；
>
> 静态库是灰色的，没有可执行权限。

#### 2.6.3指定生成目录

```
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
# 生成动态库
# add_library(calc SHARED ${SRC})
# 生成静态库
add_library(calc STATIC ${SRC})
```

![image-20240125193512173](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/image-20240125193512173.png)

### 2.7包含库文件

cmake中也为我们提供了相关的加载库的命令。

```cmake
target_link_libraries(
    <target> 
    <PRIVATE|PUBLIC|INTERFACE> <item>... 
    [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)
```

+ target：指定要加载动态库的文件的名字
	+ 该文件可能是一个源文件
 	+ 该文件可能是一个动态库文件
 	+ 该文件可能是一个可执行文件
+ PRIVATE|PUBLIC|INTERFACE：动态库的访问权限，默认为PUBLIC
	+ 如果各个动态库之间没有依赖关系，无需做任何设置，三者没有没有区别，一般无需指定，使用默认的 PUBLIC 即可。

+ **动态库的链接具有传递性**，如果动态库 A 链接了动态库B、C，动态库D链接了动态库A，此时动态库D相当于也链接了动态库B、C，并可以使用动态库B、C中定义的方法。

```cmake
target_link_libraries(A B C)
# A有三个库的定义，且可以在外界使用A、B、C库的内容
target_link_libraries(A PRIVATE B PRIVATE C)
# A可以使用B、C库，但在外界只能使用A库的内容
target_link_libraries(D A)
```
> PUBLIC：在public后面的库会被Link到前面的target中，并且里面的符号也会被导出，提供给第三方使用。
> PRIVATE：在private后面的库仅被link到前面的target中，并且终结掉，第三方不能感知你调了啥库
> INTERFACE：在interface后面引入的库不会被链接到前面的target中，只会导出**符号**。

+ **动态库与静态库的区别**

- 动态库个编译好的程序,**程序运行时可以直接调用其中的函数,** 不参加工程的编译.
- 而静态库应该说是一个程序集, 只是把一些相应的函数总结在一起, 如果调用静态库中的函数,在编译时,**这些调用的函数都将参加编译**

静态库必要的目标代码的是在对程序编译的时候被加入到程序中,而运行时不再需要a的库了

而动态库，则是在运行时转载

**从内存管理的角度来看**

- 静态库必须被链接到主程序中，主程序进入内存，静态库也同时进入内存
- 动态库在生成可执行程序的链接阶段不会被打包到可执行程序中，当可执行程序被启动并且调用了动态库中的函数的时候，动态库才会被加载到内存

> 可以不使用link_libraries(<static lib> [<static lib>...])，而是上述命令，精细化控制
>
> 注意：尽可能使用 target_link_libraries() 命令。链接库依赖关系会自动串联，因此很少需要在整个目录范围内指定链接库。

#### 2.7.1链接静态库

```cmake
cmake_minimum_required(VERSION 3.16)
project(CMake_Test)
# 处理头文件
include_directories(${PROJECT_SOURCE_DIR}/include)
# 指定生成文件
file(GLOB SRC ${PROJECT_SOURCE_DIR}/*.cpp)
# 顺序不能变
add_executable(app ${SRC})
# 指定包含静态库路径
target_link_directories(app PUBLIC ${PROJECT_SOURCE_DIR}/static-lib/)
# 链接静态库
target_link_libraries(app calc)
```

指定链接器在链接给定目标时应在其中搜索库的路径。每个项目可以是绝对路径或相对路径，后者被解释为相对于当前源目录。这些项目将被添加到链接命令中。

> target_link_directories命令很少需要，并且在有其他选择的情况下应避免使用。

```cmake
# 指定包含静态库路径
link_directories(${PROJECT_SOURCE_DIR}/lib)
# 链接静态库
link_libraries(calc)
add_executable(app ${SRC_LIST})
```

~~已弃用~~

#### 2.7.2链接动态库

```cmake
add_executable(app ${SRC})
target_link_directories(app PUBLIC ${PROJECT_SOURCE_DIR}/shared-lib/)
target_link_libraries(app calc)
```

> link_libraries（全局）、target_link_directories和link_directories官网都推荐少使用，

### 2.8日志

```cmake
message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR] "message to display" ...)
```

+ (无) ：重要消息
+ STATUS ：非重要消息
+ WARNING：CMake 警告, 会继续执行
+ AUTHOR_WARNING：CMake 警告 (dev), 会继续执行
+ SEND_ERROR：CMake 错误, 继续执行，但是会跳过生成的步骤
+ FATAL_ERROR：CMake 错误, 终止所有处理过程

### 2.9变量操作

有时候项目中的源文件并不一定都在同一个目录中，但是这些源文件最终却需要一起进行编译来生成最终的可执行文件或者库文件。如果我们通过file命令对各个目录下的源文件进行搜索，最后还需要做一个字符串拼接的操作，关于字符串拼接可以使用set命令也可以使用list命令。

#### 2.9.1追加

##### set拼接

```cmake
set(变量名1 ${变量名1} ${变量名2} ...)
```

关于上面的命令其实就是将从第二个参数开始往后所有的字符串进行拼接，最后将结果存储到第一个参数中，如果第一个参数中原来有数据会对原数据就行覆盖。

##### list追加

```cmake
list(APPEND <list> [<element> ...])
```

ist命令的功能比set要强大，字符串拼接只是它的其中一个功能，所以需要在它第一个参数的位置指定出我们要做的操作，APPEND表示进行数据追加，后边的参数和set就一样了。

在CMake中，使用set命令可以创建一个list。一个在list内部是一个由分号;分割的一组字符串。例如，set(var a b c d e)命令将会创建一个list:a;b;c;d;e，但是最终打印变量值的时候得到的是abcde。

#### 2.9.2字符串移除

```cmake
list(REMOVE_ITEM <list> <value> [<value> ...])
```

```
file(GLOB SRC ${PROJECT_SOURCE_DIR}/src/*.cpp)
message(${SRC})
list(REMOVE_ITEM SRC ${PROJECT_SOURCE_DIR}/src/main.cpp)
message(${SRC})
```

通过 file 命令搜索源文件的时候得到的是文件的绝对路径（在list中每个文件对应的路径都是一个item，并且都是绝对路径），那么在移除的时候也要将该文件的绝对路径指定出来才可以，否是移除操作不会成功。

```cmake
file(GLOB SRC ${PROJECT_SOURCE_DIR}/src/*.cpp)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
add_library(clac SHARED ${SRC})
set(tmp1 hello world)
# helloworld
set(tmp2 ${tmp1} ${SRC})
# helloworld/home/zijianye/LearnCMake/demo-v5/main.cpp
set(tmp3 hello;world)
# helloworld
list(APPEND tmp3 ${SRC})
# helloworld/home/zijianye/LearnCMake/demo-v5/main.cpp
message(${tmp1})
message(${tmp2})
message(${tmp3})
message("========")
message(${SRC})
list(REMOVE_ITEM SRC ${PROJECT_SOURCE_DIR}/src/main.cpp)
message(${SRC})
message("========")
set(LIST foo bar baz)
set(NEWLIST "")
foreach(ITEM ${LIST})
  list(APPEND NEWLIST "${ITEM}_suffix")
endforeach()
message("New list: ${NEWLIST}")
```

#### 2.9.3list其他操作

[见其它功能](https://subingwen.cn/cmake/CMake-primer/)

> list中的项目默认用;作为分隔符。如果list项目本身包含;字符,打印时就会被CMake识别为多个项目。
>
> 这个时候可以使用双引号将整个list项目括起来,这样CMake就会正确打印包含;的单个项目:

### 2.10宏定义

在进行程序测试的时候，我们可以在代码中添加一些宏定义，通过这些宏来控制这些代码是否生效，如下所示：

```cpp
#include <cstdio>
#define NUMBER  3

int main()
{
    int a = 10;
#ifdef DEBUG
    printf("我是一个程序猿, 我不会爬树...\n");
#endif
    for(int i=0; i<NUMBER; ++i)
    {
        printf("hello, GCC!!!\n");
    }
    return 0;
}
```

+ 方案1：在g++/gcc命令中去指定

```shell
$ gcc test.c -DDEBUG -o app
```

在gcc/g++命令中通过参数 -D指定出要定义的宏的名字，这样就相当于在代码中定义了一个宏，其名字为DEBUG。

+ 方案2：在cmake中去指定

```cmake
add_compile_definitions(<definition> ...)
```

```cmake
cmake_minimum_required(VERSION 3.16)
project(CMake_Test)
# 自定义 DEBUG 宏
add_compile_definitions(DEBUG)
file(GLOB SRC ${PROJECT_SOURCE_DIR}/*.cpp)
add_executable(app ${SRC})
```

>注意，add_definitions(-DDEBUG)已被弃用，且宏名字前要加`-D`，如示例是添加了DEBUG宏
>
>```cmake
>add_compile_definitions(<definition> ...)
>add_definitions(-DFOO -DBAR ...)
>```

| 宏                       | 功能                                                         |
| ------------------------ | ------------------------------------------------------------ |
| PROJECT_SOURCE_DIR       | 使用cmake命令后紧跟的目录，一般是工程的根目录                |
| PROJECT_BINARY_DIR       | 执行cmake命令的目录                                          |
| CMAKE_CURRENT_SOURCE_DIR | 当前处理的CMakeLists.txt所在的路径                           |
| CMAKE_CURRENT_BINARY_DIR | target 编译目录                                              |
| EXECUTABLE_OUTPUT_PATH   | 重新定义目标二进制可执行文件的存放位置                       |
| LIBRARY_OUTPUT_PATH      | 重新定义目标链接库文件的存放位置                             |
| PROJECT_NAME             | 返回通过PROJECT指令定义的项目名称                            |
| CMAKE_BINARY_DIR         | 项目实际构建路径，假设在build目录进行的构建，那么得到的就是这个目录的路径 |
## 3.嵌套的CMake

如果项目很大，或者项目中有很多的源码目录，在通过CMake管理项目的时候如果只使用一个CMakeLists.txt，那么这个文件相对会比较复杂，有一种化繁为简的方式就是给每个源码目录都添加一个CMakeLists.txt文件（头文件目录不需要），这样每个文件都不会太复杂，而且更灵活，更容易维护。

```shell
zijianye@zijianye-vm:~/LearnCMake/demo-v7$ tree
.
├── build
├── calc
│   ├── add.cpp
│   ├── CMakeLists.txt
│   ├── div.cpp
│   ├── mult.cpp
│   └── sub.cpp
├── CMakeLists.txt
├── include
│   ├── calc.h
│   └── sort.h
├── sort
│   ├── bubble.cpp
│   ├── CMakeLists.txt
│   └── quick.cpp
├── test1
│   ├── calc.cpp
│   └── CMakeLists.txt
└── test2
    ├── CMakeLists.txt
    └── sort.cpp

6 directories, 15 files
```

### 3.1节点关系

众所周知，Linux的目录是树状结构，所以嵌套的 CMake 也是一个树状结构，最顶层的 CMakeLists.txt 是根节点，其次都是子节点。因此，我们需要了解一些关于 CMakeLists.txt 文件变量作用域的一些信息：

+ 根节点CMakeLists.txt中的变量全局有效
+ 父节点CMakeLists.txt中的变量可以在子节点中使用
+ 子节点CMakeLists.txt中的变量只能在当前节点中使用

#### 3.1.1添加子目录

```cmake
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

+ source_dir：指定了CMakeLists.txt源文件和代码文件的位置，其实就是指定子目录
+ binary_dir：指定了输出文件的路径，一般不需要指定，忽略即可。
+ EXCLUDE_FROM_ALL：在子路径下的目标默认不会被包含到父路径的ALL目标里，并且也会被排除在IDE工程文件之外。用户必须显式构建在子路径下的目标。
通过这种方式CMakeLists.txt文件之间的父子关系就被构建出来了。

### 3.2实现

clac目录和sort目录内容可以编译成库文件，test1和test2内容可以编译成可执行文件，include不用编译

> 动态库和静态库的选择
>
> 1.源文件多，动态库；源文件少，静态库
>
> 2.要求生成的可执行文件小，用动态库；没有要求，静态库

#### 3.2.1根目录

```cmake
cmake_minimum_required(VERSION 3.16)
project(test)
# 定义变量
# 静态库生成路径
set(LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/lib)
# 测试可执行文件的路径
set(EXEC_PATH ${CMAKE_CURRENT_SOURCE_DIR}/bin)
# 头文件路径
set(HEAD_PATH ${CMAKE_CURRENT_SOURCE_DIR}/include)
# 静态库的名字
set(CALCLIB calc)
set(SORTLIB sort)
# 可执行程序的名字
set(APPNAME1 app1)
set(APPNAME2 app2)
message("root ${CMAKE_CURRENT_SOURCE_DIR}")
message("root ${PROJECT_SOURCE_DIR}")
# 添加子目录
add_subdirectory(calc)
add_subdirectory(sort)
add_subdirectory(test1)
add_subdirectory(test2)
```

在根节点对应的文件中主要做了两件事情：`定义全局变量`和`添加子目录`。

+ 定义的全局变量主要是给子节点使用，目的是为了提高子节点中的CMakeLists.txt文件的可读性和可维护性，避免冗余并降低出差的概率。
+ 一共添加了四个子目录，每个子目录中都有一个CMakeLists.txt文件，这样它们的父子关系就被确定下来了。

> root /home/zijianye/LearnCMake/demo-v7
> root /home/zijianye/LearnCMake/demo-v7
> clac /home/zijianye/LearnCMake/demo-v7/calc
> calc /home/zijianye/LearnCMake/demo-v7/calc
> sort /home/zijianye/LearnCMake/demo-v7/sort
> sort /home/zijianye/LearnCMake/demo-v7/sort
>
> message("root ${CMAKE_CURRENT_SOURCE_DIR}")
> message("root ${PROJECT_SOURCE_DIR}") 目前没看出不同

其他目录的CMakeLists.txt文件见项目

最终的生成目录

![image-20240126005002638](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/image-20240126005002638.png)

动态库包含的写法，其中在bubble.cpp中用到了其他动态库`${CALCLIB}`

```cmake
cmake_minimum_required(VERSION 3.16)
project(sort)
# 处理头文件
include_directories(${HEAD_PATH})
# 搜索源文件
file(GLOB SRC ${PROJECT_SOURCE_DIR}/*.cpp)
set(LIBRARY_OUTPUT_PATH ${LIB_PATH})
add_library(${SORTLIB} SHARED ${SRC})
# 指定包含动态库路径
target_link_directories(${SORTLIB} PUBLIC ${LIB_PATH})
# 链接动态库
target_link_libraries(${SORTLIB} ${CALCLIB})
```

