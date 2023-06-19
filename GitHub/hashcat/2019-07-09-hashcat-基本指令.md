## hashcat 基本指令

### 字典破解

```shell
hashcat -a 0 -m 0 密码文件 字典文件 --show
```

### -a Attack Modes (攻击模式)

```shell
- [ Attack Modes ] -

  # | Mode
 ===+======
  0 | Straight
  1 | Combination
  3 | Brute-force
  6 | Hybrid Wordlist + Mask
  7 | Hybrid Mask + Wordlist
```

| key  | value                  | desc                               |
| ---- | ---------------------- | ---------------------------------- |
| 0    | Straight               | 字典破解                           |
| 1    | Combination            | 组合模式，多字典破解               |
| 3    | Brute-force            | 暴力破解                           |
| 6    | Hybrid Wordlist + Mask | 字典源码模式，先跑字典，后暴力破解 |
| 7    | Hybrid Mask + Wordlist | 源码字典模式，先暴力破解，后跑字典 |

### -m Hash modes（哈希模式）（密码类型）[ 哈希 可以理解为 密码 ]

```shell
https://hashcat.net/wiki/doku.php?id=hashcat
```

| key  | desc              |
| ---- | ----------------- |
| 0    | MD5类型的密码     |
| 5100 | 16位的MD5密码     |
| 4300 | 被加密了两次的MD5 |

### 系统参数

| param          | desc                     |
| -------------- | ------------------------ |
| --show         | 查询上一次破解的值       |
| -o             | 指定输出文件             |
| -p             | 指定哈希值与密文的分割符 |
| -c             | 字典在缓存中的大小       |
| -O             | 优化模式                 |
| --status                | 设置自动输入日志 |
| --status-timer | 设置输出日志的时间（**秒数**） |
| --force | 跳过异常 |
| --segment-size 512 | 字典缓存大小 |

