> ## alibaba easyexcel 工具的使用

> 导入 easyexcel 依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>2.1.2</version>
</dependency>
```

> 读取 Excel 文件 model

![](img/excelModel.png)

> 创建实体类 Student

```java
@Data
public class Student {

    private String name;
    private Integer age;
    private String gender;
    private String remark;

}
```

> 创建 Student 的 ReadListener

```java
@Data
@Slf4j
public class StudentReadListenerImpl extends AnalysisEventListener<Student> {

    /**
     * data list
     */
    private List<Student> list = new ArrayList<Student>();

    /**
     * 这个每一条数据解析都会来调用
     * @param data
     * @param context
     */
    @Override
    public void invoke(Student data, AnalysisContext context) {
        list.add(data);
    }

    /**
     * 所有数据解析完成了 都会来调用
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        log.info("reload success !!!");
    }

}
```

> 执行 Test

```java
@Test
public void reloadExcel() {
    String fileName = "D:/stu.xlsx";
    StudentReadListenerImpl studentReadListener = new StudentReadListenerImpl();
    EasyExcel.read(fileName, Student.class, studentReadListener).sheet().doRead();
    studentReadListener.getList().forEach(System.out::println);
}
```

> 写出文件

```java
@Data
public class Stu {

    @ExcelProperty(value = "姓名", index = 0)
    private String name;
    @ExcelProperty(value = "年龄", index = 1)
    private Integer age;
    @ExcelProperty(value = "性别", index = 2)
    private String gender;
    @ExcelProperty(value = "备注", index = 3)
    private String remark;

    // 忽略这个字段
    @ExcelIgnore
    private String ignore;

}
```

> 执行 Test

```java
@Test
public void writeExcel() {
    List<Stu> stus = new ArrayList<>(3);
    stus.add(new Stu("uname1", 18, "男", "re1"));
    stus.add(new Stu("uname2", 19, "女", "re2"));
    stus.add(new Stu("uname3", 20, "男", "re3"));

    EasyExcel.write("D:/qwl_temp/data/student.xlsx", Stu.class).sheet("Student").doWrite(stus);
}
```

> 写出的文件格式

![](img/excelOutputModel.png)


> alibaba 的官方文档中还有很多复杂的操作如果业务过于复杂可以去了解一下
* https://alibaba-easyexcel.github.io/
* https://github.com/alibaba/easyexcel.git