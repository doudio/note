> ## Csv导出工具类

> 导入csv依赖

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.7</version>
</dependency>
```

> Csv 工具类

```java
import com.alibaba.fastjson.JSON;
import lombok.Data;
import org.apache.commons.csv.CSVFormat;
import org.apache.commons.csv.CSVPrinter;
import org.apache.commons.lang3.RandomStringUtils;
import org.apache.commons.lang3.StringUtils;
import org.apache.commons.lang3.Validate;
import org.apache.commons.lang3.text.WordUtils;
import org.apache.commons.lang3.time.DateFormatUtils;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.io.UnsupportedEncodingException;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.net.URLEncoder;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.Map;

/**
 * CSV生成工具类
 */
public class CsvGenerator {

    public static void configFileDownloadHeader(String filename, HttpServletResponse response) throws UnsupportedEncodingException {
        response.setHeader("Content-Encoding", "UTF-8");
        response.setHeader("Content-Type", "text/csv");
        response.setHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode(filename, "UTF-8") + RandomStringUtils.randomAlphanumeric(6) + ".csv");
    }

    public static <T> void genCsv(List<T> content, CsvHeaders csvHeaders, String dateFormat, OutputStream os) throws IOException {
        //校验数据
        Validate.notNull(content, "内容不能为null");
        csvHeaders.valid();
        //处理时间格式
        dateFormat = StringUtils.isNotEmpty(dateFormat) ? dateFormat : "y-MM-dd HH:mm:ss";

        //组装表头
        String[] headers = new String[csvHeaders.getZhHeaders().size()];
        csvHeaders.getZhHeaders().toArray(headers);
        CSVFormat csvFormat = CSVFormat.DEFAULT.withHeader(headers);
        OutputStreamWriter outputStreamWriter = new OutputStreamWriter(os, "GBK");
        CSVPrinter printer = new CSVPrinter(outputStreamWriter, csvFormat);
        for (int i = 0; i < content.size(); i++) {

            T next = content.get(i);
            //判断是否是基础数据类型或包装类
            if (next.getClass().isPrimitive() || next instanceof String) {
                printer.print(transStr(next, dateFormat));
            } else if (next instanceof Map) {
                for (String field : csvHeaders.getFields()) {
                    Object orDefault = ((Map) next).getOrDefault(field, "--");
                    printer.print(transStr(orDefault, dateFormat));
                }
            } else {
                Class<?> aClass = next.getClass();
                for (String field : csvHeaders.getFields()) {
                    try {
                        Method method = aClass.getMethod("get" + WordUtils.capitalize(field));
                        Object invoke = method.invoke(next);
                        //把英文逗号替换成中文逗号
                        printer.print(transStr(invoke, dateFormat));
                    } catch (NoSuchMethodException e) {
                        e.printStackTrace();
                        printer.print("--");
                    } catch (IllegalAccessException e) {
                        e.printStackTrace();
                        printer.print("--");
                    } catch (InvocationTargetException e) {
                        e.printStackTrace();
                        printer.print("--");
                    }

                }
            }
            printer.println();
            printer.flush();
        }

        printer.close();
    }

    private static String transStr(Object val, String dateFormat) {
        if (val == null) {
            return "--";
        } else if (val instanceof Date) {
            return DateFormatUtils.format((Date) val, dateFormat);
        } else if (val instanceof String || val.getClass().isPrimitive()) {
            return val.toString().replace(",", "，") + "\t";
        } else if (val instanceof Iterable) {
            return StringUtils.join((Iterable) val, "，") + "\t";
        } else {
            return JSON.toJSONString(val).replace(",", "，");
        }
    }

    @Data
    public static class CsvHeaders {
        private List<String> zhHeaders = new ArrayList<>();
        private List<String> fields = new ArrayList<>();

        public CsvHeaders add(String zh, String field) {
            Validate.notEmpty(zh, "中文表头不能为空");
            Validate.notEmpty(field, "表头对应字段不能为空");
            zhHeaders.add(zh.trim());
            fields.add(field.trim());
            return this;
        }

        public void valid() {
            Validate.notEmpty(zhHeaders, "中文表头不能为空");
            Validate.notEmpty(fields, "表头对应字段不能为空");
        }
    }

}
```

> SpringWeb 环境下使用

```java
List<DataModel> voList = dataModelService.getData();
HttpServletResponse response = UserContext.getResponse();
CsvGenerator.configFileDownloadHeader("DataModelCsv文件", response);

CsvGenerator.CsvHeaders csvHeaders = new CsvGenerator.CsvHeaders();
csvHeaders.add("数据模型类型1", "column1")
    .add("数据模型类型2", "column2");

CsvGenerator.genCsv(voList, csvHeaders, null, response.getOutputStream());
```
