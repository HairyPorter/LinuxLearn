# 基于Docker在Ubuntu上部署nextcloud

- [基于Docker在Ubuntu上部署nextcloud](#基于docker在ubuntu上部署nextcloud)
  - [1.Docker安装](#1docker安装)
    - [1.1卸载旧版本Docker](#11卸载旧版本docker)
    - [1.2换源（可选）](#12换源可选)
    - [1.3使用Apt下载Docker](#13使用apt下载docker)
      - [1.3.1建立 Docker's Apt 仓库](#131建立-dockers-apt-仓库)
      - [1.3.2安装Docker包](#132安装docker包)
      - [1.3.3确认安装成功](#133确认安装成功)
      - [1.3.4更新Docker](#134更新docker)
  - [2.安装mysql](#2安装mysql)
    - [2.1获取mysql镜像并启动容器](#21获取mysql镜像并启动容器)
  - [3.安装nextcloud](#3安装nextcloud)
    - [3.1获取并安装nextcloud镜像](#31获取并安装nextcloud镜像)
  - [4.安装插件（可选）](#4安装插件可选)
    - [4.1Preview Generator](#41preview-generator)
      - [4.1.1安装](#411安装)
    - [4.1.2 mp4文件没有预览图](#412-mp4文件没有预览图)
    - [4.1.3 pdf文件没有缩略图](#413-pdf文件没有缩略图)
  - [5.内网穿透（可选）](#5内网穿透可选)

## 1.Docker安装

参考[docker docs在Ubuntu上安装Docker Engine](https://docs.docker.com/engine/install/ubuntu/)

### 1.1卸载旧版本Docker

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

### 1.2换源（可选）

可以使用[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/debian/)

启用源码镜像，加快apt-get。一般来说，只需要替换以下文件即可换源。

```bash
/etc/apt/sources.list 
```

当时23/10/15镜像源  

```bash
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware

deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bookworm-security main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bookworm-security main contrib non-free non-free-firmware

# deb https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
# deb-src https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
```

### 1.3使用Apt下载Docker

#### 1.3.1建立 Docker's Apt 仓库

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

#### 1.3.2安装Docker包

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### 1.3.3确认安装成功

```bash
sudo docker run hello-world
```

#### 1.3.4更新Docker

重复[安装Docker包](#132安装docker包)操作

## 2.安装mysql

可以指定用户使用docker，就不需要sudo了

```bash
sudo usermod -aG docker username
sudo systemctl restart docker
```

参考[CSDN docker部署nextcloud+mysql+onlyoffice](https://blog.csdn.net/u011740601/article/details/115790720)

参考知乎[基于Docker安装nextcloud](https://zhuanlan.zhihu.com/p/107820215)

### 2.1获取mysql镜像并启动容器

获取mysql镜像

```bash
docker pull mysql  #当时版本是8.1.0
```

启动mysql容器

```bash
docker run --name=nextcloud_db \  #容器名称是nextcloud_db
-e MYSQL_ROOT_PASSWORD=ROOTPASSWD \  #mysql root 密码
-v /nc_mysql/conf:/etc/mysql/conf.d \  #把mysql配置文件数据放
-v /nc_mysql/data:/var/lib/mysql \  #在本地，方便配置和迁移
-d -p 33306:3306 \  #容器3306端口映射到本地33306，后面创建了容器之间的链接，所以没用到
--restart=always \  #Docker重启时容器也重启
mysql
```

进入nextcloud_db容器创建nextcloud数据库

```bash
docker exec -it nextcloud_db mysql -u root -p  #进入容器，以root用户打开数据库
CREATE DATABASE nextcloud;  #创建nextcloud数据库
GRANT ALL ON *.* TO 'root'@'%';
flush privileges;
exit;
```

## 3.安装nextcloud

参考[CSDN docker部署nextcloud+mysql+onlyoffice](https://blog.csdn.net/u011740601/article/details/115790720)

参考知乎[基于Docker安装nextcloud](https://zhuanlan.zhihu.com/p/107820215)

### 3.1获取并安装nextcloud镜像

获取nextcloud镜像

```bash
docker pull nextcloud  #当时版本是27.1.2
```

```bash
docker run -d \  #-d表示后台运行
--name=nextcloud \  #容器名称为nextcloud
--privileged \  
--link nextcloud_db:db \  #连接之前创建的数据库
-v /nc:/var/www/html \  #把数据放在本地方便迁移
-p 48080:80 \  #把容器80端口映射到本地48080端口
--restart=always \  #Docker重启时容器也重启
nextcloud
```

浏览器输入127.0.0.1:48080进入nextcloud，设置nextcloud管理员账号；数据库选择mysql，由上到下分别填写

- root  （数据库用户）
- ROOTPASSWD  （数据库密码）
- nextcloud_db （数据库名）
- db （数据库地址，之前容器之间创建了连接）

## 4.安装插件（可选）

### 4.1Preview Generator

#### 4.1.1安装

服务器性能较差，使用该插件可以预先生成缩略图  
可以在浏览器上登录nextcloud在应用管理处安装，也可以手动安装。在[https://apps.nextcloud.com/](https://apps.nextcloud.com/)搜索Preview Generator手动下载，把插件包解压后复制到
> /var/www/html/apps

随后在浏览器上启用插件。

第一次启用插件需要生成所有文件的缩略图

```bash
sudo docker exec --user www-data -it nextcloud php /var/www/html/occ preview:generate-all -vvv
```

然后加入crontab（我选择了10min一次）
然后在crontab -e 加入以下计划（我选择了10min一次）

```bash
crontab -e  
*/10 * * * * docker exec --user www-data -i nextcloud php /var/www/html/occ preview:pre-generate -vvv
```

### 4.1.2 mp4文件没有预览图

参考[Nextcloud: Install Preview Generator](https://www.allerstorfer.at/nextcloud-install-preview-generator/)完成添加。

### 4.1.3 pdf文件没有缩略图

参考[How can I enable PDF preview](https://help.nextcloud.com/t/how-can-i-enable-pdf-preview/90303)  
注释掉"Imagemagick security policy"的部分内容

## 5.内网穿透（可选）

服务器不在公网，可以选择使用frp进行内网穿透。

使用systemd实现frp开机启动，在/usr/lib/systemd/system/目录下添加StartupFrp.service文件，填入以下内容

```bash
[Unit]
Description=StartupFrp
After=network-online.target

[Service]
ExecStart=******  #frp启动脚本

[Install]
WantedBy=multi-user.target
```

```bash
systemctl enable StartupFrp.service  #启动服务
```
