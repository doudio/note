> ## 05-hashcat-文件破解

> ### 破解 7z 文件

> #### 跟新系统并安装所需依赖

```shell
apt-get update && apt-get upgrade

apt-get install lzma
apt-get install liblzma-dev
```

> #### 下载到：[Compress-Raw-Lzma](https://metacpan.org/release/Compress-Raw-Lzma) 依赖

```shell
tar -zxvf Compress-Raw-Lzma-2.086.tar.gz
cd Compress-Raw-Lzma-2.086
perl Makefile.PL
make 
make test
make install
```

> #### 安装 git 开始获得脚本

```shell
apt-get install git
git clone https://github.com/philsmd/7z2hashcat.git
cd 7z2hashcat
```

> #### 执行指令获取 7z 文件中的密文

```shell
./7z2hashcat.pl file.7z
```

> #### 拿到最终的密文交给 hashcat 开始破解

> ### 破解 zip,rar,pdf,office 系列

> #### 下载到 [john 工具包](https://www.openwall.com/john/) ，直接使用工具包中的脚本获取到文件密文

> ##### 获取 rar 文件执行

```shell
./rar2john file.rar
```

> ##### 获取 zip 文件执行

```shell
./zip file.zip
```

> ##### 获取 pdf 文件执行

```shell
python ./pdf2hashcat.py file.pdf
```

> ##### 获取 doc,docx,xls,xlsx,ppt,pptx 文件执行

```shell
python ./office2hashcat.py file.[doc,docx,xls,xlsx,ppt,pptx]
```

> #### 拿到最终的密文交给 HashCat 来破解

> ### 各种依赖在同级目录 lib 中有备份
