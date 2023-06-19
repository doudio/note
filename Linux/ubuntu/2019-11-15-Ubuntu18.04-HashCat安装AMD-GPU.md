> ## HashCat AMD GPU

> 0x01. 首先,购买组装好所有硬件,插上网线,加电开始测试,观察硬件是否全部工作正常,确认没问题以后,再继续后面的步骤,本次自己用于测试的各类硬件具体型号如下,一套下来三万五左右,这个配置作为GPU破解来讲并不算高,如果你不缺钱,完全可以买更好的:

```shell
supermicro超微7048GR-TR准系统 双路塔式工作站4 GPU运算服务器 |一台

Intel/英特尔 XEON至强 E5-2620 V3 15M 2.4G 6核12线 |2颗

金士顿 16G DDR4 REG ECC 2133 服务器内存条 |2根

三星(SAMSUNG) 850 PRO 512G SATA3 固态硬盘 |2块

NVIDIA技嘉GTX1070 Founders Edition 8G | 4张 32G GPU
```

> 0x02. 下载安装 ubuntu-14.04.5-desktop-amd64,直接将其做成系统启动U盘,具体下载地址如下:

```http
https://ubuntu.com/
```

> 安装系统,装完以后先进行一些必要的准备工作: 首先,全面更新系统

```shell
$ apt-get update && apt-get upgrade -y

# 对这样的大规模更新,完成后务必立即重启系统
$ shutdown -r now 

# 为了防止下面编译过程中出错,这里我就提前把对应的内核头文件都装上了
$ apt-get install linux-headers-`uname -r` -y 

$ shutdown -r now
```

> 重启没问题以后,再安装一些必要的工具,主要是opencl头文件和opencl相关的工具集….:

```shell
$ apt-get install build-essential lsb-core clinfo ocl-icd-opencl-dev opencl-headers ocl-icd-libopencl1 gcc git -y
```

> 0x03. 载编译安装 Intel OpenCL 驱动,具体地址如下:

```url
http://registrationcenter-download.intel.com/akdlm/irc_nas/9019/opencl_runtime_16.1_x64_ubuntu_5.2.0.10002.tgz
```

> 具体的安装过程就非常简单了,全部一键傻瓜化:

```shell
$ tar xf opencl_runtime_16.1_x64_ubuntu_5.2.0.10002.tgz

$ cd opencl_runtime_16.1_x64_ubuntu_5.2.0.10002/

$ bash install.sh

# 还是那句话,务必在装完以后立马重启机器
$ shutdown -r now

# 重启以后看看系统有没有真正识别opencl套件,如果没有识别,务必先把问题解决了再往下继续,否则都是徒劳
$ clinfo
```

> 0x04. 接着准备安装英伟达显卡驱动: 重启以后,记得不要登录图形界面系统,直接按Ctrl + Alt + F1 进入字符终端模式,进到内核模块目录,通过配置文件的方式,禁用nouveau驱动,操作如下:

```shell
$ cd /etc/modprobe.d/

$ touch blacklist-nouveau.conf

$ vi blacklist-nouveau.conf

    blacklist nouveau

    options nouveau modeset=0

$ update-initramfs -u

# 修改完配置以后立马重启机器
$ shutdown -r now
```

> 0x05. 下载安装英伟达显卡驱动,在安装过程中会有很多交互,根据实际需求进行选择: 同样是不要登录图形界面,按 Ctrl + Alt + F1 进到字符终端模式,并停掉相关的图形服务

```shell
# lightdm是一个Linux桌面显示管理器,在安装显卡驱动过程中需要把相关的图形服务全部停掉,安装完以后再起起来
$ /etc/init.d/lightdm stop

$ chmod +x NVIDIA-Linux-x86_64-375.20.run

$ bash NVIDIA-Linux-x86_64-375.20.run --no-opengl-files

$ modprobe nvidia

$ /etc/init.d/lightdm start

# 重新回到图形界面
$ shutdown -r now
```

> 0x06 下载编译安装最新版的 hashcat,具体的编译安装方法在压缩包的 BUILD.md 文件中[不过这个似乎还有些问题]已有说明,可先用下面的方法来装:

```shell
$ git clone https://github.com/hashcat/hashcat.git

$ cd hashcat/

# 为了防止下载缺少文件,请执行该语句
$ git submodule update --init --recursive

$ make && make install

$ echo $?

# 务必在装完以后立马重启机器
$ shutdown -r now
```

> 0x07 运行 hashcat 测试破解速度,调整GPU参数:

```shell
hashcat -b
```

> 0x08 准备好各种散列hash,进行实际的hash破解测试,测试常用算法的实际破解速度