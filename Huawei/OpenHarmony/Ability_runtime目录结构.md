## 项目目录介绍

### Services

> 独立运行的进程实现

#### common

公有部件的实现，包含事件报告、权限验证、权限内容、日志打印 、进程回调处理

#### abilitymgr

AMS服务实现，实现运行设备上的AMS进程，实现AbilityManagerService服务类及其配套服务。负责ability的管理（如ability启动、生命周期变换）

#### appmgr

AppMS服务实现，实现运行设备上的AppMS进程，实现ApplicationMgrService服务类及其配套服务。复制application的管理（如application生命周期转换）

#### dataobsmgr

DataObsManagerService服务实现，实现运行设备上的数据监视器进程，实现DataObserverService服务类。负责DataAbility的监视器注册、注销等

#### dialog_ui

"未响应"、"使用以下方式打开"和"无法打开文件"弹窗的js实现

#### sa_profile

ability_runtime的sa配置

#### uripermmgr

UriPerimissionManagerService服务实现，实现运行设备上的uri资源权限管理进行，实现UriPermissionManagerService服务类。负责Uri授权、鉴权和移除权限。



### interface

> 接口，包含JS接口和C++接口

#### inner_api

系统内部件间的接口

#### kit/js

JS接口

#### kit/native

C/C++接口

### frameworks

> 部件无独立进行的实现

#### native

C/C++实现

#### js/napi

NAPI的代码实现

#### js/builtin

用于LiteOS-M的js-api实现

#### js/plugin

ArkUI特有的实现

### test

测试代码

#### BUILD.gn

编译入口，基于gn实现

### bundle.json

部件描述文件，描述了项目的依赖件及构建
