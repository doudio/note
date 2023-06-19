> ## SpringBoot 加密部署

* 通过开源的 https://github.com/core-lib/xjar.git 来实现对项目的低侵入加密部署

> 在项目中添加依赖

```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>

<dependency>
    <groupId>com.github.core-lib</groupId>
    <artifactId>loadkit</artifactId>
    <version>v1.0.1</version>
</dependency>
```

> 编译项目

> 创建一个简单的 maven 工程加入加密端依赖

```xml
<dependency>
    <groupId>com.github.core-lib</groupId>
    <artifactId>xjar</artifactId>
    <version>v2.0.6</version>
</dependency>
```

> 在 main 函数中编写加密代码

```java
public static void main(String[] args) throws Exception {
    XKey xKey = XKit.key("password");
    // SpringBoot Jar 包加密
    XBoot.encrypt("E:/code/demo/target/demo-0.0.1-SNAPSHOT.jar",
                  "E:/code/demo/target/demo.jar", xKey);
    // SpringBoot Jar 包解密
    XBoot.decrypt("E:/code/demo/target/demo.jar", 
                  "E:/code/demo/target/demo-decrypted.jar", xKey);
}
```

> 启动项目的两种方式

* 命令行运行JAR 然后在提示输入密码的时候输入密码后按回车即可正常启动

```shell
java -jar /path/to/encrypted.jar
```

* 对于 nohup 这种后台启动方式，无法使用控制台来输入密码，推荐使用指定密钥文件的方式启动

```shell
nohup java -jar encrypted.jar --xjar.keyfile=/path/to/pass.key > encrypted.log &
```

> 密钥文件格式

```
password: PASSWORD
algorithm: ALGORITHM
keysize: KEYSIZE
ivsize: IVSIZE
hold: HOLD
```

> 配置说明, 密钥文件在读取后将自动删除。

| 参数名称  | 参数含义 | 缺省值 | 说明                                 |
| --------- | -------- | ------ | ------------------------------------ |
| password  | 密码     | 无     | 密码字符串                           |
| algorithm | 密钥算法 | AES    | 支持JDK所有内置算法，如AES / DES ... |
| keysize   | 密钥长度 | 128    | 根据不同的算法选取不同的密钥长度。   |
| ivsize    | 向量长度 | 128    | 根据不同的算法选取不同的向量长度。   |
| hold      | 是否保留 | false  | 读取后是否保留密钥文件。             |