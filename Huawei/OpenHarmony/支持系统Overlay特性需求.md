# 支持系统Overlay特性需求

## 什么是Overlay

Overlay是在不改变原hap包的情况下，新增一个覆盖的overlay hap，直接实现应用界面的风格转换，该overlay hap与原hap不耦合，可独立更新。

包含Overlay应用的具体启动流程为，当启动应用程序时，元能力从包管理查询到overlay信息，再更加该信息去资源管理相关接口去加载Overlay资源，最后拉起覆盖的应用。

比如加载应用图标会先从原hap中加载得到应用图标，再加载overlay hap包中也存在同名的图标，就会发生替换为overlay的图标。如果有多个overlay hap的话，则需要通过配置优先级实现覆盖的顺序。

默认情况下，overlay目录的资源文件内容只能覆盖原有hap包中的资源，而不能新增资源。



在Android中Overlay还分为SRO——Static resource overlay静态替换和RRO——Runtime resource overlay运行时替换。

SRO发生在编译时，需要在Android系统源码环境战进行配置。

RRO发生在运行时，能直接替换三方APK的资源，而不需要其源码



## OpenHarmony overlay

### 查询overlay信息

该接口由包管理BMS提供，可查询到OverlayHapModuleInfo

### 资源调度加载overlay资源

