# Step3：为Libary添加使用需求

## 练习1 - 为Libary添加使用需求

目标参数的使用要求允许对库或可执行文件的链接和include进行更好的控制，同时也允许对CMake中目标的传递属性进行更多的控制。利用使用需求的主要命令是：

+ target_compile_definitions()
+ target_compile_options()
+ target_include_directories()
+ target_link_directories()
+ target_link_options()
+ target_precompile_headers()
+ target_sources()

### Goal

为Library添加使用需求

### Helpful Materials

+ CMAKE_CURRENT_SOURCE_DIR

### Files to Edit

+ MathFuctions/CMakeLists.txt
+ CMakeLists.txt

### Getting Started

在本练习中，我们将从[添加Library](Step2.md#step2-添加library)重构代码，以使用更现代的CMake技术。我们将让库定义自己的使用需求，以便在必要时传递给其它目标。这种情况下，`MathFunctions`库将指定任何它需要的include目录，然后消费目标`Tutorial`只需简单链接到`MathFunctions`，并不需要关心任何额外的include目录。

启动源代码在`Step3`目录中提供。在这个练习中，完成`TODO1`到`TODO3`。

首先，在`MathFunctions/CMakeLists`中添加一个`target_include_directories()`调用。记住，`CMAKE_CURRENT_SOURCE_DIR`是当前正在处理的源目录的路径。

然后，更新（和简化）顶级`CMakeLists.txt`中的`target_include_directories()`调用。

### Build and Run

新建一个`Step3_build`目录，运行`cmake`可执行文件或`cmake-gui`来配置项目，然后从构建目录使用你选择的构建工具或`cmake --build .`去构建它。下面是对命令行的回顾：

```shell
mkdir Step3_build
cd Step3_build
cmake ../Step3
cmake --build .
```

接下来，使用新构建的`Tutorial`并验证它是否按预期工作。

### Solution

让我们更新上一步的代码，以使用现代的`CMake`方法来处理使用需求。

我们想声明，任何链接到`MathFunctions`的人都需要包含当前源目录，而`MathFunctions`自身不需要，这可以用`INTERFACE`使用需求来表示。记住，`INTERFACE`意味着消费者需要而生产者不需要的东西。

在`MathFunctions/CMakeLists.txt`末尾，使用`target_include_directories()`和`INTERFACE`关键字，如下:

+ > `TODO1：`MathFunction/CMakeLists.txt
    ```shell
    target_include_directories(MathFunctions
        INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
    )
    ```

现在我们为`MathFunctions`指定了使用需求，我们可以安全的从顶级`CMakeLists.txt`中删除对`EXTRA_INCLUDES`变量的使用。

删除此行：

+ > `TODO2：` 'CMakeLists.txt'
    ```shell
    list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
    ```

从`target_include_directories()`中删除`EXTRA_INCLUDE`：

+ > `TODO3：`CMakeLists.txt
    ```shell
    target_include_directories(Tutorial PUBLIC
        "${PROJECT_BINARY_DIR}"
    )
    ```

注意，通过这个技术，我们的可执行目标使用我们的库所做的唯一事情就是使用目标库的名称调用`target_link_libraries()`。在大的项目中，手动指定库依赖项的经典做法很快就变得非常复杂。

## 练习2 - 通过Interface Library设置C++标准

既然我们已经将代码转换为更现代的方法，那么让我们演示一种为多个目标设置属性的现代技术。

让我们重构现有代码以使用`INTERFACE`库。我们将在下一步中使用该库来演示`generator expressions`的常见用法。

### Goal

添加一个`INTERFACE`库对象来指定所需的c++标准。

### HelpFul Resources

+ add_library()
+ target_compile_features()
+ target_link_libraries()

### Files to Edit

+ CMakeLists.txt
+ MathFunctions/CMakeLists.txt

### Getting Started

在本练习中，我们将重构代码，使用`INTERFACE`库指定c++标准。

从我们从练习1结束时留下的内容开始这个练习。完成`TODO4`到`TODO 7`。

从编辑顶级`CMakeLists.txt`文件开始，构造一个名为`tutorial_compiler_flags`的库对象，并指定`cxx_std_11`作为目标编译器特性。

修改`CMakeLists.txt`和`MathFunctions/CMakeLists.txt`，使所有目标都有一个`tutorial_compiler_flags`的`target_link_libraries()`调用。

### Build and Run

由于我们已经在练习1中配置了构建目录，只需通过调用以下命令重新构建代码：

```shell
cd Step3_build
cmake --build .
```

接下来，使用新构建的教程并验证它是否按预期工作。

### Solution

让我们更新上一步的代码，使用`INTERFACE`库来设置c++需求。

首先，我们需要删除变量`CMAKE_CXX_STANDARD`和`CMAKE_CXX_STANDARD_REQUIRED`上的两个`set()`调用。具体要删除的行如下:

+ > CMakeLists.txt
    ```shell
    CMakeLists.txt¶
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED True)
    ```

接下来我们需要创建一个名为`tutorial_comilper_flags`的`INTERFACE`库，然后使用`target_compile_features()`来添加`cxx_std_11`编译器特性。

+ > `TODO4：`CMakeLists.txt
    ```shell
    add_library(tutorial_compiler_flags INTERFACE)
    target_compile_features(tutorial_compiler_falgs INTERFACE cxx_std_11)
    ```

最后，设置好`INTERFACE`库，我们需要连接执行`Tutorial`、`SqrtLibrary`库、`MathFunctions`库连接到新的`tutorial_compiler_flags`库，代码分别如下。

+ > `TODO5：`CMakeLists.txt
    ```shell
    target_link_libraries(Tutorial PUBLIC MathFunctions tutorial_compiler_flags)
    ```

+ > `TODO6：`MathFunctions/CMakeLists.txt
    ```shell
    target_link_libraries(SqrtLibrary PUBLIC tutorial_compiler_flags)
    ```

+ > `TODO7：`CMakeLists.txt
    ```shell
    target_link_libraries(MathFunctions PUBLIC tutorial_compiler_flags)
    ```

自此，我们的代码需要C++11构建。注意，使用这种方法，我们可以明确哪些目标需要特定的需求。另外，我们在接口库中创建了一个单一的事实来源。