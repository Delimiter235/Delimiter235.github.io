# 现象
以前使用 WSL Debian 的时候，习惯使用 Anaconda 对版本进行管理。然而，加上 Debian 自带的 `apt` 用于包管理，`conda` 和 `pip` 同时作为 Python 的包管理器，造成了一定程度上多重嵌套和混乱（具体情况详见下图）。

<div align="center">

![flow chat of package management in debian linux](https://github.com/Delimiter235/Delimiter235.github.io/blob/main/images/wsl_blog/wsl_debian_pack_manage.png?raw=true)

</div>

经过之前一年的使用，我认为如果单独使用 Anaconda 作为环境管理器来说还是不错的，因为其创建环境时包括了设定开发所使用的 Python 版本，创建虚拟环境命令如下：
```
conda create -n envname python=3.4
```
查看环境也非常地方便。
```
conda env list
```
此外，具体的其他指令详见 [*Conda用户指导手册*](https://docs.conda.io/projects/conda/en/latest/user-guide) 。但是说回问题本身，正如流程图中所示，**Anaconda Repository 和 Conda-forge 等 Conda 频道远没有 PyPI 齐全，** 这是使用过程中的一大硬伤，下载不到的库还需要去 PyPI 补齐。也就是说，仅仅将 Anaconda 作为一个包管理器使用的话，就显得有些冗余了。

# 解决方案
为什么要解决这个问题？这个工具好在哪里？

# 具体步骤
核心步骤，代码块，配置文件的修改。
```
code block 1
```
