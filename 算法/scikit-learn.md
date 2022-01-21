# 预处理
1. 预处理的方法
   * get_params([deep]) 返回模型的参数。
      * deep：如果为true，则可以返回模型参数的子对象
   * set_params(*params):设置模型的参数
      * params：待设置的关键字参数。
   * fit(x[,y])：获取预处理需要的参数，不同的预处理方法需要的参数不同
      * x：训练集样本集合，通常是一个numpy array，每行代表一个样本，每列代表一个特征。
      * y：训练样本的标签集合，它与x的每一行对应。
   * transform(X[,copy])：执行预处理，返回处理后的样本集。
      * X：训练集样本集合....
      * copy：一个bool值，指定是否拷贝数据。
   * fit_transform(X[,y])：获取预处理的参数并执行预处理，返回处理后的样本集。
      * X：同上
      * y：同上
## 一、特征处理
***
### 1.1 二元化
1. 二元化Binarizer原型为：
```python
class sklearn.preprocessing.Binarizer(threshold=0.0, copy=True)
```
   * threshold：一个指定转换阈值浮点数：低于此阈值转换为0，高于转换为1
   * copy：指定是否拷贝
2. 方法：
   * fit(X[, y]) ：不作任何事情，主要用于为流水线Pipeline 提供接口。
   * transform(X[, copy]) ：将每个样本的特征二元化。
   * fit_transform(X[, y]) ：将每个样本的特征二元化。
### 1.2 独热码
1. OneHotEncoder原型：
```python
class sklearn.preprocessing.OneHUtEncoder(n_values='auto', categorical_features='all', dtype=<class 'float'>, sparse=True, handle_unknown='error')
```
   * n_values：字符串'auto'，或者一个整数，或者一个整数的数组，它指定了样本每个特征值的上街（特征取值为从0开始的整数）：
      * "auto:自动从训练数据中推断特征值取值的上界
      * 一个整数：指定了所有的特征值的取值上界
      * 一个整数的数组：每个元素依次指定了每个特征取值的上界。
   * categorical_features：字符串'all'，或者下标的数组，或者是一个mask，指定哪些特征需要独热码编码：
      * 'all'：所有的特征都将独热码编码
      * 一个下标的数组：指定下标的特征将独热码编码。
      * 一个mask：对应为true的特征将编码为独热码。
   * dtype：一个类型，指定了独热码编码的数值类型，默认为np.float 。
   * sparse：一个布尔值，指定编码结果是否作为稀疏矩阵。
   * handle_unknown：一个字符串，指定转换过程中遇到了未知的 categorical 特征时的异常处理策略。可以为：
      * 'errer'：抛出异常
      * 'ignore'：忽略
2. 属性
3. 方法：
   * fit(X[, y]：训练编码器。
   * transform(X)：执行独热码编码。
   * fit_transform(X[, y])：训练编码器，然后进行独热码编码。
     
## 1.3 标准化
### 1.3.1 MinMaxScaler
### 1.3.2 MaxAbsScaler
### 1.3.3 StandardScaler
## 1.4 正则化
# 二、特征选择
***
## 2.1 过滤式特征选取
### 2.1.1 VarianceThreshold
### 2.1.2 SelectKBeast
1. SelectKBest 用于保留统计得分最高的k个特征
### 2.1.3 SelectPercentile
## 2.2 包裹式特征选取
### 2.2.1 RFE
### 2.2.2 RFECV
## 2.3 嵌入式特征选择
# 三、字典学习
***
## 3.1 DictionaryLearning
## 3.2 MiniBatchDictionaryLearning
# 四、PipeLine
***
1. scikit-learn中的流水线的流程通常为：
   * 通过一组特征处理estimator来对特征进行处理（如标准化、正则化）
   * 通过一组特征提取estimator来提取特征。
   * 通过一个模型预测estimator来学习模型，并进行预测。
   除了最后一个estimator之外，前面的estimator必须提供transform方法。该方法用于执行数据变换（如归一化、正则化、以及特征提取等）。
2. Pipeline将多个estimator组成流水线，其原型为：
```python
class sklearn.pipeline.Pipeline(steps)
```
   * steps：一个列表，列表的元素为(name, transform)元组，其中：
      * name是estimator的名字，用于输出和日志。
      * transform是estimator。之所以叫transform是因为这个 estimator （除了最后一个）必须提供transform方法。
3. 属性
4. 方法：
   * fit(X[, y])：启动流水线，依次对各个estimator（除了最后一个）执行.fit方法和.transform方法转换数据；对最后一个estimator执行.fit方法训练学习器。
   * transform(X)：启动流水线，依次对各个estimator （包括最后一个）执行.fit方法和.transform方法转换数据。
   * fit_transform(X[, y])：启动流水线，依次对各个estimator（除了最后一个）执行.fit方法和.transform方法转换数据；对最后一个estimator执行.fit_transform方法转换数据。
   * inverse_transform(X)：将转换后的数据逆转换成原始数据。要求每个estimator都实现了.inverse_transform方法。
   * predict(X)/predict_log_proba(X) /predict_proba(X)：将X进行数据转换后，用最后一个学习器来预测。
   * score(X, y) ：将X进行数据转换后，训练最后一个estimator ，并对最后一个estimator 评分。



# 一、PCA
***
1.1 PCA
1. scikit-learn中实现PCA模型，原型为
```python
class sklearn.docomposition.PCA(n.components=None, copy=True, whiten=False
```
    * n_component: 一个整数，指定降维后的维数。
        * 如果为None，则选择它的值为 min(n_samples,n_features) 。
        * 如果为字符串'mle'，则使用Minka's MLE算法来猜测降维后的维数。
        * 如果为大于0，小于1的浮点数，则指定的是降维后的维数占原始维数的百分比。
    * copy: 一个布尔值，指定是否拷贝原始数据。
    * whiten: 一个布尔值，指定是否执行白化操作。
      如果为True，则会将特征向量除以 n_samples倍的特征值，从而保证非相关的输出的方差为1。白化操作可能会丢弃部分信息，但是它有时候在接下来的学习器学习阶段能获得更佳的性能。
3. 方法
    * fit(X[, y]): 训练模型，获取降维需要的参数
    * transform: 执行降维，返回降维后的样本集
    * fit_transform(X[, y]): 训练模型并执行降维，返回降维后的样本集
    * inverse_transform(X): 执行降维的逆运算，返回降维前的样本集
4. 注意：decomposition.PCA基于scipy.linalg来实现SVD分解，因此有两个限制：
    * 不能应用于稀疏矩阵。
    * 无法适用于超大规模数据，因为它要求所有的数据一次加载进内存。
    1.2 IncrementtalPCA
1. 为适应超大规模数据，可以将数据分批加载进内存。
```python
    class sklearn.decomposition.IncrementalPCA(n_components=None, whiten=False, copy=True, batch_size=None)
```
   * batch_size: 一个整数或者None，指定每个批次训练时，使用的样本数量。
      * 只有当调用fit()/partial_fit()方法时，才会用到该参数。
      * 如果为None，则由算法自动推断。
   * 其它参数参考decomposition.PCA 。
1.3 KernelPCA
1. 原型：
```python
class sklearn.decomposition.KernelPCA(n_components=None, kernel='linear', gamma=None, degree=3, coef0=1, kernel_params=None, alpha=1.0, fit_inverse_transform=False, eigen_solver='auto', tol=0, max_iter=None, remove_zero_eig=False)
```
   * n_components：一个整数，指定降维后的维数。
   * kernel：一个字符串或者可调用对象，指定核函数。
      * 'linear'：线性核：
      * 'poly': 多项式核
      * 'rbf':（默认值）：高斯核函数
      * 'sigmoid':sigmoid核函数
      * 'precomputed':
   * degree：一个整数，当核函数是多项式核函数时，指定多项式的系数。对于其他核函数，该参数无效。
   * gamma：一个浮点数，当核函数是'rbf'，'poly'，'sigmoid'时，指定核函数的系数。

如果'auto'，则表示系数为1/n_features
   * ceof0：浮点数，用于指定核函数中的自由项。只有当核函数是'poly'和'sigmoid'是有效。
   * kernel_params：当核函数是个可调用对象时才使用它，用于为该可调用对象传递参数。如果核函数是上述指定的字符串，则该参数不起作用。
   * alpha：一个整数，岭回归的超参数，用于计算逆转换矩阵（当fit_inverse_transform=True时）。
   * fit_inverse_transform: 一个布尔值，指定是否需要计算逆转换矩阵。当为True时，需要计算逆转换矩阵。
   * eigen_solver: 一个字符串，指定求解特征值的算法：
# 二、MDS
# 三、Isomap
# 四、LocallyLinearEmbedding
# 五、FA
# 六、FastICA
# 七、t-SNE