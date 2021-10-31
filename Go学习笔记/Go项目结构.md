在GOPATH下，包含bin、pkg、src三个目录

-  bin
  存放编译后的可执行文件
- pkg
  存放编译后的文件
- src
  存放项目源文件

一般，bin和pkg可以通过go install命令自动创建，只需创建src文件目录即可。





一个常见的Go项目通常有以下几个项目：

- cmd
  项目主干，构建编译的入口，main文件通常放在此处；
  cmd文件允许挂载多个需要编译的应用，只需要在不同的包下编写main.go文件即可。cmd代码应保持简洁，main函数中可能仅仅是参数初始化、配置加载、服务启动的操作。
- pkg
  存放可供项目内部/外部所使用的公共性代码
- internal
  internal包主要用处在于提供一个项目级别的代码保护方式，存放在其中的代码仅供项目内部使用。
  internal是Go1.4版本引入的特性。
  internal内部也可以继续对目录进行共享范围做区分，例如internal/myapp表示该目录下的代码是供myapp应用使用的；internal/pkg表示该目录下的代码是可以供项目内多个应用使用的。
- go.mod && go.sum
  go.mod与go.sum是采用go modules进行依赖管理所生成的配置文件。go modules是Go1.11版本引入的版本管理功能。
- Makefile
  Makefile文件通常存放项目的编译部署脚本，使用Makefile写编译部署脚本是工程实践中常见的方式。

```text
github.com/ardanlabs/kit
├── CONTRIBUTORS
├── LICENSE
├── README.md
├── cfg/  
├── examples/  
├── log/ 
├── pool/
├── tcp/
├── timezone/
├── udp/
└── web/
```

- 其他
  在Java项目中，常见的src目录在Go项目中不建议使用



## Go Modules

Go Modules是Go语言的依赖解决方案。Go Modules可用于淘汰GOPATH，在GOPATH模式中，我们必须将代码存放子啊固定的$GOPATH/src目录下并且执行`go get`来拉取外部依赖会自动下载并安装到`$GOPATH`目录下。

### Go Modules使用

| 命令            | 作用                                           |
| --------------- | ---------------------------------------------- |
| go mod init     | 生成go.mod文件                                 |
| go mod download | 下载go.mod文件中指明的所有依赖                 |
| go mod tidy     | 整理现有的依赖，拉取现有的模块，移除不用的模块 |
| go mod edit     | 查看现有的依赖结构                             |
| go mod vendor   | 将依赖复制到vendor目录下                       |
| go mod graph    | 打印模块依赖图                                 |
| go mod why      | 解释为什么需要依赖                             |
| go mod verify   | 验证依赖是否正确                               |

#### 在项目中使用

1. 在GOPATH目录之外新建一个目录，并用`go mod init`自动化生成`go.mod`文件
2. 添加依赖
3. 再次运行脚本`go run main.go`发现跳过了检查并安装依赖的步骤
4. 使用命令`go list -m -u all`来检查可升级的package，也可以使用`go get -u need-upgrade-package`，升级后会将新的依赖版本更新到go.mod *，也可以使用`go get -u`升级所有依赖
