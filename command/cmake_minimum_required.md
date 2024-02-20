# cmake_minimum_reqired

指定cmake的最低版本。

```cmake
cmake_minimum_required(VERSION <min>[...<policy_max>] [FATAL_ERROR])
```

*New in Version 3.12：* 可选的`<policy_max>`版本。

为项目设置cmake最低要求版本，还可以更新策略设置，如下所述。

`<min>`和可选的`<policy_max>`都是`major.minor[.patch[.tweak]]`形式的CMake版本号，而`...`就是字面意义的`...`。

如果运行的CMake版本小于要求的版本`<min>`，它将停止处理项目并报错。如果指定了可选`<policy_max>`版本，它至少是`<min>`版本，并且影响[Policy Settings](#policy-settings)中所述的策略设置。如果CMake的运行版本小于3.12，额外的`...`将本是为版本部分的分隔符，结果是`...<max>`部分被忽略，保留3.12之前基于`<min>`的策略行为。

此命令将变量[CMAKE_MININUM_REQUIRED_VERSION]()的值设置为`<min>`。

`FATAL_ERROR`选项可以被CMake 2.6及更高版本接受但忽略。当CMake版本为2.4或更低时因该指定它，因为此时失败时会报错，而不仅仅时一个警告。

> **注意：** 在顶级`CMakeLists.txt`文件开头，调用[project()]()命令之前调用`cmake_minimum_required()`命令。在调用可能影响其行为的其他命令之前，建立版本和策略设置非常重要。参见策略[CMP0000]()。  
>
> 在[function()]()中调用`cmake_minimum_required()`会将某些影响限制在函数范围。例如，`CMAKE_MINIMUM_REQUIRED_VERSION`变量不会在调用范围设置，函数不会引入自己的策略范围，因此调用者的策略设置将会被影响。由于这种影响和不影响调用范围的事情混在一起，通常不鼓励在函数中调用`cmake_minimum_required()`。

# Policy Settings

`cmake_minimum_required(VERSION)`命令隐式调用`cmake_policy(VERSION)`命令来指定当前项目代码是为给定范围的CMake版本编写的。