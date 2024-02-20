# Step5：安装和测试

## 练习1 - 安装规则

通常，仅仅构建可执行文件是不够的，它还应该是可安装的。通过CMake，我们可以使用`install()`命令指定安装规则，在CMake中支持本地安装通常就像指定安装位置和要安装的目标和文件一样简单。

### Goal

安装`Tutorial`可执行文件和`MathFunctions`库。

### Helpful Materials

+ install()

### Files to Edit

+ MathFunctions/CMakeLists.txt
+ CMakeLists.txt

### Getting Started

`Step5`目录中提供了起始代码，在本练习中,完成`TODO1`到`TODO4`。

首先，更新`MathFunctions/CMakeLists.txt`将`MathFunctions`和`tutorial_compiler_flags`库安装到`lib`目录。在同一个文件中，指定将`MathFunctions.h`安装到`include`目录所需的安装规则。

然后，更新顶级`CMakeLists.txt`将`Tutorial`可执行文件安装到`bin`目录。最后，所有头文件都应该安装到`include`目录中。请记住，`TutorialConfig.h`位于`PROJECT_BINARY_DIR`中。

### Build and Run

新建一个`Step5_build`目录。运行`cmake`可执行文件或`cmake-gui`来配置项目，然后使用您选择的构建工具构建它。

然后，从命令行中使用`cmake`命令的`--install`选项执行安装步骤(3.15引入，旧版本的CMake必须使用`cmake install`)。这一步将安装适当的头文件、库和可执行文件：

```shell
cmake --install .
```

对于多配置工具，不要放及使用`--config`参数来指定配置：

```shell
cmake --install . --config Release
```

如果使用IDE，只需构建`INSTALL`目标。您可以从命令行构建相同的安装目标，如下所示:

```shell
cmake --build . --target install --config Debug
```

CMake变量`CMAKE_INSTALL_PREFIX`用于确定文件安装的根目录。如果使用`cmake --install`命令，安装前缀可以通过`--prefix`参数覆盖：

```shell
cmake --install . --prefix "/home/myuser/installdir"
```

导航到安装目录，并验证安装的`Tutorial`。

### Solution

我们项目的安装规则相当简单：

+ 对于`MathFunctions`，我们希望将库文件和头文件分别安装到`lib`和`include`目录。
+ 对于`Tutorial`可执行文件，我们希望将可执行文件和配置头文件分别安装到`bin`和`include`目录。

因此，在`MathFunctions/CMakeLists.txt`末尾添加：

+ > `TODO1：`MathFunctions/CMakeLists.txt
    ```cmake
    set(installable_libs MathFunctions tutorial_compiler_flags)
    if(TARGET SqrtLibrary)
        list(APPEND installable_libs SqrtLibrary)
    endif()
    install(TARGETS ${installable_libs} DESTINATION lib)
    ```

+ > `TODO2：`MathFunctions/CMakeLists.txt
    ```cmake
    install(FILES MathFunctions.h DESTINATION include)
    ```

`Tutorial`可执行文件和配置头文件的安装规则类似。在顶级`CMakeLists.txt`尾部添加：

+ > `TODO3,4：`CMakeLists.txt
    ```cmake
    install(TARGETS Tutorial DESTINATION bin)
    install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
        DESTINATION include
    )
    ```

这就是创建`tutorial`基本本地安装所需的全部内容。

## 练习2 - 测试支持

CTest提供了一种轻松管理项目测试的方法，可以通过`add_test()`命令添加测试。尽管在本教程中没有明确介绍，但`CTest`和其他测试框架(如`GoogleTest`)之间有很多兼容性。

### Goal

使用CTest为可执行文件创建单元测试。

### Helpful Materials

+ enable_testing()
+ add_test()
+ function()
+ set_tests_properties()
+ ctest

### Files to Edit

+ CMakeLists.txt

### Getting Started

`Step5`目录中提供了起始代码，在本练习中,完成`TODO5`到`TODO9`。

首先，我们需要开启测试，接下来使用`add_test()`向项目添加测试。我们将添加3个简单的测试，然后您可以添加您认为合适的其它测试。

### Build and Run

导航到构建目录并重新构建应用，然后运行**ctest**可执行文件：`ctest -N`和`ctest -VV`。对于多配置生成器(如Visual Studio)，配置类型必须通过`-C <mode>`标志指定。
例如：要在Debug模式下运行测试，请从构建目录(不是Debug子目录!)使用`ctest -C Debug -VV`。Release模式将从相同的位置执行，但使用`-C Release`。或者，从IDE构建`RUN_TESTS`目标。

### Solution

让我们测试一下应用程序。在顶级`CMakeLists.txt`文件的末尾，我们首先需要使用`enable_testing()`命令启用测试。

+ > `TODO5：`CMakeLists.txt
    ```cmake
    enable_testing()
    ```

开启测试后，我们将添加一些基本测试来验证应用程序是否正常工作。首先，我们使用`add_test()`创建一个测试，它运行`Tutorial`可执行文件，并传入参数25。对于这个测试，我们不打算检查可执行程序的计算答案。此测试将验证应用程序是否运行，没有出现分段故障或以其他方式崩溃，并且返回值为零。这是CTest测试的基本形式。

+ > `TODO6：`CMakeLists.txt
    ```cmake
    add_test(NAME Runs COMMAND Tutorial 25)
    ```

接下来，让我们使用`PASS_REGULAR_EXPRESSION`测试属性来验证测试的输出是否包含某些字符串。在本例中，验证当提供的参数数量不正确时是否打印usage消息。

+ > `TODO7：`CMakeLists.txt
    ```cmake
    add_test(NAME Usage COMMAND Tutorial)
    set_tests_properties(Usage
        PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number"
    )
    ```

我们将添加的下一个测试验证计算值确实是平方根。

+ > `TODO8：`CMakeLists.txt
    ```cmake
    add_test(NAME StandardUse COMMAND Tutorial 4)
    set_tests_properties(StandardUse
        PROPERTIES PASS_REGULAR_EXPRESSION "4 is 2"
    )
    ```

这一个测试还不足以让我们确信它对传入的所有值都有效，我们应该添加更多的测试去验证它。为了方便的添加更多测试，我们创建了一个名为`do_test`的函数，此函数运行应用程序并验证给定输入的计算平方根否正确。对于`do_test`的每次调用，另一个带有名称、输入和基于传递的参数的预期结果的测试被添加到项目中。

+ > `TODO9：`CMakeLists.txt
    ```cmake
    function(do_test target arg result)
        add_test(NAME Comp${arg} COMMAND ${target} ${arg})
        set_tests_properties(Comp${arg}
            PROPERTIES PASS_REGULAR_EXPRESSION "${arg} is ${result}"
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