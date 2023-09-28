### 暴力破解

暴力破解是以一种疯狂尝试密码的一种行为。

```shell
  ? | Charset
 ===+=========
  l | abcdefghijklmnopqrstuvwxyz
  u | ABCDEFGHIJKLMNOPQRSTUVWXYZ
  d | 0123456789
  h | 0123456789abcdef
  H | 0123456789ABCDEF
  s |  !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~
  a | ?l?u?d?s
  b | 0x00 - 0xff
```

| key  | value                             | desc             |
| ---- | --------------------------------- | ---------------- |
| l    | abcdefghijklmnopqrstuvwxyz        | 代表所有小写字母 |
| u    | ABCDEFGHIJKLMNOPQRSTUVWXYZ        | 代表所有大写字母 |
| d    | 0123456789                        | 代表数字         |
| h    | 0123456789abcdef                  | 数字加小写字符   |
| H    | 0123456789ABCDEF                  | 数字加大写字符   |
| s    | !"#$%&'()*+,-./:;<=>?@[\]^_{\|}~ | 代表特殊字符     |
| a    | ?l?u?d?s                          | 包含{l, u, d, s} |
| b    | 0x00 - 0xff                       | 代表二进制       |

```shell
hashcat -a 3 -m 0 827ccb0eea8a706c4c34a16891f84e7b ?d?d?d?d?d
```

> `-1` `-2` `-3` `-4` 可以理解为，4个别名

```shell
 -1, --custom-charset1 | CS | User-defined charset ?1 | -1 ?l?d?u
 -2, --custom-charset2 | CS | User-defined charset ?2 | -2 ?l?d?s
 -3, --custom-charset3 | CS | User-defined charset ?3 |
 -4, --custom-charset4 | CS | User-defined charset ?4 |
```

```shell
hashcat -a 3 -m 0 "202cb962ac59075b964b07152d234b70" -1 ?a?a?a ?1
```

> 自定义字符范围


```shell
username@linux:~/docs$ cat char.c
01
username@linux:~/docs$ hashcat -a 3 -m 0 "7ae660824d7e77d3800bcada8409a05f" -1 char.c ?1?1 --show
```

> 暴力破解中文

```shell
hashcat -a 3 -m 0 密码文件 -1 ?h?H?s?a?b ?1?1?1?1
```

> 在暴力破解时可以

```shell
hashcat -a 3 -m 0 密码文件 -1 pwq?h?H?s?a?bpwd ?1?1?1?1

在如果知道多少位，某一位上的字符可以在暴力破解的规则中直接添加明文
```

