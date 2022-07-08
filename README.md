# 1、前言

实验室深度学习服务器从硬件到系统和软件上的配置，不使用外包的服务器，从装机到管理全程自己摸索，从而实现实验室多用户共同共用服务器资源，目前服务器上所有的硬件配置就都是INTEL和NVIDIA，系统上使用Ubuntu系统，同样其他Linux操作系统也可以，但是目前来说Ubuntu对于深度学习服务器来说还是适用性最广的。

# 2、硬件方面

服务器的配置方面，首先是CPU，普遍选择Intel至强系列芯片居多，当然最近几年AMD确实是香，但是从软件兼容性角度考虑，还是选择Intel；显卡方面就不必多说了RTX30系列真的香；关于电源方面，推荐美商海盗船的电源，一定要选择有质量保障的，电源功率方面最好选择较大一点的根据显卡具体情况而定；关于散热方面，可选择风冷也可以选择水冷，水冷噪音会小一点；关于内存条方面，运行内存越大越好对于深度学习的训练速度有帮助；关于存储硬盘部分，机械硬盘选择西数或者希捷硬盘，固态硬盘选择三星的固态。

# 3、系统方面

系统方面选择Ubuntu20.04版本，比较新且比较稳定好看

## 3.1、安装Ubuntu20.04

- 下载官方镜像文件[Ubuntu20.04官方镜像](https://ubuntu.com/#download)
- 使用[Runfus](https://github.com/pbatard/rufus/releases/download/v3.13/rufus-3.13.exe)制作U盘启动器，给电脑装上Ubuntu系统

## 3.2 换镜像源

- 备份原来的镜像源文件

```
cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

- 更改镜像源文件的内容

```
sudo vim /etc/apt/sources.list
```

- 将文件里的所有内容替换为以下阿里源(阿里的镜像源亲测比较快而且好用)

```
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
 deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
 deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
 deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
 deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
 deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
 deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
 deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
 deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
 deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

- 更新一下

```
sudo apt update
```

## 3.3 关闭自动休眠

- 关闭自动休眠

```
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

- 查看自动休眠是否关闭

```
systemctl status sleep.target
```

## 3.4 挂载硬盘

- 显示硬盘及所属分区情况

```
 sudo fdisk -l
```

- 如果是完全空的全新的硬盘插上去先对其分区，如果已经分区格式化好了就跳过此步

```
# 表示将分区格式化成ext4文件系统类型
sudo mkfs -t ext4 /dev/sdb
```

- 挂载硬盘分区，显示硬盘挂载情况

```
df -l
```

- 在/media/目录下创建挂载文件夹

```
sudo mkdir /media/Harddisk/
```

- 配置硬盘在系统启动自动挂载

```
sudo vim /etc/fstab

# 在该文件最后一行添加
/dev/sdb     /media/Harddisk    ext4     defaults       0 0
```

- 重新启动即可

## 3.5 更改pip镜像源

- 更改配置文件

```
cd ~
mkdir .pip
sudo vim ~/.pip/pip.conf

# 加入下面内容
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple/ 
[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
```

# 4、软件方面

## 4.1 配置Windows的远程桌面xrdp

- 安装轻量型的远程控制桌面Xfce

```
sudo apt-get install xfce4
```

- 安装xrdp协议

```
sudo apt-get install xrdp 
```

- 启动xrdp协议

```
sudo adduser xrdp ssl-cert  
sudo systemctl restart xrdp
```

- 配置xfce环境

```
echo xfce4-session >~/.xsession
```

## 4.2 Ubuntu添加多用户

- 添加用户

```
sudo adduser 用户名
```

- 根据提示设置用户名的密码
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/8d0a869fed5f43b9bea79f354dd334e7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAWXVyaUZhbg==,size_19,color_FFFFFF,t_70,g_se,x_16&ynotemdtimestamp=1657248682225)
- 设置用户权限，这里是给全部权限

```
sudo gedit /etc/sudoers
```

- 打开文件后添加内容

```
用户名   ALL=(ALL:ALL)  ALL
```

- 切换到用户

```
su 用户名
```

- 配置用户的远程桌面环境

```
echo xfce4-session >~/.xsession
```

## 4.3 配置Zerotier内网穿透

- 注册登录Zerotier并创建一个网络
- Ubuntu安装zerotier

```
curl -s https://install.zerotier.com | sudo bash
```

- 将服务器加入zerotier的网络中即刚刚创建的网络

```
sudo zerotier-cli join 你的network ID
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a146490ce2f7408886b5425439a5796b.png?ynotemdtimestamp=1657248682225)

- 找到加入的设备然后打勾允许加入
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/ad08db0cb6bf4b68a13844f61a010173.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAWXVyaUZhbg==,size_20,color_FFFFFF,t_70,g_se,x_16&ynotemdtimestamp=1657248682225)
- Windows下使用远程桌面访问，win+R输入mstsc然后输入服务器分配的用户名和密码即可登录

## 4.4 安装显卡驱动和CUDA和Cudnn

- 显卡驱动直接在软件和更新里面选择——附加驱动然后根据显卡型号选择显卡驱动版本，一般选择470的驱动，然后对应的CUDA版本到时候安装11.4
- 去官网下载CUDA安装包[CUDA下载官网](https://developer.nvidia.com/cuda-toolkit-archive)，不要下载最新的CUDA
- 选择runfile(local)，然后运行网页上对应提示的命令进行安装
- 前面已经卸载过旧版本了直接Continue就好。然后根据提示选择安装选项，注意不要勾选第一个安装显卡驱动的，因为之前已经安装过了。
- 配置CUDA环境变量

```
sudo vim ~/.bashrc

# 最后一行加入(注意把文件夹名改成对应安装的CUDA文件夹名)
export CUDA_HOME=/usr/local/cuda-11.0
export LD_LIBRARY_PATH=${CUDA_HOME}/lib64
export PATH=${CUDA_HOME}/bin:${PATH}

# 生效环境变量
source ~/.bashrc
```

- 进入到CUDNN的下载官网[cudnn下载](https://developer.nvidia.com/rdp/cudnn-download)，选择对应CUDA版本的cudnn
- 解压压缩包

```
 tar -xzvf cudnn-11.0-linux-x64-v8.0.5.39.tgz
```

- 使用以下两条命令复制这些文件到CUDA目录下

```
# 注意改一下对应的文件夹名
 sudo cp cuda/lib64/* /usr/local/cuda-11.0/lib64/
 sudo cp cuda/include/* /usr/local/cuda-11.0/include/
```

- 拷贝完成之后，可以使用以下命令查看CUDNN的版本信息：

```
 cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

# 5、服务器可能存在的问题以及解决方法

在服务器使用过程中，服务器可能存在一些异常状况导致服务器无法开机，死机，无法连接等

## 5.1 容天三卡2080Ti服务器出现上电后无法进行远程连接并且接显示屏也没有画面输出

```
解决方法： 这种情况目前遇到过的原因可能是因为没有服务器没有连接外接键盘，因为有些系统
在进BIOS的时候需要接外接设备即键盘然后才会进入系统，因此这个情况很可能就是这种原因导致
并且使得无图像输出到显示屏上，因此以为系统没有进BIOS，实际上已经进入BIOS系统了，只是由
于外接设备没有输入的原因才导致没有显示，所以接上键盘外接设备即可
```

```
我的电脑启动后提示要接键盘，不接的话就进不去系统解决方法:
1、借个键盘。
2、在启动时按DEL进入BIOS(系统设置)一个蓝色的界面。
3、进入第一个选项(standard CMOS Features)。
4、光标移动到下面那个关于系统自检的选项(Halt on)。
5、里面就有All Error及AlI Error but... [选第二个的]。
6、然后Keyboard项就会被激活[把键盘的检查]取消(Disabled)掉。
7、去掉键盘,自行启动电脑就好了。

```

