# windows系统遇到的问题

## 安装完没有显卡驱动

### 核显

是intel的cpu，可以去intel官网下载一个安装驱动的助手软件，能够自动识别需要的驱动并安装

### 独显

找卖家要驱动；或是英伟达显卡可以直接去官网下载对应驱动

## 远程桌面

可以使用mstsc

默认端口是3389

可以到注册表修改默认端口

在路径`计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp`下找到端口进行修改，注意选择十进制

