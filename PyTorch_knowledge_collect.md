# PyTorch用法收集整理
在这个文档中，我将收集和整理PyTorch的相关用法

## 查看PyTorch版本和PyTorch对应的cuda版本
在交互式命令行中，运行如下的代码：
``` bash
[Linux shell]$ python
Python 3.8.12 | packaged by conda-forge | (default, Jan 30 2022, 23:53:36) 
[GCC 9.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.__version__
'1.8.0'
>>> torch.version.cuda
'11.1'
>>> exit()
[Linux shell]$
```
注意，由于我激活了conda虚拟环境，因此，使用`python`命令自动打开的就是这个conda虚拟环境中安装的Python3.8。上述用法参考[How do I know the current version of pytorch?](https://discuss.pytorch.org/t/how-do-i-know-the-current-version-of-pytorch/6754)和[How to check which cuda version my pytorch is using](https://discuss.pytorch.org/t/how-to-check-which-cuda-version-my-pytorch-is-using/116622)