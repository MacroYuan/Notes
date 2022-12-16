# CMake

俗话说得好，一个好的C++程序员一定会写一手好的MakeFile。

make是linux的系统程序，用于管理大型程序的自动编译任务。Makefile是一个描述文件，描述了需要编译的文件之间的依赖（输入输出）关系，并提供更新文件的SHell命令。

对于C/C++程序，可以通过cmake工具生成make程序需要的Makefile配置文件。



CMake的执行步骤一般为以下三步：

```shell
cmake .
make
./Demo
```

第三步执行编译好的可执行文件。



## 目录

一个CMake项目在项目根目录下有一个`CMakeLists.txt`

CMakeLists.txt内容

```cmake
# CMake最低版本要求
cmake_minimum_required(VERSION 3.2)

# 项目名称
project(MyProject)

# 生成可执行文件
add_executable(Test test.cpp)
```

## 添加库

在需要打包库下创建CMakeLists.txt

```cmake
add_library(MyFunction foo.cpp foo.h)
```

在根目录下的`CMakeLists.txt`中添加`add_subdirectory()`添加库

```cmake
include_directories("${PROJECT_SOURCE_DIR}/MyFunction")
add_subdirectory(MyFunction)

# add the executable
add_executable(Demo test.cpp)
target_link_libraries(Demo MyFunction)
```

二进制目标之间的依赖关系使用`target_link_libraries()`表示

## 添加配置文件

```cmake
configure_file ("my_project.gni")
```

## 添加与使用设置

设置

```cmake
option(SUPPORT_MATH "Use provided math implementation" ON)
```

```cmake
if (SUPPORT_MATH)
    includ_directories (...)
    add_subdirectory (...)
    set (...)
    ......
endif (SUPPORT_MATH)
```

## 添加三方依赖

```cmake
install (TARGETS ... DESTINATION bin)
install (FILES ... DESTINATION include)
```



## 定义宏

```cmake
macro (do_test)
    add_test (TutorialComp${arg} Tutorial ${arg})
    set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result}
    )
endmacro (do_test)
```

```cmake
macro(<name> [arg1 [arg2 [arg3 ...]]])
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
endmacro(<name>)
```

## 定义函数

```cmake
function(<name> [arg1 [arg2 [arg3 ...]]])
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
function(<name>)
```
