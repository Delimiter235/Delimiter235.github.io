# 在 WSL（Arch Linux）上配置网络代理


## 现象
前些天为了在 WSL 上科学上网（在未来可以clone项目或爬取网站信息），我决定在 VM 中启用了全局代理，这致使后续几天内使用 `pacman` 和 `pip` 国内源下载包时出现高延迟和丢包的现象。即使换用了国外源，因为地理位置的缘故，我依然需要顶着较高的延迟下载。本着“头痛砍头”的思想，我选择抛弃“clone项目”和“爬取信息”的虚空需求，转向了直接采用流量直连和国内源的方式满足最迫切的需求：包的下载。

<div align="center">

**Linux代理配置中最经典的矛盾“全局代理环境变量”与“国内镜像加速”之间的冲突。**—— Gemini 3.0

</div>

## 解决方案
我决定采用以下方案进行配置：创建 `.proxy` 脚本文件并使用自定义函数 `setproxy` 和 `unsetproxy` 进行控制。如图：

<div align="center">

![flow chat of how to set net work agent in wsl arch linux](https://github.com/Delimiter235/Delimiter235.github.io/blob/main/images/wsl_blog/wsl_agent_set.png?raw=true)

</div>

## 具体方式
### 准备阶段：确认WSL网络连接模式
 VM 的网络模式有三种，即 **NAT模式、桥接模式（Bridge）和仅主机模式（Host-only）** ，这三种模式的区别可以看这篇博客短文了解[*虛擬機的網路模式(Host-only,Bridged,NAT)*](https://hackmd.io/@yzai/H1GmIiqV9)。 **默认情况下，WSL使用基于NAT的体系结构** ，详见[*使用 WSL 访问网络应用程序*](https://learn.microsoft.com/zh-cn/windows/wsl/networking)。

由于并没有修改默认的网络模式，我可以顺理成章的开始正式进行配置工作。如果不放心的话，可以通过在 CMD 和 PowerShell 输入 `ipconfig` 命令检查主系统的 `IPv4` 和 `子网掩码`，同时通过在 WSL 中通过在 Shell 中输入 `ip addr` 来检查 VM 的 `inet` 。

### 第一步
网络代理工具开启“允许局域网代理”（Allow LAN），如果不开启此设置，其将只侦听 `127.0.0.1` 。

<div align="center">

![flow chat of how to set net work agent in wsl arch linux](https://github.com/Delimiter235/Delimiter235.github.io/blob/main/images/wsl_blog/app_shot_edit1.jpg?raw=true)

</div>

### 第二步
允许 `适用于Linux的Windows子系统` 通过 Windows Defender 防火墙进行通讯。

<div align="center">

![flow chat of how to set net work agent in wsl arch linux](https://github.com/Delimiter235/Delimiter235.github.io/blob/main/images/wsl_blog/app_shot_edit2.png?raw=true)

</div>

### 第三步
在 VM 家目录下创建并编辑配置文件 `.proxy`。
```
nano ~/.proxy
```
其中变量 `hostip` 为动态变量，变量 `proxy_url` 中的端口与网络代理工具常规页面显示的端口一致。
```
# 定义变量
hostip=$(grep nameserver /etc/resolv.conf | awk '{print $2}')
proxy_url="http://${hostip}:7890"
```
然后分别定义开启和关闭代理的命令函数。
```
# 定义命令函数
# 代理开启
setproxy() {
    export http_proxy="$proxy_url"
    export https_proxy="$proxy_url"
    export all_proxy="socks5://${hostip}:7890"
    echo "Proxy ON: GitHub Mode"
}

# 代理关闭
unsetproxy() {
    unset http_proxy
    unset https_proxy
    unset all_proxy
    echo "Proxy OFF: Domestic Mode"
}
```
然后根据流程图，添加 `unsetproxy` 至文件末尾以在启动 WSL 时保持代理关闭。最后在文件头部添加 `#!/bin/bash` 以声明脚本解释器并启用语法高亮，编辑完成后在 `.bashrc` 文件中添加 `source ~/.proxy` 即可在进入 WSL 系统后，输入 `setproxy` 就可以开启代理，输入 `unsetproxy` 以关闭代理。

### 第四步
分别配置 Pacman 和 Pip 的镜像源。
首先输入以下命令以编辑 Pacman 镜像源。
```
sudo nano /etc/pacman.d/mirrorlist
```
在注释掉其他国外镜像源后添加物理意义上离我较近的源，如清华源。
```
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```
强制刷新数据库以使 Pacman 在新地址下载。
```
sudo pacman -Syy
```
接下来进入 `pip.conf` 文件。
```
nano  ~/.config/pip/pip.conf
```
添加清华源以作为全局镜像源。
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

### 测试
在输入 `setproxy` 命令后可以尝试克隆一个 GitHub 仓库，在输入 `unsetproxy` 命令后享受国内源带来的高速下载的快乐，你懂的XD。