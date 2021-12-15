# FedML

## FedML介绍

FedML是一个联邦学习的开源库。

FedML支持三种计算场景：**移动设备训练、分布式计算、单机模拟仿真**。



在论文中，相比于FATE的联邦学习，FedML的优势有：

- 灵活通用的API设计
- 支持更多标准化的算法实现（**FedAvg, decentralized FL, FedNAS, VFL, SplitNN**）



常见的联邦学习有：TFF(TensorFlow-Fedrated)、PySyft、LEAF，都只支持单机上执行FedAvg、FedProx的中心模式。面向产品的FATE、PaddleFL太过工业化，不利于研究。

论文中指出现存联邦学习框架缺点有:

- 缺少对不同FL配置的支持
- 缺少对更多不同FL算法的实现



![](http://doc.fedml.ai/image/architecture_for_website.png)



## 源码分析

FedML联邦学习库主要的两个组件是fedml_api和fedml_core

| 包名              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| fedml_api         | 更高级的api，基于fedml_core构建<br />> 后期开发主要基于此包代码进行适配更改 |
| fedml_core        | 更偏向底层的api，通过MPI等通信后端实现分布式计算，并支持拓扑管理 |
| fedml_experiments | 通过调用fedml_api进行测试                                    |
| fedml_iot         | 适配物联网设备的联邦学习                                     |
| fedml_mobile      | 适配移动设备的联邦学习                                       |
| fedml_server      | 构建移动端的服务中心                                         |
| benchmark         | 用于运行基准实验                                             |
| application       | 基于FedML开发的应用程序，暂时没东西                          |

fedml_api的使用参照pytorch框架

## fedml_core

> TODO 理解框架



## 库使用

### 分布式计算

![](http://doc.fedml.ai/image/multi-gpu-topo.png)

配置：

1. N个计算节点
2. 一个头节点（登录、测试）
3. 集中式容错文件服务器（eg. NFS）

软件：

​	版本要求：PyTorch>=1.4.0、MPI4Py>=3.0.3、Python>=3.7.4

> 作者说将来会支持rpc，rpc我更熟悉点

数据集：

​	自行下载

运行检查：

​	检查实验配置是否正确

运行：

​	通过运行如`run_fedavg_distributed_pytorch.sh`等不同文件+参数实现模型训练。

### 单机模拟

### 移动端和物联网