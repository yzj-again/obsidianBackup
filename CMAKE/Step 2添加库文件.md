---
date: 2024-12-11
tags:
  - cmake
---
至此，我们已经了解了如何使用 CMake 创建基本项目。在此步骤中，我们将学习如何在项目中创建和使用库。我们还将了解如何使我们的库的使用成为==可选==的。

## Exercise 1 - Creating a Library

要在 CMake 中添加一个库，请使用 `add_library()` 命令并指定组成该库的源文件。

我们可以用一个或多个子目录来组织项目，而不是将所有源文件放在一个目录中。 在本例中，我们将专门为库创建一个子目录。 在这里，我们可以添加一个新的 CMakeLists.txt 文件和一个或多个源文件。 在顶层 CMakeLists.txt 文件中，我们将使用 `add_subdirectory()` 命令将子目录添加到构建中。

一旦创建了库，就可以使用 `target_include_directories()` 和 `target_link_libraries()` 将其连接到我们的可执行目标。

###  Goal

添加并且使用库。

### Files to Edit

```shell
$ tree
.
├── CMakeLists.txt*
├── MathFunctions
│   ├── CMakeLists.txt*
│   ├── MathFunctions.cxx
│   ├── MathFunctions.h
│   ├── mysqrt.cxx
│   └── mysqrt.h
├── TutorialConfig.h.in
└── tutorial.cxx*

1 directory, 8 files
```

### Getting Started

在本练习中，我们将在项目中添加一个库，其中包含我们自己实现的数字平方根计算方法。 然后，可执行文件就可以使用这个库，而不是编译器提供的标准平方根函数。

在本教程中，我们将把函数库放到名为 MathFunctions 的子目录中。 这个目录已经包含了头文件 MathFunctions.h 和 mysqrt.h。我们还提供了它们各自的源文件 MathFunctions.cxx 和 mysqrt.cxx，我们不需要修改任何这些文件。 mysqrt.cxx 有一个名为 mysqrt 的函数，提供与编译器的 sqrt 函数类似的功能。 MathFunctions.cxx 包含一个函数 sqrt，用于隐藏 sqrt 的实现细节。

### Solution

TODO 1：在 MathFunctions 目录下的 CMakeLists.txt 文件中，我们使用 add_library() 创建了一个名为 MathFunctions 的目标库。 库的源文件作为参数传递给 add_library()。 如下行所示

```cmake
add_library(MathFunctions MathFunctions.cxx mysqrt.cxx)
```

TODO 2：为了使用新库，我们将在顶层 CMakeLists.txt 文件中添加 add_subdirectory() 调用，这样该库就会被编译。

```cmake
add_subdirectory(MathFunctions)
```

TODO 3：接下来，使用 target_link_libraries() 将新目标库链接到可执行目标。

```cmake
target_link_libraries(Tutorial PUBLIC MathFunctions)
```

TODO4：最后，我们需要指定库的头文件位置。 修改现有的 target_include_directories() 调用，将 MathFunctions 子目录添加为包含目录，以便找到 MathFunctions.h 头文件。

```cmake
target_include_directories(Tutorial PUBLIC
                          "${PROJECT_BINARY_DIR}"
                          "${PROJECT_SOURCE_DIR}/MathFunctions"
                          )
```

TODO5：现在，让我们使用我们的函数库。 在 tutorial.cxx 中，包含 MathFunctions.h：

```c++
#include "MathFunctions.h"
```

TODO6：最后，将 sqrt 替换为封装函数 mathfunctions::sqrt。

```c++
  const double outputValue = mathfunctions::sqrt(inputValue);
```

### Final
```cmake
TODO 1: Add a library called MathFunctions with sources MathFunctions.cxx
# and mysqrt.cxx
# Hint: You will need the add_library command
add_library(MathFunctions MathFunctions.cxx mysqrt.cxx)
```

```cmake
cmake_minimum_required(VERSION 3.10)

# set the project name and version
project(Tutorial VERSION 1.0)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# configure a header file to pass some of the CMake settings
# to the source code
configure_file(TutorialConfig.h.in TutorialConfig.h)

# TODO 2: Use add_subdirectory() to add MathFunctions to this project
add_subdirectory(MathFunctions)

# add the executable
add_executable(Tutorial tutorial.cxx)

# TODO 3: Use target_link_libraries to link the library to our executable
target_link_libraries(Tutorial PUBLIC MathFunctions)

# TODO 4: Add MathFunctions to Tutorial's target_include_directories()
# Hint: ${PROJECT_SOURCE_DIR} is a path to the project source. AKA This folder!
# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           "${PROJECT_SOURCE_DIR}/MathFunctions"
                           )
```

## Exercise 2 - Adding an Option

现在，让我们在 MathFunctions 库中添加一个选项，允许开发人员选择自定义平方根实现或内置标准实现。 虽然在教程中确实不需要这样做，但对于大型项目来说，这是经常发生的事情。

CMake 可以使用 `option()` 命令实现这一功能。 这样，用户就可以在配置 cmake 编译时更改一个变量。 该设置将保存在缓存中，因此用户无需每次在构建目录上运行 CMake 时都设置该值。

### Goal

添加不使用 MathFunctions 的构建选项。

### Files to Edit

```
$ tree
.
├── CMakeLists.txt
├── MathFunctions
│   ├── CMakeLists.txt*
│   ├── MathFunctions.cxx*
│   ├── MathFunctions.h
│   ├── mysqrt.cxx
│   └── mysqrt.h
├── TutorialConfig.h.in
└── tutorial.cxx

1 directory, 8 files
```

### Getting Started

首先，使用 MathFunctions/CMakeLists.txt 中的 option() 命令创建一个变量 `USE_MYMATH`。 在同一文件中，使用该选项将编译定义传递给 MathFunctions 库。

然后，更新 MathFunctions.cxx，根据 USE_MYMATH 重定向编译。

最后，通过在 MathFunctions/CMakeLists.txt 的 USE_MYMATH 代码段中将 mysqrt.cxx 设为自己的库，防止在 USE_MYMATH 启用时编译 mysqrt.cxx。

### Solution

TODO 7：第一步是在 MathFunctions/CMakeLists.txt 中添加一个选项。 该选项将显示在 cmake-gui 和 ccmake 中，默认值为 ON，用户可以更改。

```cmake
option(USE_MYMATH "Use tutorial provided math implementation" ON)
```

TODO 8：接下来，使用这一新选项使我们的程序库与 mysqrt 函数的构建和链接成为有条件的。

创建一条 if() 语句，检查 USE_MYMATH 的值。 在 if() 块中，将 target_compile_definitions() 命令与编译定义 USE_MYMATH 放在一起。

```cmake
if (USE_MYMATH)
  target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")
endif()
```

当 USE_MYMATH 开启时，编译定义 USE_MYMATH 将被设置。 然后，我们就可以使用这个编译定义来启用或禁用源代码的各个部分。

TODO 9：对源代码的相应修改相当简单。 在 MathFunctions.cxx 中，我们让 USE_MYMATH 控制使用哪个平方根函数：

```c++
#ifdef USE_MYMATH
  return detail::mysqrt(x);
#else
  return std::sqrt(x);
#endif
```

TODO 10：接下来，如果定义了 USE_MYMATH，我们需要包含 mysqrt.h。

```c++
#ifdef USE_MYMATH
#  include "mysqrt.h"
#endif
```

TODO 11：最后，既然我们使用了 std::sqrt，就需要加入 cmath。

```c++
#include <cmath>
```

此时，如果关闭 USE_MYMATH，就不会使用 mysqrt.cxx，但它仍会被编译，因为 MathFunctions 目标程序的源代码中列出了 mysqrt.cxx。

有几种方法可以解决这个问题。 第一种方法是使用 target_sources()，在 USE_MYMATH 代码块中添加 mysqrt.cxx。 另一种方法是在 USE_MYMATH 代码块中创建一个额外的库，负责编译 mysqrt.cxx。 在本教程中，我们将创建一个附加库。

TODO 12：首先，在 USE_MYMATH 中创建一个名为 SqrtLibrary 的库，其源代码为 mysqrt.cxx。

```cmake
add_library(SqrtLibrary STATIC
              mysqrt.cxx
              )
```

接下来，当启用 USE_MYMATH 时，我们将 SqrtLibrary 链接到 MathFunctions。

```cmake
target_link_libraries(MathFunctions PRIVATE SqrtLibrary)
```

最后，我们可以从 MathFunctions 库源代码列表中删除 mysqrt.cxx，因为当 SqrtLibrary 被包含时，它将被调入。

```cmake
add_library(MathFunctions MathFunctions.cxx)
```

有了这些更改，mysqrt 函数现在对于构建和使用 MathFunctions 函数库的用户来说完全是可选的了。 用户可以切换 USE_MYMATH 来控制在构建过程中使用哪个库。

### Final

```cmake
# TODO 14: Remove mysqrt.cxx from the list of sources
add_library(MathFunctions MathFunctions.cxx)

# Create a variable USE_MYMATH using option and set default to ON
option(USE_MYMATH "Use tutorial provided math implementation" ON)

# TODO 8: If USE_MYMATH is ON, use target_compile_definitions to pass
# USE_MYMATH as a precompiled definition to our source files
if (USE_MYMATH)
	target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")

# TODO 12: When USE_MYMATH is ON, add a library for SqrtLibrary with
# source mysqrt.cxx
add_library(SqrtLibrary STATIC
              mysqrt.cxx
              )
              
# TODO 13: When USE_MYMATH is ON, link SqrtLibrary to the MathFunctions Library
target_link_libraries(MathFunctions PRIVATE SqrtLibrary)

endif()
```

```c++
#include "MathFunctions.h"

// TODO 11: include cmath
#include <cmath>

// TODO 10: Wrap the mysqrt include in a precompiled ifdef based on
// USE_MYMATH

#ifdef USE_MYMATH
#include "mysqrt.h"
#endif

namespace mathfunctions {

double sqrt(double x)
{
// TODO 9: If USE_MYMATH is defined, use detail::mysqrt.
// Otherwise, use std::sqrt.
#ifdef USE_MYMATH
    return detail::mysqrt(x);
#else
    return std::sqrt(x);
#endif
}
}  // namespace mathfunctions
```

```shell
-rw-rw-r--  1 zijianye zijianye 5000 12月 11 23:16 libMathFunctions.a
-rw-rw-r--  1 zijianye zijianye 1644 12月 11 23:46 libMathFunctionsOFF.a
-rw-rw-r--  1 zijianye zijianye 1668 12月 11 23:51 libMathFunctionsON.a
-rw-rw-r--  1 zijianye zijianye 3404 12月 11 23:51 libSqrtLibrary.a
-rwxrwxr-x  1 zijianye zijianye 18480 12月 12 00:26 TutorialOFF*
-rwxrwxr-x  1 zijianye zijianye 18648 12月 12 00:26 TutorialON*
-rwxrwxr-x  1 zijianye zijianye 18648 12月 12 00:36 Tutorial*
$ diff Tutorial TutorialOFF
二进制文件 Tutorial 和 TutorialOFF 不同
$ diff Tutorial TutorialON
```

> 静态库连接，即在构建可执行文件时，库的代码已经被静态地链接到可执行文件中，那么在运行时不再需要原始的库文件。这意味着即使删除了库文件，可执行文件仍然可以运行。
> 虽然最终结果相同，但两种方式在构建过程中略有不同：
> 
> - 第一种方式更简单直接
> - 第二种方式允许更好的模块化和代码组织，特别是在大型项目中


