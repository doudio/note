## IO 操作一行文件

> ### 读取文件

```java
try(BufferedReader input = new BufferedReader(new InputStreamReader(new FileInputStream(new File("F:/file.txt"))));){
    StringBuilder builder = new StringBuilder();
    String line = null;
    while((line = input.readLine()) != null) {
        builder.append(line);
        builder.append(String.format("%n"));
    }
    System.out.println(builder.toString());
} catch (IOException e) {
    e.printStackTrace();
}
```

> ### 写出文件

```java
try(BufferedWriter output = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(new File("F:/OutPut"))));){
    List<String> list = new ArrayList<String>();
    for (String context : list) {
        output.write(String.format("%s%n", context));
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
}
```

> ### utils class

```java
import org.springframework.util.ObjectUtils;
import java.io.*;
import java.util.List;

public class IOUtils {

    // 读
    public static void read(File file) {
        try (BufferedReader input = new BufferedReader(new InputStreamReader(new FileInputStream(file)));) {
            StringBuilder builder = new StringBuilder();
            String line = null;
            while ((line = input.readLine()) != null) {
                builder.append(line);
                builder.append(String.format("%n"));
            }
            System.out.println(builder.toString());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // 写
    public static void write(File file, List<String> list) {
        if (ObjectUtils.isEmpty(list)) return;
        try (BufferedWriter output = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(file)));) {
            for (String context : list) {
                output.write(String.format("%s%n", context));
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```