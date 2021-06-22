> ## HashCat 根据键盘布局生成密码

> HashCat 其他项目

```shell
hashcat - World's fastest and most advanced password recovery utility									:: 破解密码的工具
hashcat-utils - Small utilities that are useful in advanced password cracking							:: 文件格式转换工具
maskprocessor - High-performance word generator with a per-position configureable charset				:: 字典生成
statsprocessor - Word generator based on per-position markov-chains										:: 字典生成工具可以选择：min ~ max
princeprocessor - Standalone password candidate generator using the PRINCE algorithm					:: 字符拼接器
kwprocessor - Advanced keyboard-walk generator with configureable basechars, keymap and routes			:: 根据键盘走势生成弱密码
```

> 安装 GIt 拉取 kwprocessor 项目

```shell
 $ apt-get install git
 
 $ git clone https://github.com/hashcat/kwprocessor.git
```

> 目录结构介绍

```shell
basechars/	:: 基础表
	|- full.base
	|- tiny.base

keymaps/	:: 键盘布局
	|- de.keymap
	|- en-gb.keymap
	|- en-us.keymap	- default 美式键盘
	|- es.keymap
	|- fr.keymap
	|- pt.keymap
	|- ru.keymap
	|- sv.keymap

routes/		:: 路由规则
	|- 2-to-10-max-2-direction-changes-combinator.route
	|- 2-to-10-max-3-direction-changes.route
	|- 2-to-16-max-3-direction-changes.route
	|- 2-to-16-max-4-direction-changes.route
	|- 2-to-32-max-5-direction-changes.route
	|- 2-to-4-exhaustive-prince.route
	|- 4-to-4-exhaustive.route
```

> 操作指令

```shell
# 根据键盘头生成
kwp ./basechars/full.base ./keymaps/en-us.keymap ./routes/2-to-10-max-2-direction-changes-combinator.route -o out.file | head

# 根据键盘体生成
kwp ./basechars/full.base ./keymaps/en-us.keymap ./routes/2-to-10-max-2-direction-changes-combinator.route -o out.file | tail
```

> 路由规则

```shell
212
222
```

> 212

```
从任意建开始：
	2 连续 2 个键
	1 中转 1 个键
	2 回去 2 个键

例如：
	2[qw]1[ed]2[sa]
	2[qw]2[edc]2[xz]
```

> 四位的路由规则

```shell
第一位	:: x 轴个数
第二位	:: Y 轴个数
第三位	:: X 轴个数
第四位	:: y 轴个数

	y
X	+	x
	Y
```

