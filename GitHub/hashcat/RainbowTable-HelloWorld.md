## RainbowTable HelloWorld

> 解压 `./rainbowcrack-win32_veryhuo.com.zip` 目录下的文件

> 在里面打开命令窗口（cmd）

> 先生成彩虹表

```shell
rtgen md5 byte 1 7 5 2400 40000 all
```

> 将彩虹表进行排序

```shell
rtsort *.rt
```

> 利用彩虹表破解密码

```shell
rcrack *.rt -h 3295c76acbf4caaed33c36b1b5fc2cb1
```

> 压缩包内有详细介绍

```shell
WIN10

rtgen md5 byte 1 7 5 2400 40000 all

rtsort.exe .

rcrack.exe rainbowcrack-1.7-linux64.zip\ . -h 3295c76acbf4caaed33c36b1b5fc2cb1
```

