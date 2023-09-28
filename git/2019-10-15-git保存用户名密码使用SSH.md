> git 保存用户名密码使用SSH

> 进入指定目录

```cmd
$ cd ~
```

> 创建一个 ssh 目录: $ ssh-keygen -t rsa -C `用户邮箱`

```cmd
Administrator@JavaEE MINGW64 ~
$ ssh-keygen -t rsa -C jiangruyi@jiangruyi.com
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa.
Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:MDUElmLB/X3FtTUxdY37+aLaeFo4SHm/4i3q2IwDkLM jiangruyi@jiangruyi.com
The key's randomart image is:
+---[RSA 2048]----+
|   ..oo++    . *O|
|    +.o. .    + B|
|   o .o. .   . o |
|  +    o... . .  |
|   +    S ..   ..|
|  E .  . o o   ..|
|     .  . o o   .|
|      .=  o*... .|
|      oo=o=*=. . |
+----[SHA256]-----+
```

> 进入 ssh 目录

```cmd
Administrator@JavaEE MINGW64 ~
$ cd .ssh/
```

> 查看文件, 拷贝结果在 git 服务器中添加 SSH Keys

```cmd
$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLLL9jPTQOKX+C4YGFkdCi1BamSFukWvTvcLWOh9hol5w+4i9yAUbxBQTxU9czsn5s7LnZ3VYV+X5Nz/jw/LvaBTDTXNOXsESa6MBaR46PgfgbRCHMnGOCTDRn1M1Sq7eS+jfMWMSCmpbGNtoQiwFvAjpU0WKSlj/CoQCi4yWr9IsysTEd5EAoOXs6VMuDPadYl+Xp1ozX6AuFVLDyV/WY6vojI9ZOohgGHPnpdEry84GzWtzXITdPv3FMLdeZFmmhTx7yyNxQ2rptPUCp5QI0aJRkdAfbuiXzSlgmY1aEGLblZHaKhgJXVsUhJ01ZUb8wVBntfQ5ZwPws0HRUMXdr jiangruyi@jiangruyi.com
```

> 进入工作区, 查看提交的`origin`

```cmd
$ git remote -v
origin  http://www.znsd.com/jiangruyi/test.git (fetch)
origin  http://www.znsd.com/jiangruyi/test.git (push)
```

> 添加ssh地址

```cmd
$ git remote add origin_ssh git@www.znsd.com:jiangruyi/test.git

Administrator@JavaEE MINGW64 /d/git_test/test (test)
$ git remote -v
origin  http://www.znsd.com/jiangruyi/test.git (fetch)
origin  http://www.znsd.com/jiangruyi/test.git (push)
origin_ssh      git@www.znsd.com:jiangruyi/test.git (fetch)
origin_ssh      git@www.znsd.com:jiangruyi/test.git (push)
```

> 修改 提交