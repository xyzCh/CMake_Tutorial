# Step1: 基础指引

应该从哪里开始学习CMake？这一步将介绍一些CMake的`基础语法`，`命令`和`变量`。随着这些概念的介绍，我们将通过三个练习，并创建一个简单的CMake项目。

本步骤中的每个练习都将从一些背景信息开始，然后提供一个`Goal`和`Helpful Resources`。`Files to Edit`章节中的每个文件都位于`Step1`目录中，并包含一个或多个`TODO`注释。每个`TODO`表示需要修改或添加一到两行代码。`TODO`要按数字顺序完成，先完成`TODO1`,然后`TODO2`，以此类推。`Getting Started`章节将提供一些有用的提示知道你完成练习。然后`Build and Run`章节将逐步介绍如何和构建和测试练习。最后，在每个练习结束时讨论预期的`Solution`。

还要注意，本教程中的每一步都建立在上一步的基础上。例如，`Step2`的起始代码是`Step1`的完整解决。

## 练习1 - 创建一个基础项目

最基础的CMake项目是从单个源文件构建可执行文件。对于这样的简单项目，只需要一个包含三个命令的`CMakeLists.txt`文件

> **注意：**  
> 尽管CMake命令支持大写、小写和大小写混合，但小写命令作为首选并在整改教程中使用。

任何一个项目的CMakeLists.txt最顶部必须使用`cmake_minimum_required()`命令指定CMake的最小版本。这将建立策略设置，并确保接下来的CMake函数使用兼容版本的CMake运行。

要开始一个项目，我们使用`project()`命令去设置项目名称。每个项目都需要调用此命令，并且应该在`cmake_minimum_requred()`之后不久调用。稍后我们将会看到，此命令还可以用于指定其它的项目级别的信息，例如语言和版本号。

最后，`add_executable()`命令告诉CMake使用指定的源文件去创建一个可执行文件。

### Goal

理解如何去创建一个简单的CMake项目

### Helpful Resources

+ add_executable()
+ cmake_minimum_required()
+ project()

### Files to Edit

+ CMakeLists.txt

### Getting Started

源码`tutorial.cxx`位于Help/guid/tutorial/Step1目录，用于计算一个数的平方根。此文件在当前步骤不需要修改。

同一目录下`CMakeLists.txt`文件中的`TODO1`到`TODO3`需要你去完成。

### Build and Run

一旦完成了`TODO1`到`TODO3`，我们就准备好构建和运行我们的项目了！首先，运行`cmake`可执行文件或`cmake-gui`来配置项目，然后使用你选择的构建工具去构建。

例如，我们可以从命令行导航到CMake源码树的`Help/guide/tutorial`目录，并创建一个构建目录：

```shell
mkdir Step1_build
```

接着导航到构建目录，然后运行`cmake`来配置项目，并生成一个本地构建系统：

```shell
cd Step1_build
cmake ../Step1
```

然后调用构建系统来编译/链接项目：

```shell
cmake --build .
```

对于多配置生成器（例如Visual Studio），首先导航到适当的子目录，例如：

```shell
cd Debug
```

最后，尝试使用新构建的`Tutorial`：

```shell
Tutorial 4294967296
Tutorial 10
Tutorial
```

> **注意：**  
> 根据不同的shell，正确的语法可能是`Tutorial`、`./Tutorial`或`.\Tutorial`。为了简单，练习中将始终使用`Tutorial`。

### Solution

如上所述，一个三行的`CMakeLists.txt`文件是我们启动和运行的所有内容。如下所示，第一行我们使用`cmake_minimum_requred()`去设置CMake版本：
  
+ > `TODO1`： CMakeLists.txt
    ```shell
    cmake_minimum_required(VERSION 3.10)
    ```

下一步创建基本工程，使用`project()`命令设置工程名称，如下所示：

+ > `TODO2`：CMakeLists.txt
    ```shell
    project(Tutorial)
    ```
    
最后一个命令为`add_executable()`，如下所示：

+ > `TODO3`：CMakeLists.txt
    ```shell
    add_executable(Tutorial tutorial.cxx)
    ```

## 练习2 - 指定 C++标准

CMake有一些特殊变量，这些变量要么是在后台创建的，要么是在项目代码中设置的。这些变量多以`CMAKE_`开头。在为项目创建变量时应避免这种命名约定。其中两个特殊的用户可设置的变量为`CMAKE_CXX_STANDARD`和`CMAKE_CXX_STANDARD_REQUIRED`，它们可以一起用于指定构建项目所需的C++标准。

### Goal

添加一个需要C++11的特性

### Helpfual Resources

+ CMAKE_CXX_STANDARD
+ CMAKE_CXX_STANDARD_REQUIRED
+ set()

### Files to Edit

+ CMakeLists.txt
+ tutorial.cxx

### Getting Started

继续编辑Step1目录中的文件，完成`TODO4`到`TODO6`。

首先，编辑`tutorial.cxx`添加一个需要C++11的特性，然后更新`CMakeLists.txt`去要求C++11。

### Build and Run

再次构建我们的项目，由于之前在步骤1已经创建了构建目录并执行了CMake，我们可以跳到构建步骤：

```shell
cd Step1_build
cmake --build .
```

现在我们尝试使用之前相同的命令使用新构建的`Tutorial`：

```shell
Tutorial 4294967296
Tutorial 10
Tutorial
```

### Solution

我们通过在`tutorial.cxx`文件中将`atof替换为`std::stod`，来为我们的项目添加一些C++特性。如下所示：

+ > `TODO4`：tutorial.cxx
    ```c++
    const double inputValue = std::stod(argv[1]);
    ```

要完成`TODO5`，只需简单删除`#include <cstdlib>`。

我们需要在CMake代码中明确声明它应该使用正确的标志。在CMake中启用对特定C++标准支持的一种方法是使用`CMAKE_CXX_STANDARD`变量。对于本教程，在`CMakeLists.txt`中将`CMake_CXX_STANDARD`设置为`11`,`CMAKE_CXX_STANDARD_REQUIRED`设置为`True`。确保`在add_executable()`调用的上方添加`CMAKE_CXX_STANDARD`声明。

+ > `TODO6`：CMakeLists.txt
    ```shell
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED True)
    ```

## 练习3 - 添加版本号和配置头文件

有时候让在`CMakeLists.txt`文件中定义的变量在源码中可用可能是有用的。在这种情况下，我们希望打印项目版本。

实现这一目标的一种方法是使用配置好的头文件。我们创建一个输入文件，其中包含一个或多个要替换的变量，这些变量具有类似`@VAR@`的特殊语法。然后，使用`configure_file()`命令来复制输入文件到一个指定的输出文件并用`CMakeLists.txt`文件中的`VAR`的当前值替换这些变量。

虽然可以直接在源代码中编辑版本，但使用这个特性是首选的，因为它创建了一个单一的事实来源，避免了重复。

### Goal

定义并报告项目的版本号。

### Helpful Resources

+ \<PROJECT-NAME\>_VERSION_MAJOR
+ \<PROJECT-NAME\>_VERSION_MINOR
+ configure_file()
+ target_include_directories()

### Files to Edit

+ CMakeLists.txt
+ tutorial.cxx

### Getting Started

继续从步骤1编辑文件，完成`TODO7`到`TODO12`。在这个练习中，我们首先在`CMakeLists.txt`中添加一个项目版本号。在同一个文件中，使用`configure_file()`将给定的输入文件复制到一个输出文件，并在输入文件内容中替换一些变量值。

接下来，创建一个输入头文件`TutorialConfig.h.in`定义版本号，它将接受从`configure_file()`传递的变量。

最后，更新`tutorial.cxx`打印其版本号。

### Build and Run

让我们再次构建项目，和之前一样，我们已经创建了一个构建目录并运行CMake，这样我们就可以跳转到构建步骤:

```shell
cd Step1_build
cmake --build .
```

验证在不带任何参数运行可执行文件时是否报告了版本号。

### Solution
在本练习中，我们通过打印版本号来改进可执行文件。虽然可以只在源代码中这样做，但使用`CMakeLists.txt`可以让我们维护版本号的单一数据源。

首先，我们修改`CMakeLists.txt`文件，使用`project()`命令设置项目名称和版本号。当`project()`命令被调用时，CMake在后台定义`Tutorial_VERSION_MAJOR`和`Tutorial_VERSION_MINOR`。

+ > `TOOD7`：CMakeLists.txt
    ```shell
    project(Tutorial VERSION 1.0)
    ```

然后我们使用`configure_file()`复制输入文件，替换指定的CMake变量：

+ > `TODO8`：CMakeLists.txt
    ```shell
    configure_file (TutorialConfig.h.in TutorialConfig.h)
    ```

由于配置的文件将被写入项目二进制目录，我们必须将该目录添加到搜索头文件的路径列表中。

> **注意:**  
> 在本教程中，我们将交替使用项目构建和项目二进制目录，它们是相同的，但并不是指bin/目录

我们使用`target_include_directories()`来指定可执行目标应该在哪里查找头文件。

+ > `TODO9`: CMakeLists.txt
    ```shell
    target_include_directories(Tutorial PUBLIC "${PROJECT_BINARY_DIR}")
    ```

`TutorialConfig.h.in`是要配置的输入头文件。当`configure_file()`从`CMakeLists.txt`调用时，在`TutorialConfig.h`文件中`@Tutorial_VERSION_MAJOR@`和`@Tutorial_VERSION_MINOR@`的值将被替换为项目相关的版本号。

+ > `TOTO10`: TutorialConfig.h.in
    ```c++
    // the configured options and settings for Tutorial
    #define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
    #define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
    ```

接下来，我们需要修改`tutorial.cxx`去include配置的`TutorialConfig.h`头文件.

+ > `TOTO11`: TutorialConfig.h.in
    ```c++
    #include "TutorialConfig.h"
    ```

最后，我们通过更新`tutorial.cxx`打印出可执行文件名和版本号，如下:

+ > `TOTO12`: TutorialConfig.h.in
    ```c++
    if (argc < 2) {
    // report version
    std::cout << argv[0] << " Version " << Tutorial_VERSION_MAJOR << "."
              << Tutorial_VERSION_MINOR << std::endl;
    std::cout << "Usage: " << argv[0] << " number" << std::endl;
    return 1;
  }
    ```