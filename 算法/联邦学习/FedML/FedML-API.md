# FedML-API

## data_processing

讲下载的数据集进行处理，包含数据集划分，和数据格式转换，输出为统一的X_train、X_test、y_train、y_test等格式。

如果通过联邦开放服务平台连接的服务器数据集能够处理成该格式，就不需对该内容封装。

## model

模型一般由专业的机器学习人员构建，FedML暂时只支持PyTorch的模型，考虑对一些基础的模型进行封装，模型传输接口。

### cv

darts

resnet56

cnn

efficientnet

mnist_gan

mobilenet

mobilenet_v3

resnet

vgg

### finance

专门用于vfl的lr模型，参考上周笔记

### linear

lr

### nlp

rnn

## 联邦学习接口

主要需要封装的接口

### standalone

单机版联邦学习api，包含不同的联邦学习算法，通过实例化对应API类，输入数据集、学习参数、模型训练器，再调用类中的run()方法

### centralized

中心服务器在多个数据集上训练，更单机版差不多，数据要出狱，没什么价值

### distributed

分布式版联邦学习api，包含不同的联邦学习算法。

与单机版不同的是，分布式api接口需要配置好mpi连接的服务器ip地址，

## 其他接口

自动包含在FedML框架中的，可增加一些其他功能，如日志的本地存储功能。

### utils

**logger.py**

负责记录log日志信息的`logging_config(args, process_id)`函数

**context.py**

记录通信过程中的错误