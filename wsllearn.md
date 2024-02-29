# 折腾WSL

## 安装

去微软商店安装即可

## 配置

有时候登录账户变了，默认wsl系统变了可以使用wslconfig配置

## 读取windows上文件很慢

可能因为linux文件系统是ext4，windows是NTFS，需要转化所以慢
解决方法是创建一个ext4的虚拟盘，挂载到linux上面使用；然鹅这样windows就不能读取了，没什么用，也许是wsl内存不够时挂载外部存储器使用；如果空间不够，可以把wsl移动到空间大的磁盘

```ps
wsl --mount --vhd "F:\wslF.vhdx" --name wslF -p1
```
使用以上方式挂载vhdx

