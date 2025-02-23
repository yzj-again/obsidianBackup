---
date: 2024-12-12
tags:
  - cmake
---
生成器表达式在构建系统生成过程中进行评估，以生成每个构建配置的特定信息。

在许多目标属性（如 LINK_LIBRARIES、INCLUDE_DIRECTORIES、COMPILE_DEFINITIONS 等）的上下文中都允许使用生成器表达式。 在使用 target_link_libraries()、target_include_directories()、target_compile_definitions() 等命令填充这些属性时，也可以使用这些表达式。

生成器表达式可用于启用条件链接、编译时使用的条件定义、条件包含目录等。 这些条件可以基于编译配置、目标属性、平台信息或任何其他可查询的信息。

生成器表达式有多种类型，包括逻辑表达式、信息表达式和输出表达式。

逻辑表达式用于创建条件输出。 基本表达式是 0 和 1 表达式。 `$<0:...>`的结果是空字符串，`$<1:...>`的结果是 .... 的内容。 它们还可以嵌套。

## Exercise 1 - Adding Compiler Warning Flags with Generator Expression

生成器表达式的一个常见用法是有条件地添加编译器标志，如语言级别或警告标志。 一种不错的模式是将这些信息与 INTERFACE 目标相关联，从而使这些信息得以传播。

### Goal

在编译时添加编译器警告标志，但不针对已安装的版本。

### Files to Edit

```shell
$ tree                                               
.
├── CMakeLists.txt*
├── MathFunctions
│   ├── CMakeLists.txt
│   ├── MathFunctions.cxx
│   ├── MathFunctions.h
│   ├── mysqrt.cxx
│   └── mysqrt.h
├── TutorialConfig.h.in
└── tutorial.cxx

1 directory, 8 files
```

### Getting Started

首先，在顶层 CMakeLists.txt 文件中，我们需要将 `cmake_minimum_required()` 设置为 3.15。 在本练习中，我们将使用 CMake 3.15 中引入的生成器表达式。

接下来，我们为项目添加所需的编译器警告标志。 由于警告标志因编译器而异，我们使用 `COMPILE_LANG_AND_ID` 生成器表达式来控制在给定语言和编译器 ID 的情况下应用哪些标志。

### Solution

TODO 1：更新 cmake_minimum_required() 至少需要 CMake 3.15 版本：

```cmake
cmake_minimum_required(VERSION 3.15)
```

TODO 2：接下来，我们要确定系统当前使用的编译器，因为警告标志会根据编译器的不同而变化。 这是通过 `COMPILE_LANG_AND_ID` 生成器表达式完成的。 我们在变量 gcc_like_cxx 和 msvc_cxx 中设置结果如下：

```cmake
set(gcc_like_cxx "$<COMPILE_LANG_AND_ID:CXX,ARMClang,AppleClang,Clang,GNU,LCC>")
set(msvc_cxx "$<COMPILE_LANG_AND_ID:CXX,MSVC>")
```

TODO 3：接下来，我们为项目添加所需的编译器警告标志。 使用变量 gcc_like_cxx 和 msvc_cxx，我们可以使用另一个生成器表达式，只有当变量为 true 时才应用相应的标志。 我们使用 target_compile_options() 将这些标志应用到我们的接口库中。

```cmake
target_compile_options(tutorial_compiler_flags INTERFACE
  "$<${gcc_like_cxx}:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>"
  "$<${msvc_cxx}:-W3>"
)
```

> `$< ... >`：表示生成一个字符串，这个字符串的值就是表达式内部计算的结果。

TODO 4：最后，我们只希望在构建过程中使用这些警告标志。 已安装项目的用户不应继承我们的警告标志。 为了明确这一点，我们使用 BUILD_INTERFACE 条件将 TODO 3 中的标志包在生成器表达式中。 生成的完整代码如下：

```cmake
target_compile_options(tutorial_compiler_flags INTERFACE
  "$<${gcc_like_cxx}:$<BUILD_INTERFACE:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>>"
  "$<${msvc_cxx}:$<BUILD_INTERFACE:-W3>>"
)
```

