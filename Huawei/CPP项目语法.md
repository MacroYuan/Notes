- HILOG_INFO(), HILOG_WARN(), HILOG_ERROR(), HILOG_DEBUG()的日志打印相当于printf标准化格式输出。没有string语法糖，需传入如`"%{public}s", string`，`"%{privite}d", Integer`的形式

- 头文件名需要按照字典序进行排序

- 在使用AbilityManagerService的方法时，AMS是单例对象，需要GetInstance()获取对象实例，再使用其中的方法，不能在静态函数中使用。

- 如果不是回调参数且参数不会发生修改的情况，传入参数时使用引用传递加上const，参数会发生修改时使用引用传递

- 回调是将原本函数结束输出的返回值放到入参中，通过在函数体中的变化，再次拿到入参为想要的结果

- 


