# 

## Activity启动方式

### 1. Standard（标准模式）

每次启动Activity时都会创建一个新的实例，不管这个Activity已经存在。

### 2.SingleTop（栈顶复用模式）

如果当前Activity在栈顶，则直接复用该Activity，不会创建新实例；如果不在栈顶，则创建新实例，并加入栈顶。适用需要及时更新数据的情况，如聊天界面、通知界面等。

### 3.SingleTask（栈内复用模式）

如果当前任务栈不存在该Activity，则创建新实例，并放在栈底；如果存在，则将Activity上面的所有Activity弹出栈，使其处于栈顶。可用于需要保持唯一性的Activity，如主界面、登录界面等

### 4.SingleInstance（单实例模式）

该Activity是全局的，独立于其他任务栈，该模式启动时，会创建一个新的任务栈存在该Activity实例。适用于需要和其他应用程序隔离的Activity。



## 冷启动和热启动

### 冷启动

是指在应用没有在后台运行的情况下，点击应用图标启动应用。

这种方式需要进行完整的应用启动过程，包括创建新的进程、创建应用对象、创建应用界面和执行初始化等操作。需要执行完整的启动流程，耗时较长。

### 热启动

是指应用已经在后台运行的情况下，再次点击应用图标启动应用。

这种方式是直接将应用从后台切换到前台并恢复应用的状态



## Android模拟器

Android模拟器是基于QEMU实现的，Qemu是纯



































```cpp
auto task = [callback]() {
    callback->DoPrepareTermiante();
}
```

