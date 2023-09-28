> ## analysis-ik 分词器的配置

> ik 的分词器有

```json
Analyzer: ik_smart , ik_max_word , Tokenizer: ik_smart , ik_max_word

官方声明: 自 v5.0.0 起
	移除名为 ik 的analyzer和tokenizer,请分别使用 ik_smart 和 ik_max_word
```

> tree 结构

```json
IK/
├── commons-codec-1.9.jar
├── commons-logging-1.2.jar
├── config
│   ├── extra_main.dic
│   ├── extra_single_word.dic
│   ├── extra_single_word_full.dic
│   ├── extra_single_word_low_freq.dic
│   ├── extra_stopword.dic
│   ├── IKAnalyzer.cfg.xml
│   ├── main.dic
│   ├── preposition.dic
│   ├── quantifier.dic
│   ├── stopword.dic
│   ├── suffix.dic
│   └── surname.dic
├── elasticsearch-analysis-ik-7.4.2.jar
├── elasticsearch-analysis-ik-7.4.2.zip
├── httpclient-4.5.2.jar
├── httpcore-4.4.4.jar
├── plugin-descriptor.properties
└── plugin-security.policy

1 directory, 20 files
```

> ik 的重要配置文件 :: IKAnalyzer.cfg.xml

```xml
<comment>IK Analyzer 扩展配置</comment>
<!--用户可以在这里配置自己的扩展字典 -->
<entry key="ext_dict"></entry>
<!--用户可以在这里配置自己的扩展停止词字典-->
<entry key="ext_stopwords"></entry>
<!--用户可以在这里配置远程扩展字典 -->
<!-- <entry key="remote_ext_dict">words_location</entry> -->
<!--用户可以在这里配置远程扩展停止词字典-->
<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
```

* config 下的其他 *.dic 是ik自带的一些词典
* 配置多本字典时以`;`分割
* `ext_dict` 扩展词典，`ext_stopwords` 需要被忽略排出的词
* 配置好后建议重启一下

> 修改 analysis-ik 内部实现达到定时从 mysql 库中检索扩展词和忽略词

> 实现步骤为

* 下载到 ik 的源码这个在 github 上可以找到
* 将项目解压并导入到 IDE 中
* 在源码中添加逻辑

```shell
Dictionary类大概[169]行; 在Dictionary 的初始化方法中我们创建一个守护线程在后台执行

Dictionary类大概[389]行: this.loadMySQLExtDict();
Dictionary类大概[683]行: this.loadMySQLStopwordDict();
```

* 打包 maven 项目
* 将打包好的项目导入到es插件库中顺便加入mysql连接驱动
* 重启es改好的代码在es中执行起来