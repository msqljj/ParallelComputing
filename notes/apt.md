# Ubuntu16.04更改apt源
## 一、Ubuntu16.04：控制台挂载/(卸载)U盘
### 1.将U盘插入

### 2.直接回车，然后输入以下命令
```
sudo fdisk -l
```
此时，会输出一大段问题，只看最后一段可以得到U盘
```
Device Boot Start End Sectors Size Id Type
/dev/sdb4 * 256 7866367 7866112 3.8G b W95 FAT32
```
### 3.挂载U盘
```
sudo mount -t vfat /dev/sdb4 /media 
//-t 后的vfat是文件系统格式，即FAT32
//dev/sdb4是需要挂载的U盘//media是挂载点
```
### 4.进入U盘
```
cd /media
```
### 5.卸载U盘
```
sudo umount /dev/sdb4
```
## 一、ubuntu16.04 server 更换apt-get阿里源
### 1.备份系统自带源
```
mv /etc/apt/sources.list /etc/apt/sources.list.bak
```
### 2.修改/etc/apt/sources.list文件
```
vim /etc/apt/sources.list  
```
加入如下内容
```
deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
deb http://archive.canonical.com/ubuntu xenial partner
deb-src http://archive.canonical.com/ubuntu xenial partner
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
```
### 3.更新生效
```
apt-get update
```
