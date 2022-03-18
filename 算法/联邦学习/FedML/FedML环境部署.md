## 下载源文件

**方式一：**

直接git下载

```shell
git clone git@github.com:FedML-AI/FedML.git
```

由于学校内部网关访问github速度很慢，建议自行下载后用ftp上传

**方式二：**

先将源文件下载到本地，再通过ssh工具的ftp文件传送到服务器。

![](../../image/ftp.png)

在服务器对压缩文件进行解压得到目标文件夹

```shell
unzip FedML.zip
cp FedML-master FedML
```

![](../../image/unzip.png)



## 安装运行环境

FedML中提供了CI-install.sh脚本来安装运行环境

**问题：**

1. pip3服务器未安装
2. 安装conda时出现source:not found的错误
3. conda安装pytorch和torchvision的cuda版本时因为网络问题无法安装

**解决方案：**

1. 需要管理员权限安装好pip3，环境中只是一个python语法检查库pyflask需要用到pip3进行安装，可忽略

2. 原因是source在bash和dash运行中的区别，Ubuntu中的sh默认是Dash，将执行脚本的命令`sh CI-install.sh`改为`bash CI-install.sh`

3. 添加镜像源，手动再多次尝试安装
   - [清华源](https://blog.csdn.net/weixin_39902085/article/details/112407606)
   
   - 通过pytorch.org网页查看安装命令
   
     ![](../../image/conda_install_cuda.png)
   
     `conda install pytorch torchvision torchaudio cudatoolkit=11.3`





## 安装数据集

下载数据集时从谷歌网上来的，校园网不好访问谷歌，这部分不好直接在服务器下载，需要自己下载好上传到服务器。