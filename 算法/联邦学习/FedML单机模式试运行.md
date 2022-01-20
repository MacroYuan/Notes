# FedML单机模式

FedML的单机模式跟普通的机器学习一样，单机的情况下不存在联邦的情况，也不需要考虑各机器的通信。

## 如何调起一个训练过程

1. 登录wandb

   ```shell
   wandb login
   ```

   在FedML中是使用[wandb](../wandb.py)来记录训练过程中的模型情况并自动记录日志，所以在使用FedML时需要先登录wandb。

   > 这部分考虑到之后要对接联邦学习平台，相关的模型训练过程数据需要上传到平台服务中。

2. 定义好模型训练参数，通过shell命令调起脚本运行机器学习程序。
   *run_fedavg_standalone_pytorch.sh*

   ```shell
   GPU=$1
   
   CLIENT_NUM=$2
   
   WORKER_NUM=$3
   
   BATCH_SIZE=$4
   
   DATASET=$5
   
   DATA_PATH=$6
   
   MODEL=$7
   
   DISTRIBUTION=$8
   
   ROUND=$9
   
   EPOCH=$10
   
   LR=$11
   
   OPT=$12
   
   CI=$13
   
   python3 ./main_fedavg.py \
   --gpu $GPU \
   --dataset $DATASET \
   --data_dir $DATA_PATH \
   --model $MODEL \
   --partition_method $DISTRIBUTION  \
   --client_num_in_total $CLIENT_NUM \
   --client_num_per_round $WORKER_NUM \
   --comm_round $ROUND \
   --epochs $EPOCH \
   --batch_size $BATCH_SIZE \
   --client_optimizer $OPT \
   --lr $LR \
   --ci $CI
   ```

   eg.

   ```shell
   sh run_fedavg_standalone_pytorch.sh 0 1000 10 10 mnist ./../../../data/mnist lr hetero 200 1 0.03 sgd 0
   ```

   这条命令的意义是用MNIST数据集进行逻辑回归的机器学习模型训练，以及给定一些训练模型的参数。

3. 查看训练过程

   通过在wandb上的日志可以看到当前模型训练的epoch过程，并且通过提前定义的一些ACC等模型评价的参数，查看当前模型的精确度。

   ![](../../image/fedavg_train.png)

   ![](../../image/fedavg_test.png)

可以看到采用逻辑回归的当前机器学习模型ACC为81.9 。

## 尝试服务器运行

![](../../image/ai1_fedavg_standalone_result.png)

## 问题：

1. 模型训练过程中的数据显示问题，服务器运行wandb超时。
2. 代码DEBUG问题：本地电脑运行机器学习只能选择cpu方式，运行CNN的模型需要使用GPU，只能在服务器训练，服务器上代码调试麻烦。

**解决方法：**

1. 寻找另外的数据可视化方式 或者 修改数据显示方法，直接保存在本地。

2. 通过PyCharm的远程调试工具，SSH连接服务器进行断点调试。