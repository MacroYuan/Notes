# 第十章 Spring原理及应用

## 1.1 Spring的特性

​	Spring是基于J2EE技术实现的一套轻量级的Java Web Service系统应用框架。Spring的特性包括轻量、控制反转、面向容器、面向切面和框架灵活。

### 1.1.2 控制反转(Inversion of Control, IoC)

​	Spring的控制反转是指一个对象依赖的其他对象将会在容器的初始化完成后将其依赖的对象传递给它，而不需要这个对象自己创建或者查找其他依赖的对象。Spring基于控制反转技术实现系统对象之间依赖的解耦。

### 1.1.3 面向容器

### 1.1.4 面向切面

​	Spring提供了面向切面的编程支持，面向切面技术通过分离系统逻辑和业务逻辑来提高系统的内聚性。在具体的使用过程中，业务层只需要关注并实现和业务相关的代码逻辑，而不需要关注系统功能（例如系统日志、事务支持）和业务功能的复杂关系，Spring通过面向切面技术将系统功能自动织入业务逻辑的关键点。

## 1.2 Spring的模块

核心容器层(Core Container)、数据访问层(Data Access)、Web应用层(Web Access)、AOP、Aspects、Instrumentation、Messaging、Test。

### 1.2.1 核心容器层

- Spring-Beans
- Spring-Core
- Spring-Context
- SpEL(Spring Expression Language)

### 1.2.2 数据访问层

JDBC、ORM、OXM、JMS和事务处理模块。

### 1.2.3 Web应用层

### 1.2.4 其他重要模块

## 1.4 Spring注解

## 1.5 Spring IoC的原理

### 1.5.4 Spring的4种依赖注入

1. **构造器注入**
2. **set方法注入**
3. **静态工厂注入**
4. **实例工厂注入**

## 1.6 Spring AOP的原理

### 1.6.3 AOP的2种代理方式

JDK动态代理技术、CGLib动态代理技术。

- JDK动态代理：
- CGLib动态代理：
- 区别：

## 1.7 SpringMVC原理

## 1.8 事务

### 1.8.1 本地事务

### 1.8.2 分布式事务

### 1.8.3 两阶段提交协议

## 1.9 MyBatis的缓存

一级缓存和二级缓存