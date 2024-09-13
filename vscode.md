# vscode 使用笔记

## vscode 使用 remote-ssh 免密登录

### vscode安装插件

vscode 安装 Remote-SSH 插件

### 连接远程服务器

点击左下角的><标志连接；输入密码连接，vscode会自动配置

### 上传密钥

本地电脑如果没有生成过ssh密钥，打开命令行输入以下命令

```bash
ssh-keygen
```

密钥一般存储在`C:\Users\xxxx\.ssh`

把id_rsa.pub文件上传到主机 **~/.ssh** 目录下，并重命名为 **authorized_keys** ，权限设置为600，并执行 **service sshd restart**

```bash
mkdir .ssh
mv id_rsa.pub .ssh
cd .ssh
cat id_rsa.pub >> authorized_keys
sudo chmod 600 authorized_keys
service sshd restart
```

### 关闭密码登录

打开`/etc/ssh/sshd_config`,把**PasswordAuthentication** 设置为 no
随后重启sshd

```bash
systemctl restart sshd.service
```

> 注意：设置的会话先不要关，测试一下是否可以登录，避免设置错误无法登录

## 使用vscode 编辑 python 代码

需要安装python插件


### python默认解释器设置

在上方搜索`python interpreter`设置即可；
也可以在设置中修改


### pylance在启动时崩溃

观察输出，是交互式界面引起的，使用完记得关闭即可

### pylance的自动导入不生效

使用了anaconda虚拟环境，需要在`python.autoComplete.extraPaths`手动添加`site-packages`的路径