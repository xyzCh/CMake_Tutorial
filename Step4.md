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