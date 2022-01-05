## 例子：实现一个trainer

抽象类**`ModelTrainer()`**:

- `train()`
- `test()`
- `test_on_the_server()`

实现类**`MyModelTrainer()`**

- `train(self, train_data, device, args)`实现：

  - `self`：类MyModelTrainer的变量数据
  - `train_data`：训练数据
  - `device`：cpu或gpu训练
  - `args`：一些训练的其他参数，如epochs，lr，wd等

  模型训练过程先加载好模型model，在根据args定义好训练过程中的各种参数，然后开始epoch轮次的训练

- `test(self, test_data, device, args)`实现

  各入参定义与train()中一样。

  