# OpenHarmony进程间通信

进程间通信

进程间的通信方式有

管道：

- 匿名管道
- 命名管道

操作系统：

- 共享内存
- 消息队列
- 信号量



类似于安卓的binder机制，OH中也采用Stub-Proxy的形式进行进程间通信。IPC因为扮演角色的不同可分为发送端和接收端。

通过IRemoteBroker、IRometeStub、IRemoteProxy几个对象构成进程间传输数据的接口，

IRemoteProxy为运行在发送端的模拟接口，通过将参数以序列化的方式写入data，再通过SendRequest发送到接受端进行处理，然后阻塞等待IPC返回结果。

IRemoteStub为运行在接收端的桩，接收端通过Stub桩OnRemoteRequest接受请求后，先将序列化数据转化为对应的参数或对象，再调用服务层的具体方法进行处理，将结果写入reply返回给接收端。

在Proxy-Stub直接有预先云顶好的一套逻辑，哪一个IPC-code调用哪一个接口