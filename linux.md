# Linux系统使用中遇到的问题

## WSL

### 安装

去微软商店安装即可

### 配置

有时候登录账户变了，默认wsl系统变了可以使用wslconfig命令配置

### 读取windows上文件很慢

可能因为linux文件系统是ext4，windows是NTFS，需要转化所以慢
解决方法是创建一个ext4的虚拟盘，挂载到linux上面使用；然鹅这样windows就不能读取了，没什么用，也许是wsl内存不够时挂载外部存储器使用；如果空间不够，可以把wsl移动到空间大的磁盘

```ps
wsl --mount --vhd "F:\wslF.vhdx" --name wslF -p1
```

使用以上方式挂载vhdx

也许需要代码环境，开发环境分开有点用

#### 配置自启动

在windows11上使用任务计划程序，触发器选择启动时，安全选项选择**不管用户是否启动都要运行**，且**使用最高权限运行**

### wsl代理

`wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理。`

在`C:\Users\<your name>`路径下创建`.wslconfig`文件并输入下面内容

```yaml
[experimental]
autoMemoryReclaim=gradual  
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
```

### 莫名显示更新失败

```batch
PS C:\Users\hh> wsl --list --verbose
WSL 正在完成升级...
Could not write value  to key \SOFTWARE\Classes\Directory\shell\WSL.   Verify that you have sufficient access to that key, or contact your support personnel.
更新失败(退出代码: 1603)。
错误代码: Wsl/CallMsi/Install/ERROR_INSTALL_FAILURE
```

参照[Could not write value to key \SOFTWARE\Classes\Drive\shell\WSL #11697](https://github.com/microsoft/WSL/issues/11697#issuecomment-2168104220)把注册表几个WSL相关的改名就好了

old:

- 计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\WSL
- 计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\background\shell\WSL
- 计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\WSL

new:

- 计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\WSL_bak
- 计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\background\shell\WSL_bak
- 计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\WSL_bak


### 显示配置不支持

```PS
(base) PS C:\Users\1> wsl 当前计算机配置不支持 WSL2。 请启用“虚拟机平台”可选组件，并确保在 BIOS 中启用虚拟化。 通过运行以下命令启用“虚拟机平台”: wsl.exe --install --no-distribution 有关信息，请访问 https://aka.ms/enablevirtualization Error code: Wsl/Service/CreateInstance/CreateVm/HCS/HCS_E_HYPERV_NOT_INSTALLED
```

[已解决 WSL / WSL2 请启用虚拟机平台 Windows 功能并确保在 BIOS 中启用虚拟化问题](https://cloud.tencent.com/developer/article/2443274)

开启hype-v
执行
```PS
bcdedit /set hypervisorlaunchtype auto
```

## NUC

### 拿到学校后重启失败

只有网络发生了变化，bios has detected unsuccessful post attempt
网上查询说关掉网络启动
成功

## Ubuntu 软件安装失败 提示not fully installed or removed

<https://blog.csdn.net/frank_gzb/article/details/52699536>

```bash
cd /var/lib/dpkg
sudo mv info info.bak
sudo mkdir info
```

然后apt-get install 或者 apt-get upgrade

再把info替换回去应该就可以了

## 使用Mobaxterm 时 zsh 的 highlight-suggestions 插件后面提示的灰色不生效

<https://blog.csdn.net/HideOnLie/article/details/107661295>
更改环境变量**TERM**即可
在`~/.zshrc`增加一句

```bash
export TERM=xterm-256color
```


## 更改启动内核

[ubuntu Linux多个内核选择_ubuntu中存在两种内核版本怎么办-CSDN博客](https://blog.csdn.net/zxnzjccmily/article/details/124601867)

查看已安装内核
```bash
dpkg --get-selections |grep linux-image
```

查看内核启动顺序
```bash
grep menuentry /boot/grub/grub.cfg
```

修改内核启动顺序
```bash
sudo gedit /etc/default/grub
```
将GRUB_DEFAULT=0注释，新增  
GRUB_DEFAULT=“Advanced options for Ubuntu>Ubuntu, with Linux 5.4.0-91-generic”

## 禁止内核更新

[如何禁止Ubuntu内核更新？](https://www.zhihu.com/question/617290612)



## rsync

使用rsync进行备份
[rsync 用法教程](https://www.ruanyifeng.com/blog/2020/08/rsync.html)


## 挂起

[Linux前台、后台、挂起、退出、查看命令汇总](https://www.cnblogs.com/insane-Mr-Li/p/11211532.html)

`ctrl+Z`可以挂起当前进程

查看挂起进程；带+表示最新的jobs；带-表示次新的jobs；其他jobs不带符号
```bash
jobs -l
```

将第n个挂起进程置于前台
```bash
fg %n
```

将第n个进程置于后台运行
```bash
bg %n
```

可以使用kill结束进程，使用ps查看进程号或使用jobs查看job号
```bash
kill %n
kill pid
```

在命令最后加&，可以把命令放到后台执行
```bash
watch  -n 10 sh  test.sh  &
```

nohup=no hang up，不挂断，如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令。该命令可以在你退出帐户/关闭终端之后继续运行相应的进程.长命令必须写在shell文件中，否则nohup不起作用  
```bash
nohup command &                 //该命令的一般形式  
nohup command > myout.file 2>&1 &      
//log输出到myout.file，并将标准错误输出重定向到标准输出，再被重定向到myout.file
```

## 华工vpn

华工 vpn EasyConnect 软件的 ubuntu版本安装了不能用
报错：段错误

在网上查询发现是依赖库版本不对
[<Linux开发＞ ubuntu开发工具-EasyConnect使用记录](https://blog.csdn.net/qq_39257814/article/details/128528556)
[Ubuntu 20.04安装Easyconnect及兼容性问题解决（亲测可用）](https://blog.csdn.net/weixin_37926734/article/details/123068318)

下载下面3个动态依赖库

>libpangocairo-1.0
>libpango-1.0
>libpangoft2-1.0

![[241022-191049724-Pasted image 20241022191047.png]]

解压deb包，再将其中的`data.tar.xz`解压，将其中`data/usr/lib/x86_64-linux-gnu/`的文件复制到EasyConnect安装的文件夹`/usr/share/sangfor/EasyConnect`下

完成后即可运行EasyConnect

## Netplan

使用netplan设置IP地址
[Netplan使用（高版本Ubuntu静态/动态IP设置方法）-CSDN博客](https://blog.csdn.net/bigbaojian/article/details/125396045)

配置文件在`/etc/netplan`目录中

配置完成后执行
```bash
sudo netplan apply
```


## 终端快捷键

[Linux的shell终端常用快捷键大全](https://zhuanlan.zhihu.com/p/29538650)

Ctrl + b：同键盘左键，向左移动光标。

Ctrl + f：同键盘右键，向右移动光标。

Alt + b：向前移动一个词的距离。如果shell终端配置启用了可以通过快捷键打开菜单，会发生冲突，解决办法是禁用这个功能或者和Shift键一起使用。

Alt + f：向后移动一个词的距离。如果shell终端配置启用了可以通过快捷键打开菜单，会发生冲突，解决办法是禁用这个功能或者和Shift键一起使用。

Esc + b：移动到当前单词的开头。

Esc + f：移动到当前单词的结尾。

Ctrl + a：移动光标到命令行首。

Ctrl + e：移动光标到命令行尾。

Ctrl + x + x：光标在命令行中光标最后两次出现的位置间进行切换。

## VirtualBox 虚拟机

### ssh

有时候是sshd，有时候是ssh；连接本地电脑的ip填localhost

### 端口转发

前面那个是电脑，后面是虚拟机

![[241026-135628148-Pasted image 20241026135626.png]]
## 组权限不生效

使用`usermod -aG <newgroup> <yourname>`时，组权限没生效
可以使用`newgrp <newgroup>` 使其立刻生效，可以用命令`id`或`groups`检查

## 重启后掉显卡驱动

[Linux之NVIDIA显卡驱动安装方法(重启失效解决方法)](https://blog.csdn.net/zong596568821xp/article/details/108824369)

