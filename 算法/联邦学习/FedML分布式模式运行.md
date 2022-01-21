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

上面的host1、host2对应于不同机器的通信IP号，每个机器运行10个进程。

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

```shell
#!/usr/bin/env bash

CLIENT_NUM=$1
WORKER_NUM=$2
MODEL=$3
DISTRIBUTION=$4
ROUND=$5
EPOCH=$6
BATCH_SIZE=$7
LR=$8
DATASET=$9
DATA_DIR=${10}
CLIENT_OPTIMIZER=${11}
CI=${12}

PROCESS_NUM=`expr $WORKER_NUM + 1`
echo $PROCESS_NUM

#hostname > mpi_host_file

mpirun -np $PROCESS_NUM -hostfile ./mpi_host_file python3 ./main_fedavg.py \
  --gpu_mapping_file "gpu_mapping.yaml" \
  --gpu_mapping_key "mapping_default" \
  --model $MODEL \
  --dataset $DATASET \
  --data_dir $DATA_DIR \
  --partition_method $DISTRIBUTION  \
  --client_num_in_total $CLIENT_NUM \
  --client_num_per_round $WORKER_NUM \
  --comm_round $ROUND \
  --epochs $EPOCH \
  --client_optimizer $CLIENT_OPTIMIZER \
  --batch_size $BATCH_SIZE \
  --lr $LR \
  --ci $CI
```



## 运行

**初始化MPI分布式**

通过`mpi_host_file`文件中的定义，将原训练过程分解为多个进程在多个机器上的多个GPU中运行，其中最前面的一台机器将作为Server节点（一台机器上可同时运行Server和Client，作为两个进程），MPI就是作为这些进程间的通信方式。每一个Client都有自己的Client号用于区分。训练过程中的模型参数交互需要使用到该进程号。

**获取参数**

**初始化日志**

**设置随机种子**

**将进程映射到GPU设备上**

该过程会对GPU进行一个map映射，用于多GPU学习活动的管理，每个进程之间是分布式的联邦学习。而进程间是通过global_weight共享梯度权重信息、还是差异隐私方法，则是根据相应API实现的通信模块决定。

**加载数据**

**创建模型**

**开始分布式训练**

分布式训练过程基于Core包的通信接口和训练接口，根据模型和分布式方法、联邦算法做相应实现在API包中，用户所需实习的`main.py`作用是综合数据集加载处理程序、模型创建程序及API包中定义好的接口，传入获取后的参数进行训练。

Client进程负责加载数据集对模型进行训练，以及将训练过程中的数据通过manager类进行接收和上传数据；Server进程则不参加训练，只负责数据的同步调度。manager对数据的处理又分为client_manager和server_manager，由进程功能来定。