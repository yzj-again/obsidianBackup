---
date: 2024-12-16
tags:
  - cmake
---
向仪表板提交测试结果的支持非常简单。 我们已经在 "测试支持 "中为项目定义了大量测试。 现在我们只需运行这些测试并将其提交给 CDash。

## Exercise 1 - Send Results to a Testing Dashboard

### Goal

用 CDash 显示我们的 CTest 测试结果。

### Files to Edit

+ `CMakeLists.txt`

### Getting Started

在本练习中，请完成 CMakeLists.txt 顶层的 TODO 1，加入 CTest 模块。 这将启用 CTest 测试以及向 CDash 提交仪表板，因此我们可以放心地移除对 `enable_testing()` 的调用。

我们还需要获取 CTestConfig.cmake 文件，并将其放置在顶层目录中。 运行时，ctest 可执行文件将读取该文件，以收集有关测试仪表板的信息。 它包含：

+ 项目名称
+ 项目 “Nightly” 开始时间
+ CDash 实例的 URL，提交生成的文件将被发送到该 URL

本教程使用的是公共 dashboard 服务器，其相应的 CTestConfig.cmake 文件位于本步骤的根目录中。 在实际操作中，该文件将从用于托管测试结果的 CDash 实例上的项目设置页面下载。 从 CDash 下载后，不应在本地修改该文件。

```cmake
set(CTEST_PROJECT_NAME "CMakeTutorial")
set(CTEST_NIGHTLY_START_TIME "00:00:00 EST")

set(CTEST_DROP_METHOD "http")
set(CTEST_DROP_SITE "my.cdash.org")
set(CTEST_DROP_LOCATION "/submit.php?project=CMakeTutorial")
set(CTEST_DROP_SITE_CDASH TRUE)
```

### Solution

在这一步中，唯一需要修改的 CMake 代码是在顶层 CMakeLists.txt 中加入 CTest 模块，以启用向 CDash 提交仪表盘的功能：

```cmake
include(CTest)
```

要创建一个简单的测试仪表板，请运行 cmake 可执行文件或 cmake-gui 配置项目，但先不要编译。 相反，请导航至构建目录并运行：

```cmake
ctest [-VV] -D Experimental
```

请记住，对于多配置生成器（例如 Visual Studio），必须指定配置类型：

```cmake
ctest [-VV] -C Debug -D Experimental
```