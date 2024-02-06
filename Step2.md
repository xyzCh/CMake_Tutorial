# Step2：添加Library

此时我们已经看到了如何使用CMake创建一个基本项目。在这一步中，我们将学习如何在项目中创建和使用库，同时还将看到如何使库的使用成为可选的。

## 练习1 - 创建Library

要在CMake中创建一个库，使用`add_library()`命令并指定构成库的源文件。

我们可以使用一个或多个子目录组织项目，而不是把所有源文件放到一个目录下。在本例中，我们将专门为库创建一个子目录，然后添加一个新的`CMakeLists.txt`文件和一个或更过的源文件。在顶级的`CMakeLists.txt`文件中，我们使用`add_subdirectory()`命令把子目录添加到构建。

创建库之后，通过`target_include_directories()`和`target_link_libraries()`将它链接到我们的可执行目标。

### Goal

添加并使用库

### Helpful Resources

+ add_library()
+ add_subdirectory()
+ target_include_directories()
+ target_link_libraries()
+ PROJECT_SOURCES_DIR

### Files to Edit

+ CMakeLists.txt
+ tutorial.cxx
+ MathFunctions/CMakeLists.txt

### Getting Started

在本练习中，我们将添加一个包含我们自己实现的计算平方根的库，可执行文件可以使用这个库，而不是编译器提供的标准平方根函数。

本教程中，我们把库放进`MathFunctions`子目录中，此目录中已经包含了`MathFunctions.h`和`mysqrt.h`头文件和它们各自的源文件`MathFunctions.cxx`、`mysqrt.cxx`，但我们不需修改这些文件。`mysqrt.cxx`提供了一个名为`mysqrt`和编译器的`sqrt`功能类似的函数，`MathFunctions.cxx`包含了一个`sqrt`函数，用来隐藏`sqrt`实现细节。

从`Help/guid/tutorial/Step2`目录开始，完成`TODO1`到`TODO6`。

首先在`MathFunctions`子目录中的`CMakeLists.txt`文件中填写一行。  
接下来，编辑顶级的`CMakeLists.txt`文件。  
最后，在tutorial.cxx中使用新建的`MathFunctions`库。

### Build and Run

运行`cmake`可执行文件或`cmake-gui`去配置项目，然后使用您选择的构建工具构建它。

下面是对命令行的回顾:

```shell
mkdir Step2
cd Step2_build
cmake ../Step2
cmake --build .
```

尝试使用新建的`Tutorial`，并确保它仍然产生准确的平方根值。

### Solution

在`MathFunctions`目录下的`CMakeLists.txt`文件中，我们使用`add_library()`创建一个名为`MathFunctions`的库对象， 库的源文件作为参数传递给`add_library()`。看起来如下：

+ > `TODO1：`MathFunctions/CmakeLists.txt
    ```shell
    add_library(MathFunctions MathFunctions.cxx mysqrt.cxx)
    ```

为了使用新库，我们将在顶级`CMakeLists.txt`文件中添加·add_subdirectory()·调用，以便库将被构建。

+ > `TODO2：`CMakeLists.txt
    ```shell
    add_subdirectory(MathFunctions)
    ```

接下来，使用`target_link_libraries()`把库连接到可执行文件。

+ > `TODO3：`CMakeLists.txt
    ```shell
    target_link_libraries(Tutorial PUBLIC MathFunctions)
    ```

最后我们需要指定库的头文件位置。修改`target_include_directories()`来添加`MathFunctions`子目录作为一个include目录以便`MathFunctions.h`头文件可以被找到。

+ > `TODO4：`CMakeLists.txt
    ```shell
    target_include_directories(Tutorial PUBLIC
        "${PROJECT_BINARY_DIR}"
        "${PROJECT_SOURCE_DIR}/MathFunctions"
    )
    ```
现在让我们使用库，在`tutorial.cxx`include`MathFunctions.h`：

+ > `TODO5：`tutorial.cxx
    ```c++
    #include "MathFunctions.h"
    ```
最后，将`sqrt`替换为包装函数`mathfunctions::sqrt`。

+ > `TODO6：`tutorial.cxx
    ```c++
    const double outputValue = mathfunctions::sqrt(inputValue);
    ```

## 练习2 - 添加选项

现在让我们在MathFunctions库中添加一个选项，允许开发者使用自定义平方根实现或内置的标准实现。虽然对于教程来说，确实没有必要这样做，但对于大型项目来说，这是一种常见的情况。

CMake可以使用`option()`命令执行此操作，这为用户提供了一个变量，可以在配置他们的CMake构建时更改。
这个设置将被存储在缓存中，这样用户就不需要每次在构建目录上运行CMake时都设置这个值。

### Goal

添加不使用`MathFunctions`的构建选项。

### Helpful Resources

+ if()
+ option()
+ target_compile_definitions()

### Files to Edit

+ MathFunctions/CMakeLists.txt
+ MathFunctions/MathFunctions.cxx

### Getting Started

从练习1的文件开始，完成`TODO7`到`TODO14`。

首先使用`option()`命令在`MathFunctions/CMakeLists.txt`创建一个`USE_MYMATH`变量，在同一个文件中，使用该选项将编译定义传递给`MathFunctions`库。

然后更新`MathFunctions.cxx`基于`USE_MYMATH`重定向编译。

最后，当`USE_MYMATH`打开时，通过在`MathFunctions/CMakeLists.txt`的`USE_MYMATH`块中构建自己的库，来组织`mysqrt.cxx`被编译。

### Build and Run

由于我们已经在练习1中配置了构建目录，我们可以通过简单地调用以下命令进行重建：
```shell
cd ../Step2_build
cmake --build .
```

接着在几个数字上运行`Tutorial`可执行文件，以验证它仍然正确。

现在让我们把`USE_MYMATH`更新为`OFF`，最简单的方法是使用`cmake-gui`，如果您在终端中则使用`ccmake`(终端中的图形界面)。或者，如果你想从命令行更改选项，尝试:

```shell
cmake ../Step2 -DUSE_MYMATH=OFF
```

现在，用下面的代码重新构建：

```shell
cmake --build .
```

然后，再次运行可执行文件，以确保它在USE_MYMATH设置为OFF时仍然工作。哪个函数给出更好的结果，sqrt还是mysqrt?

### Solution

第一步是添加一个选项到`MathFunctions/CMakeLists.txt`。该选项将显示在`cmake-gui`和`ccmake`中，默认值为`ON`，可由用户更改。

+ > `TODO7：`MathFunctions/CMakeLists.txt
    ```shell
    options(USE_MYMATH "Use tutorial provided math implementation" ON)
    ```

接下来，使用这个新选项使库与`mysqr`t函数的构建和链接成为有条件的。

创建一个检查`USE_MYMATH`的`if()`语句，在`if()`块中，将`target_compile_definitions()`命令和编译定义`USE_MYMATH`放在一起。

+ > `TODO8：`MathFunctions/CMakeLists.txt
    ```shell
    if (USE_MYMATH)
        target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")
    endif()
    ```

当`USE_MYMATH`为`ON`时，编译定义`USE_MYMATH`将被设置。然后，我们可以使用这个编译定义来启用或禁用部分源码。

对源代码的相应更改相当简单。在`MathFunctions.cxx`，我们让`USE_MYMATH`控制使用哪个平方根函数:

+ > `TODO9：`MathFunctions/MathFunctions.cxx
    ```c++
    #ifdef USE_MYMATH
        return detail::mysqrt(x);
    #else
        return std::sqrt(x);
    #endif
    ```
接下来，如果`USE_MYMATH`已定义，我们需要include`mysqrt.h`。

+ > `TODO10：`MathFunctions/MathFunctions.cxx
    ```c++
    #ifdef USE_MYMATH
        #include "mysqrt.h"
    #endif
    ```

最后，在使用`std::sqrt`时，我们需要include`cmath`。

+ > `TODO11：`MathFunctions/MathFunctions.cxx
    ```c++
    #include <cmath>
    ```

此时，如果`USE_MYMATH`为`OFF`，`mysqrt.cxx`没有使用但是`mysqrt.cxx`仍会被编译，因为`mysqrt.cxx`列在`MathFunctions`目标的源文件下。

有几个方法可以解决这个问题，第一个选项是在`USE_MYMATH`块中使用`target_sources`添加`mysqrt.cxx`。另一个选项是在`USE_MYMATH`块中创建一个额外的库，负责编译`mysqrt.cxx`。在本教程中，我们将创建一个额外的库。

首先，在`USE_MYMATH`中创建一个名为`SqrtLibrary`的库，其源代码为`mysqrt.cxx`。

+ > `TODO12：`MathFunctions/CMakeLists.txt
    ```shell
    add_library(SqrtLibrary STATIC
        mysqrt.cxx
    )
    ```

接下来，当`USE_MYMATH`启用时，我们把`SqrtLibrary`链接到`MathFunctions`。

+ > `TODO13：`MathFunctions/CMakeLists.txt
    ```shell
    target_link_libraries(MathFunctions PRIVATE SqrtLibrary)
    ```

最后，我们可以从`MathFunctions`库的源文件列表中移除`mysqrt.cxx`，因为它将在包含`SqrtLibrary`时被拉入。

+ > `TODO14：`MathFunctions/CMakeLists.txt
    ```shell
    add_library(MathFunctions MathFunctions.cxx)
    ```

通过这些更改，`mysqrt`函数现在对于正在构建和使用`MathFunctions`库的人来说完全是可选的。用户可以切换`USE_MYMATH`来操纵构建中使用的库。