> ## ExcelAPI-Hello

| Object       | Desc                         |
| ------------ | ---------------------------- |
| Workbook     | 表格对象                     |
| HSSFWorkbook | excel2003 表格对象实现`xls`  |
| XSSFWorkbook | excel2007 表格对象实现`xlsx` |
| Sheet        | 表格中的页                   |
| Row          | 一行数据                     |
| Cell         | 一个单元格                   |

> 注意由于 excel 文件有两个格式，在读取数据时需要根据不同版本创建相应对象

> 在读取一列数据中的值时出现类型问题可以通过：

```java
cell.setCellType(HSSFCell.CELL_TYPE_STRING); 来设置指定的类型
```

> 合并单元格

```java
var cra = new CellRangeAddress(int firstRow, int lastRow, int firstCol, int lastCol);
sheet.addMergedRegion(cra);

/**
int firstRow 从哪一行 到 int lastRow 最后一行
int firstCol 从哪一列 到 int lastCol 最后一列
*/
```

---

> `水平剧中`

```java
CellStyle style = workbook.createCellStyle();
style.setAlignment(HSSFCellStyle.ALIGN_CENTER);
style.setVerticalAlignment(HSSFCellStyle.VERTICAL_CENTER);
```

> `背景红字体白`

```java
CellStyle style = workbook.createCellStyle();
style.setFillForegroundColor(HSSFColor.DARK_RED.index);
style.setFillPattern(HSSFCellStyle.SOLID_FOREGROUND);

Font font = workbook.createFont();
font.setColor(HSSFColor.WHITE.index);
style.setFont(font);
```

