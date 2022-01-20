# FedML分布式模式运行

## 配置

两台服务器：

内存：125G
CPU：Intel(R) Xeon(R) Silver 4210R CPU @ 2.40GHz 
GPU：NVIDIA Corporation Device 2204

## 配置协议通信

在运行分布式训练前需要先配置好机器间的通信协议。

### MPI

使用`mpi_host_file`和`gpu_mapping.yaml`文件定义通信协议

#### mpi_host_file

模板：

```
host1:10
host2:10
```

上面的host1、host2对应于不同机器的通信IP号

#### gpu_mapping.yaml

这个文件用于定义不同机器间的GPU间的连接，如一台服务器上的多个GPU间、多个服务器上的GPU间。如果该文件没定义，则服务器会使用CPU进行训练。

```
mapping_config:
	host1:[1]
	hsot2:[1]
```

上面这段代码定义的是两台机器间的GPU连接方式，有两台机器，每台机器只有一个GPU，每个GPU上只允行一个进程process。当然一个GPU上也可以运行复数的进程，但可能会降低训练速度，但可以更大化利用GPU内存。
Server process——host1：GPU0
client1——host2：GPU0

## 参数定义

PROCESS_NUM 运行进程数，一般等于需要拉起的工作节点数+1



## 运行

**初始化MPI分布式**

**获取参数**

**初始化日志**

**设置随机种子**

**将进程映射到GPU设备上**

**加载数据**

**创建模型**

**开始分布式训练**