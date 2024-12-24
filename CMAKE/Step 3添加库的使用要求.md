---
date: 2024-12-11
tags:
  - cmake
---

## Exercise 1 - Adding Usage Requirements for a Library
目标参数的使用要求可以更好地控制库或可执行文件的链接和包含行，同时还能在 CMake 内部更好地控制目标的传递属性。 利用使用要求的主要命令有：

- [`target_compile_definitions()`](https://cmake.org/cmake/help/latest/command/target_compile_definitions.html#command:target_compile_definitions "target_compile_definitions")
- [`target_compile_options()`](https://cmake.org/cmake/help/latest/command/target_compile_options.html#command:target_compile_options "target_compile_options")
- [`target_include_directories()`](https://cmake.org/cmake/help/latest/command/target_include_directories.html#command:target_include_directories "target_include_directories")
- [`target_link_directories()`](https://cmake.org/cmake/help/latest/command/target_link_directories.html#command:target_link_directories "target_link_directories")
- [`target_link_options()`](https://cmake.org/cmake/help/latest/command/target_link_options.html#command:target_link_options "target_link_options")
- [`target_precompile_headers()`](https://cmake.org/cmake/help/latest/command/target_precompile_headers.html#command:target_precompile_headers "target_precompile_headers")
- [`target_sources()`](https://cmake.org/cmake/help/latest/command/target_sources.html#command:target_sources "target_sources")

### Goal

添加库的使用要求。

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
└── tutorial.cxx

1 directory, 8 files
```

### Getting Started

在本练习中，我们将重构添加库中的代码，以使用现代 CMake 方法。 我们将让我们的库定义自己的使用要求，以便在必要时将它们传递给其他目标。 在这种情况下，MathFunctions 将自行指定任何需要的 include 目录。 然后，目标 Tutorial 只需链接到 MathFunctions，而不必担心任何额外的 include 目录。

首先，在 MathFunctions/CMakeLists 中添加对 target_include_directories() 的调用。 请记住，CMAKE_CURRENT_SOURCE_DIR 是当前正在处理的源代码目录的路径。

然后，更新（并简化！）顶层 CMakeLists.txt 中对 target_include_directories() 的调用。

### Solution

我们希望规定，链接到 MathFunctions 的任何人都需要包含当前源代码目录，而 MathFunctions 本身则不需要。 这可以用 INTERFACE 使用要求来表达。 记住，INTERFACE 是指消费者需要但生产者不需要的东西。

TODO 1：在 MathFunctions/CMakeLists.txt 的末尾，使用带有 INTERFACE 关键字的 target_include_directories()，如下所示：

```cmake
target_include_directories(MathFunctions
                           INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
                           )
```

TODO 2：既然我们已经指定了 MathFunctions 的使用要求，就可以放心地从 CMakeLists.txt 顶层删除 EXTRA_INCLUDES 变量的使用了（传递给了顶层目标）。

```cmake
list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
```

TODO 3：并删除 target_include_directories 中的 EXTRA_INCLUDES：

```cmake
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           )
```

请注意，使用这种技术，我们的可执行目标要使用我们的库，唯一要做的就是调用 target_link_libraries()，并输入目标库的名称。 在大型项目中，手动指定库依赖关系的传统方法很快就会变得非常复杂。

```cmake
# MathFunctions/CMakeLists.txt
add_library(MathFunctions MathFunctions.cxx)

# TODO 1: State that anybody linking to MathFunctions needs to include the
# current source directory, while MathFunctions itself doesn't.
# Hint: Use target_include_directories with the INTERFACE keyword
target_include_directories(MathFunctions
                           INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
                           )
                           
# should we use our own math functions
option(USE_MYMATH "Use tutorial provided math implementation" ON)
if (USE_MYMATH)
  target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")
  
  # library that just does sqrt
  add_library(SqrtLibrary STATIC
              mysqrt.cxx
              )
           
  target_link_libraries(MathFunctions PRIVATE SqrtLibrary)

endif()
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

# TODO 2: Remove EXTRA_INCLUDES list
# list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")

# add the MathFunctions library
add_subdirectory(MathFunctions)

# add the executable
add_executable(Tutorial tutorial.cxx)

target_link_libraries(Tutorial PUBLIC MathFunctions)

# TODO 3: Remove use of EXTRA_INCLUDES
# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           )
```

> 在 CMake 中，INTERFACE 关键字用于指定目标的接口属性，这些属性仅对使用该目标的其他目标（即依赖于该目标的目标）可见，而不影响该目标本身的构建过程。
> 当将 `INTERFACE` 关键字与 `target_include_directories()` 函数一起使用时，它将指定目标的包含目录作为接口属性。这些接口属性会传递给依赖该目标的其他目标（顶层CMakeLists）。

## ## Exercise 2 - Setting the C++ Standard with Interface Libraries

既然我们已经将代码转换为更现代的方法，那么让我们来演示一下为多个目标设置属性的现代技术。

让我们重构现有代码，使用 INTERFACE 库。 我们将在下一步中使用该库来演示生成器表达式的常用用法。

### Goal

添加 INTERFACE 库目标，指定所需的 C++ 标准。

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
└── tutorial.cxx

1 directory, 8 files
```

### Getting Started

在本练习中，我们将重构代码，使用 INTERFACE 库来指定 C++ 标准。

首先编辑顶层 CMakeLists.txt 文件。 构建名为 tutorial_compiler_flags 的 INTERFACE 库目标，并指定 cxx_std_11 为目标编译器特征。

修改 CMakeLists.txt 和 MathFunctions/CMakeLists.txt，使所有目标都有 target_link_libraries() 调用 tutorial_compiler_flags。

### Solution

让我们更新上一步的代码，使用接口库来设置 C++ 要求。

首先，我们需要删除变量 CMAKE_CXX_STANDARD 和 CMAKE_CXX_STANDARD_REQUIRED 上的两个 set() 调用。 需要删除的具体行列如下：

```cmake
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
```

TODO 4：接下来，我们需要创建一个接口库 tutorial_compiler_flags。 然后使用 target_compile_features() 添加编译器特性 cxx_std_11。

```cmake
add_library(tutorial_compiler_flags INTERFACE)
target_compile_features(tutorial_compiler_flags INTERFACE cxx_std_11)
```

> 这个命令设置 tutorial_compiler_flags 库需要 C++11 标准特性。INTERFACE 意味着这个特性要求被传播到任何使用这个接口库的其他目标。

TODO 5：最后，设置好接口库后，我们需要将可执行的 Tutorial、SqrtLibrary 库和 MathFunctions 库链接到新的 tutorial_compiler_flags 库（需要分别进行传递接口属性）。 代码将分别如下：

```cmake
target_link_libraries(Tutorial PUBLIC MathFunctions tutorial_compiler_flags)
```

TODO 6：这个：

```cmake
target_link_libraries(SqrtLibrary PUBLIC tutorial_compiler_flags)
```

TODO 7：和这个：

```cmake
target_link_libraries(MathFunctions PUBLIC tutorial_compiler_flags)
```

这样，我们的所有代码仍然需要 C++ 11 才能构建。 不过请注意，有了这种方法，我们就能明确哪些目标需要特定的要求。 此外，我们还在接口库中创建了一个单一的真相源。

> 和 set(CMAKE_CXX_STANDARD 11) 比较
> 功能替换：是的，这确实是在用一个更细粒度、更现代的方法来替代 set(CMAKE_CXX_STANDARD 11) 和 set(CMAKE_CXX_STANDARD_REQUIRED True)。这种方式提供了以下优势：
> 	-  模块化：每个目标可以有不同的编译标准或选项，而不需要全局设置。
> 	-  精确控制：可以为每个目标或库设置不同的编译特性，而不是全局应用。
> 	-  依赖传递：通过 PUBLIC 或 INTERFACE 可以控制哪些依赖如何传递给其他目标。

```cmake
add_library(MathFunctions MathFunctions.cxx)

target_include_directories(MathFunctions
                           INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
                           )
# should we use our own math functions
option(USE_MYMATH "Use tutorial provided math implementation" ON)

if (USE_MYMATH)

  target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")
  # library that just does sqrt

  add_library(SqrtLibrary STATIC
              mysqrt.cxx
              )
  # TODO 6: Link SqrtLibrary to tutorial_compiler_flags
  target_link_libraries(SqrtLibrary PUBLIC tutorial_compiler_flags)
  target_link_libraries(MathFunctions PRIVATE SqrtLibrary)

endif()

# TODO 7: Link MathFunctions to tutorial_compiler_flags
target_link_libraries(MathFunctions PUBLIC tutorial_compiler_flags)
```

```cmake
cmake_minimum_required(VERSION 3.10)

# set the project name and version
project(Tutorial VERSION 1.0)

# TODO 4: Replace the following code by:
# * Creating an interface library called tutorial_compiler_flags
#   Hint: use add_library() with the INTERFACE signature
# * Add compiler feature cxx_std_11 to tutorial_compiler_flags
#   Hint: Use target_compile_features()
add_library(tutorial_compiler_flags INTERFACE)
target_compile_features(tutorial_compiler_flags INTERFACE cxx_std_11)

# specify the C++ standard
# set(CMAKE_CXX_STANDARD 11)
# set(CMAKE_CXX_STANDARD_REQUIRED True)

# configure a header file to pass some of the CMake settings
# to the source code
configure_file(TutorialConfig.h.in TutorialConfig.h)

# Remove EXTRA_INCLUDES list
# list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")

# add the MathFunctions library
add_subdirectory(MathFunctions)

# add the executable
add_executable(Tutorial tutorial.cxx)

# TODO 5: Link Tutorial to tutorial_compiler_flags
target_link_libraries(Tutorial PUBLIC MathFunctions tutorial_compiler_flags)
# target_link_libraries(Tutorial PUBLIC MathFunctions)

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           )
```
		
    
        
    
    
    
