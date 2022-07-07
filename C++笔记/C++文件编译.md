## Make

make是Linux系统的程序，用于对大型程序的自动编译 认为，自动决定程序某一部分需要重新编译，并发出编译指令。

Makefile

在使用make之前，需要在当前目录下添加一个**Makefile**文件，用于描述文件之间的依赖（输入输出）关系，并提供更新文件的Shell命令。`Makefile`文件名可以是`Makefile`也可以是`makefile`。

Makefile文件规则如下：

```makefile
target ... : prerequisite ...
	recipe    # 注意必须以Tab开头
	...
```

- target:通常是程序生成的一个或多个文件名，例如可执行文件或目标文件；也可以是要执行任务的名称
- prerequisite：先决条件，用于生成target文件的输入文件或是完成target任务前需要执行的任务。
- recipe：步骤，用于生成target文件或完成target任务而执行一系列shell命令。

```makefile
hello.out : hello.c util_1.o util_2.o
	cc -o hello.out hello.c util_1.o util_2.o

util_1.o : util_1.c helper.h
	cc -c util_1.c
	
util_2.o : util_2.c helper.h
	cc -c util_2.c
```

运行`make`会执行：

```shell
$ make
# shell 就会执行
$ cc -c util_1.c helper.h
$ cc -c util_2.c helper.h
$ cc -o hello.out hello.c util_1.o util_2.o
```



## Ninja

Ninja可以看作是一个更好的make，最早用于chrome的构建

## GN

gn是生成ninja的工具

### 语法

#### 字符串

```gn
a = ""
```

### 配置

#### 模板

```gn
# 使用模板类似Java的导入类
import("//tools/idl_compiler.gni")
idl("my_interfaces") {  # 等同于调用idl
	sources = ["a", "b"]  # 给idl传参，参数接受方式是invoker。sources
}
```

#### 配置项

```gn
config("myconfig") { # 创建一个标签为"myconfig"的配置项
	include_dirs = ["include/common"]
	defines = ["ENABLE_DOOM_MELON"]
}

executable("mything") { # 生成可执行文件
	configs = [":myconfig"] # 使用标签为"myconfig"的配置项来生成目标文件
}
```

#### 目标项

目标项是生成的某种可执行文件或库文件

```gn
action: 运行脚本以生成文件
executable: 生成可执行文件
group: 生成依赖关系组
shared_library: 生成.dll或.so动态链接库
static_library: 生成.lib或.a静态链接库
```



