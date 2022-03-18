单机版：

| 联邦学习算法 | 数据集                                  | 模型        | 训练效果                                         |
| ------------ | --------------------------------------- | ----------- | ------------------------------------------------ |
| fedavg       | MNIST                                   | lr          | https://app.wandb.ai/automl/fedml/runs/ybv29kak  |
|              | shakespeare                             | rnn         | https://app.wandb.ai/automl/fedml/runs/2al5q5mi  |
|              | fed_shakespeare                         | rnn         | https://wandb.ai/automl/fedml/runs/4btyrt0u      |
|              | fed_CIFAR100                            | resnet18_gn | https://wandb.ai/automl/fedml/runs/1canbwed      |
|              | stackoverflow_LR<br />stackoverflow_NWP | lr<br />rnn | https://wandb.ai/automl/fedml/runs/7pf2c9r2      |
|              | CIFAR10                                 | resnet56    |                                                  |
| vfl          | lending_club_loan                       | lr          |                                                  |
|              | nus_wide                                | lr          |                                                  |
| fednova      | CIFAR10                                 | vgg         | https://wandb.ai/elliebababa/fedml/runs/51thqi89 |
|              | Sythetic                                | lr          |                                                  |
| fedopt       | MNIST                                   | lr          |                                                  |
|              | shakespeare                             | rnn         |                                                  |
|              | fed_shakespeare                         | rnn         |                                                  |
|              | fedrated EMNIST                         | cnn         |                                                  |
|              | Fed_CIFAR100                            | resnet18_gn |                                                  |
|              | Stackiverflow_LR                        | lr          |                                                  |
|              | stackoverflow_NWP                       | rnn         |                                                  |

中心化：

| 联邦算法 | 数据集   | 模型         |
| -------- | -------- | ------------ |
|          | ImageNet | mobilenet_v3 |
|          | gld23k   | efficientnet |
|          | gld160k  | mobilenet_v3 |

分布式：

| 联邦算法 | 数据集                             | 模型        |
| -------- | ---------------------------------- | ----------- |
| vfl      | lending_club_loan                  | lr          |
| fedavg   | MNIST                              | lr          |
|          | shakespeare                        | rnn         |
|          | Fed_MNIST                          | cnn         |
|          | fed_cifar100                       | resnet18_gn |
|          | fed_shakespeare                    | rnn         |
|          | stackoverflow_LR                   | lr          |
|          | stackoverflow_NWP                  | rnn         |
| fedgan   | MNIST_gan                          | lr          |
| fedopt   | federated EMNIST                   | cnn         |
| fednas   | cifar10<br />cifar100<br />cinic10 | darts       |
| fedgkt   | cifar10<br />cifar100<br />cinic10 | resnet56    |

算法简介：

fedgkt：通过像fedavg一样使用本地SGD进行训练来整合fedavg和split-learning方法，讲地计算需求放在边缘，从而提高内存利用和计算效率。

![img](https://pic2.zhimg.com/80/v2-8bd2c3e5c40973eae95255f90aba0cd9_720w.jpg)

具体查看论文: 《Group Knowledge Transfer: Federated Learning of Large CNNs at the Edge》

fednova：优化fedavg算法，一种消除目标不一致性同时保持快速误差收敛的归一化平均算法。
具体论文：《**Tackling the Objective Inconsistency Problem in Heterogeneous Federated Optimization**》
