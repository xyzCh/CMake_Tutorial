# Step4：添加Generator Expressions

`Generator Expressions`在构建系统生成期间求值，医生称特定于每个构建配置的信息。

在许多目标属性的上下文中都允许使用`Generator Expressions`，如`LINK_LIBRARIES`, `INCLUDE_DIRECTORIES`, `COMPILE_DEFINITIONS`等。它们也可以在使用命令填充这些属性时使用，例如`target_link_libraries()`、`target_include_directories()`、`target_compile_definitions()`等。

`Generator expressions`也可以用于启用条件链接，编译时使用条件定义，条件包含目录等。条件可以基于构建配置、目标属性、平台信息或任何其他可查询的信息。

`Generator expressions`具有不同的类型，包括逻辑表达式、信息表达式和输出表达式。

逻辑表达式用于创建条件输出。基本表达式是`0`和`1`表达式，`$<0:...>`结果为空字符串，`$<1:...>`的结果为`...`，它们也可以嵌套。

## 练习1 - 使用Generator Expressions添加编译器警告标志

`Generator expressions`的一个常见用法是有条件的添加编译器标志，例如语言级别或警告。一个不错的模式是将此信息关联到一个INTERFACE目标，从而允许此信息传播。

### Goal

在构建时添加编译器警告标志，但不针对已安装的版本。

### Helpful Resources

+ cmake-generator-expressions(7)
+ cmake_minimum_required()
+ set()
+ target_compile_options()

### Files to Edit

+ CMakeLists.txt

### Getting Started

打开文件`Step4/CMakeLists.txt`并完成`TODO1`到`TODO4`。

首先，在顶级`CMakeLists.txt`文件中，我们需要把`cmake_minimum_required()`设置为`3.15`。在本练习中我们将使用generator expression，它是在CMake 3.15中引入的。

接下来，我们添加我们项目所需要的编译器警告标志，由于警告标志因编译器而异，我们使用`COMPILE_LANG_AND_ID`生成器表达式来控制给定语言和一组编译器id时应用哪些标志。

### Build and Run

新建一个`Step4_build`目录，运行`cmake`可执行文件或`cmake-gui`去配置项目，然后从构建目录使用你选择的构建工具或`cmake --build .`去构建它。

```shell
mkdir Step4_build
cd Step4_build
cmake ../Step4
cmake --build .
```

### Solution

更新`cmake_minimum_required()`以至少要求CMake版本3.15:

+ > `TODO1：`CMakeLists.txt
    ```cmake
    cmake_minimum_required(VERSION 3.15)
    ```

接下来，我们确定系统当前正在使用哪个编译器进行构建，因为警告标志会根据我们使用的编译器而变化。这是通过`COMPILE_LANG_AND_ID`生成器表达式完成的,我们在变量`gcc_like_cxx`和`msvc_cxx`中设置结果如下：

+ > `TODO2：`CMakeLists.txt
    ```cmake
    set(gcc_like_cxx "$<COMPILE_LANG_AND_ID:CXX,ARMClang,AppleClang,Clang,GNU,LCC>")
    set(msvc_cxx "$<COMPILE_LANG_AND_ID:CXX,MSVC>")
    ```

接下来，我们添加项目所需的编译器警告标志。使用变量`gcc_lick_cxx`和`msvc_cxx`，我们可以使用另一个生成器表达式，仅在变量为true时应用各自的标志。我们使用`target_compile_options()`将这些标志应用于Interface库。

+ > `TODO3：`CMakeLists.txt
    ```cmake
    target_compile_options(tutorial_compiler_flags INTERFACE
        "$<${gcc_like_cxx}:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>"
        "$<${msvc_cxx}:-W3>"
    )
    ```

最后，我们只希望在构建时使用这些标志。已安装项目的使用者不应该继承我们的警告标志。为了指定这一点，我们使用`BUILD_INTERFACE`条件将来自TODO3的标志包装在生成器表达式中。完整代码如下所示：

+ > `TODO4：`CMakeLists.txt
    ```cmake
    target_compile_options(tutorial_compiler_flags INTERFACE
        "$<${gcc_like_cxx}:$<BUILD_INTERFACE:-Wall;-Wextra;-Wshadow;-Wformat=2;-Wunused>>"
        "$<${msvc_cxx}:$<BUILD_INTERFACE:-W3>>"
    )
    ```