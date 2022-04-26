# Linux系统上配置conda虚拟环境的步骤
这个文档记录了如何从零开始在Linux系统上配置conda虚拟环境

一般来说，我会使用vscode和remote-ssh进行远程开发。在vscode终端里，输入如下的命令：
``` bash
lsb_release -a
```
得到如下的输出：
```
LSB Version:    :core-4.1-amd64:core-4.1-noarch:cxx-4.1-amd64:cxx-4.1-noarch:desktop-4.1-amd64:desktop-4.1-noarch:languages-4.1-amd64:languages-4.1-noarch:printing-4.1-amd64:printing-4.1-noarch
Distributor ID: CentOS
Description:    CentOS Linux release 7.5.1804 (Core) 
Release:        7.5.1804
Codename:       Core
```
由此可知，我是在CentOS这个Linux发行版上进行远程开发的。
或者，如果上述命令无法使用，可以使用下述命令：
``` bash
cat /etc/redhat-release
```
在我的测试中，这个命令输出了如下的内容：
```
CentOS Linux release 7.6.1810 (Core)
```
由此可知，我使用的开发机安装的是`CentOS Linux release 7.6.1810`版本的Linux发行版。

## 安装Miniconda的步骤
首先，我们需要在我们的Linux操作系统上安装Miniconda。步骤如下：

### 1.使用下述命令，下载最新版本的Miniconda:
``` bash
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.11.0-Linux-x86_64.sh
```
注意，这是2022年4月26日看到的。以后最新版本肯定还会更新，只需进入[清华源Miniconda官方镜像站](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)查看最新版本的Miniconda即可（按照日期的降序排列）。

如果开发机网络无法访问，可以先在本地下载，再使用`MobaXterm`上传开发机。


### 2.使用如下命令安装Miniconda：
``` bash
bash Miniconda3-py39_4.11.0-Linux-x86_64.sh
```

### 3.设置打开终端后，不激活默认的base虚拟环境：
``` bash
conda config --set auto_activate_base false
```
之所以要设置成不激活默认的base虚拟环境，是因为一般来说，都不会使用这个base虚拟环境。一般都是使用自己建的特殊的conda虚拟环境。

### 4.添加清华源：
``` bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/menpo/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
```
添加清华源的目的是：方便之后下载conda虚拟环境中的各种包。如果直接从位于国外的服务器下载，会非常慢。使用清华源，速度会很快（至于原因嘛，是众所周知的）。

### 5.修改conda虚拟环境的默认安装路径：
**（注意：这一步是可选的。如果`/home`目录空间足够大，这一步是不需要的。）**
众所周知，一般来说，home目录无法存放过多的环境，因此有必要换一个空间足够大的地方，来安装以后的众多conda虚拟环境。在home目录下，有一个隐藏的文件`.condarc`。使用`code .condarc`打开`.condarc`文件，在最下面添加如下的代码：
```
envs_dirs:  
  - /data/Jazihars/conda_env
```
表明今后的conda虚拟环境都会被装在`/data/Jazihars/conda_env`目录下。

至此，Miniconda的安装和配置工作就算是完成了。下面，我们来实际安装一个conda虚拟环境。

## 安装conda虚拟环境的步骤
### 1.使用下述命令，新建conda虚拟环境：
``` bash
conda create --name leaves python=3.9
```
本次新建一个名为`leaves`的conda虚拟环境，安装最新的python3.9版本（本次安装的是python3.9.12）

### 2.激活conda虚拟环境：
``` bash
conda activate leaves
```

### 3.把pip设为清华源：
以下步骤参考[这里的教程](https://www.runoob.com/w3cnote/pip-cn-mirror.html)。
首先使用`pip --version`命令查看我目前正在使用的`pip`是否为当前虚拟环境下的`pip`。
然后在`~/.pip/pip.conf`文件里（如果没有这个文件，就自己创建），添加如下的代码：
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```
使用命令
``` bash
pip config list
```
测试是否成功修改了`pip`的源为清华源。

至此，conda虚拟环境配置完成了，可以按照项目的需要来安装具体的包了。

## 在conda虚拟环境中安装PyTorch的步骤
接下来我们进入我们刚刚安装好的conda虚拟环境`leaves`。运行下述命令：
``` bash
conda activate leaves
```
然后在这个conda虚拟环境中安装PyTorch。
我们在[清华源PyTorch安装包列表页面](https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/linux-64/)中找到下述的两个包：
```
pytorch-1.10.1-py3.9_cuda11.1_cudnn8.0.5_0.tar.bz2
torchvision-0.11.1-py39_cu111.tar.bz2
```
为什么安装这两个版本的包呢？我们在运行一下nvcc命令。运行下述命令：
``` bash
nvcc -V
```
产生了如下的输出：
```
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2020 NVIDIA Corporation
Built on Tue_Sep_15_19:10:02_PDT_2020
Cuda compilation tools, release 11.1, V11.1.74
Build cuda_11.1.TC455_06.29069683_0
```
这说明，我的开发机上安装的`cuda`版本是`11.1`。因此，我们选择cuda11.1版本的PyTorch和torchvision包，我们选择两个相对较新的，因此选择了`pytorch-1.10.1-py3.9_cuda11.1_cudnn8.0.5_0.tar.bz2`和`torchvision-0.11.1-py39_cu111.tar.bz2`这两个包。

我们现在自己的本地下载这两个包，再用`MobaXterm`把这两个安装包上传到开发机上的`/home/users/XXX/miniconda3/pkgs`目录下。然后进入`/home/users/XXX/miniconda3/pkgs`目录，在这个目录为当前目录的前提下，运行下述两个命令：
``` bash
conda install --use-local pytorch-1.10.1-py3.9_cuda11.1_cudnn8.0.5_0.tar.bz2
conda install --use-local torchvision-0.11.1-py39_cu111.tar.bz2
```
运行`pip list`命令，可以看到如下的内容：
```
Package     Version
----------- -------
pip         22.0.4
setuptools  62.1.0
torch       1.10.1
torchvision 0.11.1
wheel       0.37.1
```
表明我们已经在当前的`leaves`虚拟环境下成功安装了`torch`和`torchvision`这两个库。
然后我们运行下述命令：
``` bash
conda install pytorch==1.10.1 torchvision==0.11.1 cudatoolkit=11.1
```
这个命令是为了安装PyTorch所需的其他依赖项。在我的安装过程中，这个命令还自动修复了我的torch和torchvision版本不匹配的问题。这条命令执行完毕之后，我们重新运行`pip list`命令，看到了如下输出：
```
Package           Version
----------------- -------
black             22.3.0
click             8.1.2
mkl-fft           1.3.1
mkl-random        1.2.2
mkl-service       2.4.0
mypy-extensions   0.4.3
numpy             1.21.5
pathspec          0.9.0
Pillow            9.1.0
pip               22.0.4
platformdirs      2.5.2
setuptools        62.1.0
six               1.16.0
tomli             2.0.1
torch             1.10.1
torchvision       0.11.2
typing_extensions 4.2.0
wheel             0.37.1
```
可以看到，`torchvision`包的版本已经由`0.11.1`修正为`0.11.2`了。因此，`conda install`命令自动帮我们修正了包的版本不一致的问题。

最后我们在一个空白脚本中运行下述命令：
``` python
import torch

print("torch版本：", torch.__version__)
print("torch对应的cuda版本：", torch.version.cuda)
```
结果为：
```
torch版本： 1.10.1
torch对应的cuda版本： 11.1
```
这就是我使用的PyTorch的版本和它对应的cuda版本。

至此，我们的conda虚拟环境`leaves`就算是初步构建好了。剩下的工作可以安装一些常用的包。比如，我一般会用：
``` bash
pip install black
```
命令安装一个强制格式化代码的工具`black`，使得自己写的Python代码可以被比较规范地格式化。

## 常用的conda命令整理
以下为一些常用的conda命令

### 1.显示目前已有的所有conda虚拟环境
``` bash
conda env list
conda info --envs
```
这两个命令显示目前已经安装的所有conda虚拟环境。

### 2.删除conda虚拟环境
``` bash
conda remove --name superglue --all
```
这个命令删除了一个名为`superglue`的conda虚拟环境以及里面的所有包。