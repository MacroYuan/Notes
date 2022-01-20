# Wandb

> https://blog.csdn.net/qq_40507857/article/details/112791111

wandb是Weights&Biases的缩写，是一款跟踪机器学习项目的工具，它能够自动记录训练过程中的超参数和输出指标，然后可视化和比较结果，并且可以快速共享结果。简言之，就是可视化训练模型过程中的数据。

![](https://img-blog.csdnimg.cn/2021011818331542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTA3ODU3,size_16,color_FFFFFF,t_70)

## 使用

### 1. 安装库

```shell
pip install wandb
```

### 2. 创建账户

```shell
wandb login
```

### 3. 初始化

```python
import wandb
wandb.init(project="my-project")
```

### 4. 声明超参数

```python
wandb.config.dropout = 0.2
wandb.config.hidden_layer_size = 128
```

### 5. 记录日志

```python
def train():
    ...
    train_acc = sum(train_metrics['num_correct']) / sum(train_metrics['num_samples'])
    train_loss = sum(train_metrics['losses']) / sum(train_metrics['num_samples'])
    wandb.log({"Train/Acc": train_acc, "round": round_idx})
    wandb.log({"Train/Loss": train_loss, "round": round_idx})
```

### 6. 保存文件

```python
wandb.save("mymodel.h5")

model.save(os.path.join(wandb.run.dir, "mymodel.h5"))
```

