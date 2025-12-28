# 现象
以前使用 WSL Debian 的时候，习惯使用 Anaconda 对版本进行管理。然而，加上 Debian 自带的 `apt` 用于包管理，`conda` 和 `pip` 同时作为 Python 的包管理器，造成了一定程度上多重嵌套和混乱（具体情况详见下图）。

<div align="center">

![flow chat of package management in debian linux](https://github.com/Delimiter235/Delimiter235.github.io/blob/main/images/wsl_blog/wsl_debian_pack_manage.png?raw=true)

</div>

<br> 经过之前一年的使用，我认为如果单独使用 Anaconda 作为环境管理器来说还是不错的，因为其创建环境时包括了设定开发所使用的 Python 版本，创建虚拟环境命令如下：
```
conda create -n envname python=3.9
```
查看环境也非常地方便：
```
conda env list
```
此外，具体的其他指令详见 [*Conda用户指导手册*](https://docs.conda.io/projects/conda/en/latest/user-guide/) 。但是说回问题本身，正如流程图中所示，**Anaconda Repository 和 Conda-forge 等 Conda 频道远没有 PyPI 齐全，** 下载不到的库还需要去 PyPI 补齐，这是使用过程中的一大硬伤。仅仅将 Anaconda 作为一个包管理器使用，Conda 频道就显得有些冗余了。

# 解决方案
由此，我们吸取了在 WSL Debian 中环境管理混乱的经验，并采用了下图流程管理 Python 的虚拟环境和包。

<div align="center">

![flow chat of package management in arch linux](https://github.com/Delimiter235/Delimiter235.github.io/blob/main/images/wsl_blog/wsl_arch_pack_manage.png?raw=true)

</div>

<br> 采用 `pyenv` 库可以有效隔离系统环境与项目环境的 Python 版本，在使用命令 `pyenv versions` 后可以清晰地看到系统与其他安装的 Python 版本，其中 `*` 所在行代表了当前是使用系统版本，还是其他 Python 版本（以下输出显示当前属于系统版本）。
```
* system (set by PYENV_VERSION environment variable)
  3.13.7
  3.13.7/envs/myproject
  myproject --> /home/username/.pyenv/versions/3.13.7/envs/myproject
```
至于具体的项目环境的切换和查看，则参考  [*pyenv virtualenv插件文档*](https://github.com/pyenv/pyenv-virtualenv)。
# 具体步骤
核心步骤，代码块，配置文件的修改。
```
code block 1
```
