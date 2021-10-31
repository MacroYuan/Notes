# 第一章 JVM

## 1.8 垃圾收集器

### 1.8.7 G1垃圾收集器

​	G1(Garbage First)将内存区域划分为大小固定的独立区域，独立使用这些区域的内存资源并跟踪这些区域的垃圾回收进度。

​	同时在后台维护一个优先级队列，在垃圾回收过程中根据系统允许的最长垃圾收集时间，优先回收垃圾最多的区域。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9bbddeeb-e939-41f0-8e8e-2b1a0aa7e0a7.png)

​	G1垃圾收集器通过内存区域独立划分使用，和根据不同优先级回收各区域垃圾的机制，确保了G1垃圾回收器在有限的时间内能够获得最高的垃圾回收效率。

​	相比CMS收集器，G1垃圾回收器改进：

	* 基于标记整理算法，不产生内部碎片。
	* 可以精准地控制STW停顿时间，在不牺牲吞吐量的前提下实现最短停顿垃圾回收。

### 1.8.7 ZG1垃圾回收器

## 1.9 网络编程模型

### 1.9.1 阻塞I/O模型

### 1.9.2 非阻塞I/O模型

### 1.9.3 多路复用I/O模型

### 1.9.4 信号驱动I/O模型

### 1.9.5 异步I/O模型

### 1.9.6 Java I/O

​	java.io包中最重要的5个类：File、OutputStream、InputStream、Writer、Reader，和1个接口Serializable。

### 1.9.7 NIO

(1) Selecter

(2) Channel

(3) Buffer

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/093f9e57-429c-413a-83ee-c689ba596cef.png)

## 1.10 JVM的类加载机制

### 1.10.1 JVM的类加载阶段

5个阶段：加载、验证、准备、解析、初始化。

### 1.10.2 类加载器

启动类加载器(Boosstrap Classloader)、扩展类加载器(Extension Classloader)、应用类加载器(Application Classloader)

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0dd2d40a-5b2b-4d45-b176-e75a4cd4bdbf.png)

### 1.10.3 双亲委派机制

​	一个类在收到类加载请求后，不会尝试自己加载这个类，而是把该类加载请求向上委派给其父类去完成，其父类在接受待该类加载请求后又会将其委派给自己的父类，以此类推，所有的类加载请求都被上派到启动类加载器中。

​	若父类加载器接受到请求后发现自己无法加载该类，则父类会将该信息反馈给子类，直到该类被加载成功。若找不到，则抛出ClassNotFound异常。

### 1.10.4 OSGI



