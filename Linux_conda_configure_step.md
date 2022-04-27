# Linux系统上配置conda虚拟环境的步骤
这个文档记录了如何从零开始在Linux系统上配置conda虚拟环境。

最初的问题：**在使用Python的开发中，为什么要使用conda虚拟环境？**

这是因为，不同的代码会运行在不同版本的依赖环境中。这些依赖环境通常是指不同版本的软件包。比如，[Swin Transformer](https://github.com/microsoft/Swin-Transformer/blob/main/get_started.md#install)需要`python=3.7`、`PyTorch==1.7.1`、`torchvision==0.8.2`、`timm==0.3.2`的安装环境，而其他一些开源代码或许需要其他版本的一些依赖环境。使用`conda`来创建不同版本的依赖环境，做到不同版本的环境彼此隔离，是一个非常有效的办法。

创建相互隔离的依赖环境的另一个办法是使用`docker`，这个办法比较复杂，我暂时还没有打通相关流程，或许以后再更新吧。

一般来说，我会使用Visual Studio Code（VS Code）和Remote-SSH进行远程开发。在使用ssh远程连接Linux开发机后，使用`` Ctrl + ` ``快捷键打开vscode的Linux终端，输入如下的命令：
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
由此可知，我是在安装了CentOS这个Linux发行版的开发机上进行远程开发的。
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

众所周知，一般来说，home目录无法存放过多的环境，因此有必要换一个空间足够大的地方，来安装以后的众多conda虚拟环境。在home目录下，有一个隐藏的文件`.condarc`。使用`code .condarc`命令打开`.condarc`文件，在最下面添加如下的代码：
```
envs_dirs:  
  - /data/XXX/conda_env
```
表明今后的conda虚拟环境都会被装在`/data/XXX/conda_env`目录下。

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
这说明，我的开发机上安装的`cuda`版本是`11.1`。因此，我们选择`cuda-11.1`版本的`PyTorch`和`torchvision`包，我们选择两个相对较新的，因此选择了`pytorch-1.10.1-py3.9_cuda11.1_cudnn8.0.5_0.tar.bz2`和`torchvision-0.11.1-py39_cu111.tar.bz2`这两个包。

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

### 3.查看当前虚拟环境中的包
在激活当前的名为`leaves`的conda虚拟环境的条件下，运行下述命令：
``` bash
conda list
```
看到了如下输出：
```
# packages in environment at /home/users/XXX/miniconda3/envs/leaves:
#
# Name                    Version                   Build  Channel
_libgcc_mutex             0.1                 conda_forge    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
_openmp_mutex             4.5                       2_gnu    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
black                     22.3.0                   pypi_0    pypi
blas                      1.0                         mkl    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
bzip2                     1.0.8                h7f98852_4    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
ca-certificates           2021.10.8            ha878542_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
click                     8.1.2                    pypi_0    pypi
cudatoolkit               11.1.1              h6406543_10    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
ffmpeg                    4.3                  hf484d3e_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch
freetype                  2.10.4               h0708190_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
giflib                    5.2.1                h36c2ea0_2    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
gmp                       6.2.1                h58526e2_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
gnutls                    3.6.13               h85f3911_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
intel-openmp              2021.4.0          h06a4308_3561    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
jbig                      2.1               h7f98852_2003    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
jpeg                      9e                   h166bdaf_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
lame                      3.100             h7f98852_1001    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
lcms2                     2.12                 hddcbb42_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
ld_impl_linux-64          2.36.1               hea4e1c9_2    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
lerc                      3.0                  h9c3ff4c_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libdeflate                1.10                 h7f98852_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libffi                    3.4.2                h7f98852_5    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libgcc-ng                 11.2.0              h1d223b6_16    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libgomp                   11.2.0              h1d223b6_16    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libiconv                  1.16                 h516909a_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libnsl                    2.0.0                h7f98852_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libpng                    1.6.37               h21135ba_2    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libstdcxx-ng              11.2.0              he4da1e4_16    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libtiff                   4.3.0                h542a066_3    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libuuid                   2.32.1            h7f98852_1000    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libuv                     1.43.0               h7f98852_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libwebp                   1.2.2                h3452ae3_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libwebp-base              1.2.2                h7f98852_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libxcb                    1.13              h7f98852_1004    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
libzlib                   1.2.11            h166bdaf_1014    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
lz4-c                     1.9.3                h9c3ff4c_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
mkl                       2021.4.0           h06a4308_640    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
mkl-service               2.4.0            py39h7e14d7c_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
mkl_fft                   1.3.1            py39h0c7bc48_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
mkl_random                1.2.2            py39hde0f152_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
mypy-extensions           0.4.3                    pypi_0    pypi
ncurses                   6.3                  h27087fc_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
nettle                    3.6                  he412f7d_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
numpy                     1.21.5           py39he7a7128_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
numpy-base                1.21.5           py39hf524024_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
openh264                  2.1.1                h780b84a_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
openjpeg                  2.4.0                hb52868f_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
openssl                   3.0.2                h166bdaf_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
pathspec                  0.9.0                    pypi_0    pypi
pillow                    9.1.0            py39hae2aec6_2    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
pip                       22.0.4             pyhd8ed1ab_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
platformdirs              2.5.2                    pypi_0    pypi
pthread-stubs             0.4               h36c2ea0_1001    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
python                    3.9.12          h2660328_1_cpython    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
python_abi                3.9                      2_cp39    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
pytorch                   1.10.1          py3.9_cuda11.1_cudnn8.0.5_0    <unknown>
pytorch-mutex             1.0                        cuda    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch
readline                  8.1                  h46c0cb4_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
setuptools                62.1.0           py39hf3d152e_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
six                       1.16.0             pyh6c4a22f_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
sqlite                    3.38.2               h4ff8645_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
tk                        8.6.12               h27826a3_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
tomli                     2.0.1                    pypi_0    pypi
torchvision               0.11.2               py39_cu111    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch
typing_extensions         4.2.0              pyha770c72_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
tzdata                    2022a                h191b570_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
wheel                     0.37.1             pyhd8ed1ab_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
xorg-libxau               1.0.9                h7f98852_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
xorg-libxdmcp             1.1.3                h7f98852_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
xz                        5.2.5                h516909a_1    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
zlib                      1.2.11            h166bdaf_1014    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
zstd                      1.5.2                ha95c52a_0    https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
```
这就是leaves环境下安装的软件包。或者，在当前虚拟环境激活的条件下，运行下述命令：
``` bash
pip list
```
也可以查看当前虚拟环境安装的软件包。

至此，环境就算是配置好了，更多的问题可以搜索网上的资料自行解决。可以开始进行正式的开发了。