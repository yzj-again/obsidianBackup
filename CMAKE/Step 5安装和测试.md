---
date: 2024-12-16
tags:
  - cmake
---

## Exercise 1 - Install Rules

通常情况下，仅仅构建可执行文件是不够的，它还应该是可安装的。 在 CMake 中，我们可以使用 install() 命令指定安装规则。 在 CMake 中支持本地安装，通常只需指定安装位置、目标和要安装的文件。

### Goal

安装 Tutorial 可执行文件和 MathFunctions 库。

### Files to Edit

+ `MathFunctions/CMakeLists.txt`
+ `CMakeLists.txt`

### Getting Started

首先，更新 `MathFunctions/CMakeLists.txt`，将 MathFunctions 和 tutorial_compiler_flags 库安装到 lib 目录。 在同一文件中，指定将 MathFunctions.h 安装到 include 目录所需的安装规则。

然后，更新 CMakeLists.txt 顶层，将 Tutorial 可执行文件安装到 bin 目录。 最后，任何头文件都应安装到 include 目录。 请记住，TutorialConfig.h 位于 PROJECT_BINARY_DIR 中。

### Solution

我们项目的安装规则相当简单：

+ 对于 MathFunctions，我们要将库和头文件分别安装到 lib 和 include 目录中。
+ 对于 Tutorial 可执行文件，我们希望将可执行文件和配置的头文件分别安装到 bin 和 include 目录中。

TODO 1：因此，在 MathFunctions/CMakeLists.txt 的末尾添加：

```cmake
set(installable_libs MathFunctions tutorial_compiler_flags)
if(TARGET SqrtLibrary)
  list(APPEND installable_libs SqrtLibrary)
endif()
install(TARGETS ${installable_libs} DESTINATION lib)
```
TODO 2：和

```cmake
	install(FILES MathFunctions.h DESTINATION include)
```

TODO 3,4：Tutorial 可执行文件和配置头文件的安装规则类似。 在顶层 CMakeLists.txt 的末尾添加：

```camke
install(TARGETS Tutorial DESTINATION bin)
install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  DESTINATION include
)
```

### Final

我们可以按一下步骤构建并安装

```shell
$ cmake ../Step5
$ cmake --build .
cmake --install . --prefix "./installdir"
-- Install configuration: ""
-- Installing: /home/zijianye/Learning/LearnCMake/cmake-3.31.2-tutorial-source/Step5_build/./installdir/lib/libMathFunctions.a
-- Installing: /home/zijianye/Learning/LearnCMake/cmake-3.31.2-tutorial-source/Step5_build/./installdir/lib/libSqrtLibrary.a
-- Installing: /home/zijianye/Learning/LearnCMake/cmake-3.31.2-tutorial-source/Step5_build/./installdir/include/MathFunctions.h
-- Installing: /home/zijianye/Learning/LearnCMake/cmake-3.31.2-tutorial-source/Step5_build/./installdir/bin/Tutorial
-- Installing: /home/zijianye/Learning/LearnCMake/cmake-3.31.2-tutorial-source/Step5_build/./installdir/include/TutorialConfig.h
```

 [`--install`](https://cmake.org/cmake/help/latest/manual/cmake.1.html#cmdoption-cmake-install) 运行安装步骤选项（在 3.15 中引入，旧版本的 CMake 必须使用 `make install`）。此步骤将安装相应的头文件、库和可执行文件。例如：
 
```shell
cmake --install .
```

> `cmake --install .` 这个命令则用于将编译好的结果（如可执行文件、库文件、头文件等）安装到指定的目录。

对于多配置工具，不要忘记使用 --config 参数指定配置。

```shell
cmake --install . --config Release
```

CMake 变量 `CMAKE_INSTALL_PREFIX` 用于确定安装文件的根目录。 如果使用 cmake --install 命令，安装前缀可以通过 --prefix 参数覆盖。 例如：

```cmake
cmake --install . --prefix "/home/myuser/installdir"
```

## Exercise 2 - Testing Support

CTest 提供了一种轻松管理项目测试的方法。 测试可以通过 `add_test()` 命令添加。 虽然本教程没有明确涉及，但 CTest 和其他测试框架（如 GoogleTest）之间有很多兼容性。

### Goal

使用 CTest 为我们的可执行文件创建单元测试。

### Files to Edit

+ `CMakeLists.txt`

### Getting Started

首先，我们需要启用测试。 接下来，开始使用 `add_test()` 为我们的项目添加测试。 我们将添加 3 个简单的测试，然后您可以根据需要添加其他测试。

### Solution

TODO 5：在顶层 CMakeLists.txt 文件的末尾，我们首先需要使用 enable_testing() 命令启用测试。

```cmake
enable_testing()
```

TODO 6：启用测试后，我们将添加一些基本测试，以验证应用程序是否正常运行。 首先，我们使用 `add_test()` 创建一个测试，在传入参数 25 后运行 Tutorial 可执行文件。 在这个测试中，我们不会检查可执行文件的计算答案。 本测试将验证应用程序是否运行、是否发生 segfault 或崩溃，以及返回值是否为零。 这是 CTest 测试的基本形式。

```cmake
add_test(NAME Runs COMMAND Tutorial 25)
```

TODO 7：接下来，让我们使用 `PASS_REGULAR_EXPRESSION` 测试属性来验证测试的输出是否包含特定字符串。 在本例中，我们要验证当提供的参数数量不正确时，是否会打印使用信息。

```cmake
add_test(NAME Usage COMMAND Tutorial)
set_tests_properties(Usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number"
  )
```

TODO 8：下一个测试将验证计算值是否真的是平方根。

```cmake
add_test(NAME StandardUse COMMAND Tutorial 4)
set_tests_properties(StandardUse
  PROPERTIES PASS_REGULAR_EXPRESSION "4 is 2"
  )
```
这一个测试还不足以让我们确信它对所有传入的值都有效。 我们应该添加更多测试来验证这一点。 为了方便添加更多测试，我们制作了一个名为 do_test 的函数，它可以运行应用程序并验证给定输入的平方根计算结果是否正确。 每次调用 do_test，都会在项目中添加另一个测试，并根据传递的参数提供名称、输入和预期结果。

```cmake
function(do_test target arg result)
  add_test(NAME Comp${arg} COMMAND ${target} ${arg})
  set_tests_properties(Comp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result}
    )
endfunction()

# do a bunch of result based tests
do_test(Tutorial 4 "4 is 2")
do_test(Tutorial 9 "9 is 3")
do_test(Tutorial 5 "5 is 2.236")
do_test(Tutorial 7 "7 is 2.645")
do_test(Tutorial 25 "25 is 5")
do_test(Tutorial -25 "-25 is (-nan|nan|0)")
do_test(Tutorial 0.0001 "0.0001 is 0.01")
```

导航至构建目录并重建应用程序。 然后运行 ctest 可执行文件：`ctest -N` 和 `ctest -VV`。 对于多配置生成器（如 Visual Studio），必须使用 `-C <mode>` 标志指定配置类型。 例如，要在调试模式下运行测试，可从构建目录（而不是调试子目录！）使用 `ctest -C Debug -VV`。 要在 Release 模式下运行测试，则应在同一目录下使用 `-C Release`。 或者，从集成开发环境构建 RUN_TESTS 目标。

### ctest -N 和 ctest -VV 的作用

- **ctest -N:**
    
    - **作用：** 显示将要运行的测试列表，但不实际执行。
    - **用途：**
        - 检查测试配置是否正确。
        - 验证测试用例是否被正确发现。
        - 在实际运行测试之前，对测试有一个整体的了解。
- **ctest -VV:**
    
    - **作用：** 以非常详细的模式运行测试，输出大量的调试信息。
    - **用途：**
        - 调试测试失败的原因。
        - 获取关于测试执行过程的详细信息。
        - 分析测试性能。

```shell
$ ctest -N
Test project /home/zijianye/Learning/LearnCMake/cmake-3.31.2-tutorial-source/Step5_build
  Test  #1: Runs
  Test  #2: Usage
  Test  #3: StandardUse
  Test  #4: Comp4
  Test  #5: Comp9
  Test  #6: Comp5
  Test  #7: Comp7
  Test  #8: Comp25
  Test  #9: Comp-25
  Test #10: Comp0.0001

Total Tests: 10
$ ctest -VV 
test 5
      Start  5: Comp9

5: Test command: /home/zijianye/Learning/LearnCMake/cmake-3.31.2-tutorial-source/Step5_build/Tutorial "9"
5: Test timeout computed to be: 10000000
5: Computing sqrt of 9 to be 5
5: Computing sqrt of 9 to be 3.4
5: Computing sqrt of 9 to be 3.02353
5: Computing sqrt of 9 to be 3.00009
5: Computing sqrt of 9 to be 3
5: Computing sqrt of 9 to be 3
5: Computing sqrt of 9 to be 3
5: Computing sqrt of 9 to be 3
5: Computing sqrt of 9 to be 3
5: Computing sqrt of 9 to be 3
5: The square root of 9 is 3
 5/10 Test  #5: Comp9 ............................   Passed    0.01 sec
```