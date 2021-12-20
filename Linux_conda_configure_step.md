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

## 安装Miniconda的步骤
首先，我们需要在我们的Linux操作系统上安装Miniconda。步骤如下：

**1.使用下述命令，下载最新版本的Miniconda:**
``` bash
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.10.3-Linux-x86_64.sh
```
注意，这是2021年12月20日看到的。以后最新版本肯定还会更新，只需进入[清华源Miniconda官方镜像站](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)查看最新版本的Miniconda即可（按照日期的降序排列）。

**2.使用如下命令安装Miniconda：**
``` bash
bash Miniconda3-py39_4.10.3-Linux-x86_64.sh
```

**3.设置打开终端后，不激活默认的base虚拟环境：**
``` bash
conda config --set auto_activate_base false
```
之所以要设置成不激活默认的base虚拟环境，是因为一般来说，都不会使用这个base虚拟环境。一般都是使用自己建的特殊的conda虚拟环境。

**4.添加清华源：**
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

**5.修改conda虚拟环境的默认安装路径：**
众所周知，一般来说，home目录无法存放过多的环境，因此有必要换一个空间足够大的地方，来安装以后的众多conda虚拟环境。在home目录下，有一个隐藏的文件`.condarc`。使用`code .condarc`打开`.condarc`文件，在最下面添加如下的代码：
```
envs_dirs:  
  - /data/Jazihars/conda_env
```
表明今后的conda虚拟环境都会被装在`/data/Jazihars/conda_env`目录下。

至此，Miniconda的安装和配置工作就算是完成了。下面，我们来实际安装一个conda虚拟环境。

## 安装conda虚拟环境的步骤
**1.使用下述命令，新建conda虚拟环境：**
``` bash
conda create --name superglue python=3.8
```

**2.激活conda虚拟环境：**
``` bash
conda activate superglue
```

**3.把pip设为清华源：**
以下步骤参考[这里的教程](https://www.runoob.com/w3cnote/pip-cn-mirror.html)。
首先使用`pip --version`命令查看我目前正在使用的`pip`是否为当前虚拟环境下的`pip`。
然后在`~/.pip/pip.conf`文件里（如果没有这个文件，就自己创建），添加如下的代码：
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```
使用命令`pip config list`，测试是否成功修改了`pip`的源为清华源。

至此，conda虚拟环境配置完成了，可以按照项目的需要来安装具体的包了。

## 常用的conda命令整理
以下为一些常用的conda命令

**1.显示目前已有的所有conda虚拟环境**
``` bash
conda env list
conda info --envs
```

**2.删除conda虚拟环境**
``` bash
conda remove --name superglue --all
```