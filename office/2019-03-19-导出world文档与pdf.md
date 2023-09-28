## 导出 world

* 项目结构

```shell
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           └── office
    │   │               ├── utils
    │   │               │   └── FtlToWordUtil.java
    │   │               └── WordMain.java
    │   └── resources
    │       ├── application.properties
    │       └── templates
    │           └── word
    │               └── XXX_MODEL.ftl
```

* XXX_MODEL.ftl 模板文件生成

---

1. Wins 中创建 .doc 或者 .docx 文件
2. 编辑好基础模板后 另存为 *.xml 格式的
   1. `Word XML 文档(*.xml)` freemarker 后格式 `.docx`
   2. `Word 2003 XML 文档(*.xml)` freemarker 后格式 `.doc`
3. 找一个XML格式化的[工具](http://www.ab173.com/format/xml.php) 给另存为的文件格式化
4. 编辑XML中的动态部分下面是一些常用语法

```xml
# 正常取值
<![CDATA[${user_name?if_exists}]]>
# 迭代List数据
<#if stu_list?exists>
	<#list stu_list as item>
		<w:t>${item_index?if_exists+1}</w:t>
		<w:t><![CDATA[${item.name?if_exists}]]></w:t>
	</#list>
</#if>
```

* 导入 freemarker pom

```xml
<dependency>
  <groupId>org.freemarker</groupId>
  <artifactId>freemarker</artifactId>
  <version>2.3.31</version>
</dependency>
```

* 模板文件生成好后创建 FtlToWordUtil

```java
package com.example.office.utils;

import freemarker.template.Configuration;
import freemarker.template.DefaultObjectWrapper;
import freemarker.template.Template;
import freemarker.template.TemplateExceptionHandler;
import lombok.extern.slf4j.Slf4j;
import sun.misc.BASE64Encoder;

import java.io.*;
import java.util.Locale;
import java.util.Map;

@Slf4j
public class FtlToWordUtil {

    private static final String ENCODING = "UTF-8";
    private static Configuration cfg = new Configuration(Configuration.VERSION_2_3_31);

    public static final String XXX_MODEL = "XXX_MODEL.ftl";

    static {
        cfg.setClassForTemplateLoading(FtlToWordUtil.class, "/templates/word");
        cfg.setEncoding(Locale.getDefault(), ENCODING);
        cfg.setObjectWrapper(new DefaultObjectWrapper(Configuration.VERSION_2_3_31));
        cfg.setTemplateExceptionHandler(TemplateExceptionHandler.IGNORE_HANDLER);
    }

    public static Template getTemplate(String templateFileName) throws IOException {
        return cfg.getTemplate(templateFileName, ENCODING);
    }

    /**
     * Generate files based on data and template
     * @param data             Map Data result set for
     * @param templateFileName ftl Template file name
     * @param outFilePath      Build file name (with path)
     */
    public static File crateFile(Map<String, Object> data, String templateFileName, String outFilePath) {
        Writer out = null;
        File outFile = new File(outFilePath);
        try {
            Template template = getTemplate(templateFileName);
            if (!outFile.getParentFile().exists()) {
                outFile.getParentFile().mkdirs();
            }
            out = new OutputStreamWriter(new FileOutputStream(outFile), ENCODING);
            template.process(data, out);
            out.flush();
        } catch (Exception e) {
            log.error(e.getMessage(), e);
        } finally {
            try {
                if (out != null) {
                    out.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return outFile;
    }

    public static String getImageBase(String src) throws Exception {
        if (src == null || src == "") {
            return "";
        }
        File file = new File(src);
        if (!file.exists()) {
            return "";
        }
        InputStream in = null;
        byte[] data = null;
        try {
            in = new FileInputStream(file);
            data = new byte[in.available()];
            in.read(data);
            in.close();
        } catch (IOException e) {
            log.error(e.getMessage(), e);
        }
        BASE64Encoder encoder = new BASE64Encoder();
        return encoder.encode(data);
    }

}
```

* 创建 Manin 方法运行, 到这里就导出好了

```java
public static void main(String[] args) {
    Map<String, Object> dataMap = new HashMap<>();
    FtlToWordUtil.crateFile(dataMap, FtlToWordUtil.XXX_MODEL, "/data/tmp/out/xxx报告.doc");
}
```

## world 转 pdf

* 项目结构

```shell
└── office
    ├── pom.xml
    └── src
        ├── main
        │   ├── java
        │   │   └── com
        │   │       └── example
        │   │           └── office
        │   │               ├── OfficeDemoApplication.java
        │   │               ├── PdfMain.java
        │   │               ├── utils
        │   │               │   ├── AsposeUtil.java
        │   │               │   └── FtlToWordUtil.java
        │   │               └── WordMain.java
        │   ├── resources
        │   │   ├── application.properties
        │   │   ├── License.xml
        │   │   └── templates
        │   │       └── word
        │   │           ├── XXX_MODEL.ftl
        │   └── web
        │       └── WEB-INF
        │           └── lib
        │               └── aspose-words-15.8.0-jdk16.jar
```

* 添加pom

```xml
<dependency>
    <groupId>com.aspose</groupId>
    <artifactId>aspose-words</artifactId>
    <version>15.8.0</version>
    <scope>system</scope>
    <systemPath>${basedir}/src/main/web/WEB-INF/lib/aspose-words-15.8.0-jdk16.jar</systemPath>
</dependency>

<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
        <includeSystemScope>true</includeSystemScope>
      </configuration>
    </plugin>
  </plugins>
</build>
```

* resources 目录下添加 License.xml

```xml
<License>
    <Data>
        <Products>
            <Product>Aspose.Total for Java</Product>
            <Product>Aspose.Words for Java</Product>
        </Products>
        <EditionType>Enterprise</EditionType>
        <SubscriptionExpiry>20991231</SubscriptionExpiry>
        <LicenseExpiry>20991231</LicenseExpiry>
        <SerialNumber>8bfe198c-7f0c-4ef8-8ff0-acc3237bf0d7</SerialNumber>
    </Data>
    <Signature>sNLLKGMUdF0r8O1kKilWAGdgfs2BvJb/2Xp8p5iuDVfZXmhppo+d0Ran1P9TKdjV4ABwAgKXxJ3jcQTqE/2IRfqwnPf8itN8aFZlV3TJPYeD3yWE7IT55Gz6EijUpC7aKeoohTb4w2fpox58wWoF3SNp6sK6jDfiAUGEHYJ9pjU=</Signature>
</License>
```

* 添加工具类

```java
package com.example.office.utils;

import com.aspose.words.Document;
import com.aspose.words.License;
import com.aspose.words.SaveFormat;
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;

import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;

/**
 * doc转pdf工具类
 */
@Slf4j
public class AsposeUtil {
    /**
     * 加载license 用于破解 不生成水印
     */
    @SneakyThrows
    private static void getLicense() {
        try (InputStream is = AsposeUtil.class.getClassLoader().getResourceAsStream("License.xml")) {
            License license = new License();
            license.setLicense(is);
        }catch (Exception e){
            log.error("加载License文件异常", e);
        }
    }

    /**
     * word转pdf
     *
     * @param wordPath word文件保存的路径
     * @param pdfPath  转换后pdf文件保存的路径
     */
    @SneakyThrows
    public static void wordToPdf(String wordPath, String pdfPath) {
        getLicense();
        File file = new File(pdfPath);
        if (!file.exists()) {
            file.createNewFile();
        }
        try (FileOutputStream os = new FileOutputStream(file)) {
            Document doc = new Document(wordPath);
            doc.save(os, SaveFormat.PDF);
        } catch (Exception e) {
            log.error("doc转pdf异常", e);
        }
    }
}
```

* main 测试运行

```java
public static void main(String[] args) {
    Map<String, Object> dataMap = new HashMap<>();
    FtlToWordUtil.crateFile(dataMap, FtlToWordUtil.XXX_MODEL, "/data/tmp/out/xxx报告.doc");
    AsposeUtil.wordToPdf("/data/tmp/out/xxx报告.doc", "/data/tmp/out/xxx报告.pdf");
}
```

* **注意** : 部署到Linux上时会有字体问题
* https://blog.csdn.net/qq_23832313/article/details/88732721
* http://t.zoukankan.com/jimmyfan-p-14034844.html