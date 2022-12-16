# NAPI到AMS的过程

- js层怎样到native层

- js_ability_context.cpp将js层与native层的对应代码绑定

- native层的AbilityContext调用AbilityMangerClient的接口，通过IPC调用AMS





开发NAPI接口

1. js_\*.cpp文件添加接口函数NativeValue* Js\*(){}，调用On\*()

2. 实现On*()功能

3. BindNativeFuntion(engine, *object, "", Js\*::\*); 绑定Js函数到引擎

4. 在\*.cpp、\*.h文件里实现ErrCode \*** const函数



接口关联步骤

1. 实现napi_common_\*.cpp中的功能函数

2. 在具体对外的cpp文件中添加引用1中功能函数的函数

3. 在初始化时通过DECLARE_NAPI_FUNCTION将接口暴露出去



测试

1. 编写几个FA、PA，应用A、B通过connect连接C，当A disconnect C时，C在disconnect时调用TerminateSelf()，尽管在B connect条件下C仍能关闭

2. 学习API8代码FA的编写

3. 
