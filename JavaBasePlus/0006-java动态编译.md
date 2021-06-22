> ### java动态编译

> 编译一个 java 类

```java
private static void dyJavac(String path) {
    // 获取动态编译器
    JavaCompiler compiler = ToolProvider.getSystemJavaCompiler();
	
    int result = compiler.run(System.in, System.out, System.err, path);

    System.out.println(result);// result == 0 : 编译成功!
}
```

> 执行一个 class

```java
private static void dyJava2(String path) throws Exception {

    URL[] urls = new URL[] { new URL("file:/" + path) };

    URLClassLoader loader = new URLClassLoader(urls);
    Class<?> loadClass = loader.loadClass("Demo");

    Method method = loadClass.getMethod("main", String[].class);
    method.invoke(null, (Object)new String[] {});

}
```

> 执行一个 class

```java
private static void dyJava(String path) throws Exception {
    Runtime runtime = Runtime.getRuntime();

    Process exec = runtime.exec("java -cp " + path);
    InputStream inputStream = exec.getInputStream();
    // InputStreamReader : 交换流, 将字符流转为字节流
    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
    String info = "";
    while ((info = reader.readLine()) != null) {
        System.out.println(info);
    }

}
```

> ##### 调用测试

```java
public static void main(String[] args) throws Exception {
    
    dyJavac("F:/tempfile/dyJavac/Demo.java");
    dyJava("F:/tempfile/dyJavac Demo");
    dyJava2("F:/tempfile/dyJavac/");

}
```

