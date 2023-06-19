* ## 如果项目需求较为简单可使用 `easyexcel`

  * https://alibaba-easyexcel.github.io/
  * https://alibaba-easyexcel.github.io/docs/current/

> 使用起来简单

> 导入pom

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>3.1.1</version>
</dependency>
```



> demo代码

```java
/**读Excel
 * DEMO代码地址：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/read/ReadTest.java
 * 最简单的读
 * <p>1. 创建excel对应的实体对象 参照{@link DemoData}
 * <p>2. 由于默认一行行的读取excel，所以需要创建excel一行一行的回调监听器，参照{@link DemoDataListener}
 * <p>3. 直接读即可
 */
@Test
public void simpleRead() {
    String fileName = TestFileUtil.getPath() + "demo" + File.separator + "demo.xlsx";
    // 这里 需要指定读用哪个class去读，然后读取第一个sheet 文件流会自动关闭
    EasyExcel.read(fileName, DemoData.class, new DemoDataListener()).sheet().doRead();
}


/**写Excel
 * DEMO代码地址：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/write/WriteTest.java
 * 最简单的写
 * <p>1. 创建excel对应的实体对象 参照{@link com.alibaba.easyexcel.test.demo.write.DemoData}
 * <p>2. 直接写即可
 */
@Test
public void simpleWrite() {
    String fileName = TestFileUtil.getPath() + "write" + System.currentTimeMillis() + ".xlsx";
    // 这里 需要指定写用哪个class去读，然后写到第一个sheet，名字为模板 然后文件流会自动关闭
    // 如果这里想使用03 则 传入excelType参数即可
    EasyExcel.write(fileName, DemoData.class).sheet("模板").doWrite(data());
}

/**web上传、下载
 * DEMO代码地址：https://github.com/alibaba/easyexcel/blob/master/easyexcel-test/src/test/java/com/alibaba/easyexcel/test/demo/web/WebTest.java
 * 文件下载（失败了会返回一个有部分数据的Excel）
 * <p>
 * 1. 创建excel对应的实体对象 参照{@link DownloadData}
 * <p>
 * 2. 设置返回的 参数
 * <p>
 * 3. 直接写，这里注意，finish的时候会自动关闭OutputStream,当然你外面再关闭流问题不大
 */
@GetMapping("download")
public void download(HttpServletResponse response) throws IOException {
    // 这里注意 有同学反应使用swagger 会导致各种问题，请直接用浏览器或者用postman
    response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
    response.setCharacterEncoding("utf-8");
    // 这里URLEncoder.encode可以防止中文乱码 当然和easyexcel没有关系
    String fileName = URLEncoder.encode("测试", "UTF-8").replaceAll("\\+", "%20");
    response.setHeader("Content-disposition", "attachment;filename*=utf-8''" + fileName + ".xlsx");
    EasyExcel.write(response.getOutputStream(), DownloadData.class).sheet("模板").doWrite(data());
}

/**web上传、下载
 * 文件上传
 * <p>1. 创建excel对应的实体对象 参照{@link UploadData}
 * <p>2. 由于默认一行行的读取excel，所以需要创建excel一行一行的回调监听器，参照{@link UploadDataListener}
 * <p>3. 直接读即可
 */
@PostMapping("upload")
@ResponseBody
public String upload(MultipartFile file) throws IOException {
    EasyExcel.read(file.getInputStream(), UploadData.class, new UploadDataListener(uploadDAO)).sheet().doRead();
    return "success";
}
```

## 较为复杂的导出可通过创建模板的方式实现

> 导入pom

```xml
<!-- https://mvnrepository.com/artifact/org.apache.poi/poi -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.17</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.poi/poi-ooxml -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>3.17</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.poi/poi-ooxml-schemas -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml-schemas</artifactId>
    <version>3.17</version>
</dependency>

<!-- https://www.hutool.cn 工具包 -->
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.8.0</version>
</dependency>
```

> 项目 resource 目录下创建出模板

* 这个模板生成流程: 通过wins创建xlsx文件 > 编辑好列头与合并单元格 > 保存到项目 resource 目录下

```shell
├─excel-demo
│  └─src
│      └─main
│          ├─java
│          │  └─com
│          │      └─xxx
│          │        └─XxxApplication.java
│          └─resources
│              ├─mapper
│              └─templates
│                  └─excel
│                    └─demo.xlsx
│  └─pom.xml
```

> java 代码导出

```java
File file = new File("/data/export/", one.getFileName());
try (
    OutputStream out = new FileOutputStream(file);
    InputStream modelInput = ResourceUtil.getStream("templates/excel/demo.xlsx");
    XSSFWorkbook workbook = new XSSFWorkbook(modelInput);
) {
    Sheet sheet = workbook.getSheetAt(0);
    // 查询出数据
    List<DemoXlsx> sheetData = list();
    
    for (int i = 0; i < sheetData.size(); i++) {
        DemoXlsx itm = sheetData.get(i);
        // 这里加多少是根据模板来的
        int rowCount = i + 2;
        Row row = sheet.createRow(rowCount);
        row.createCell(0).setCellValue(i+1);// 序号
        row.createCell(1).setCellValue(itm.getXxxId());// XxxId
        row.createCell(2).setCellValue(itm.getName());// 姓名
    }
    
    workbook.write(out);
} catch (Exception e) {
    log.error(e.getMessage(), e);
}
```

