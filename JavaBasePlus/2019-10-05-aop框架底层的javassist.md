> aop 框架底层的 javassist API

1. javassist 可以实现 java 的动态性
2. 比如在 java 程序运行时, 动态的添加新方法修改类结构
3. 该类 API 与 `java.lang.Class` API 相似



> 动态的创建一个 class 对象

```java
ClassPool pool = ClassPool.getDefault();

/** 声明类名及包名 */
CtClass ctClass = pool.makeClass("com.znsd.javassist.Emp");

/** 创建属性 */
CtField ctField = CtField.make("private Integer id;", ctClass);
ctClass.addField(ctField);

/** 创建方法 */
CtMethod ctMethod = CtMethod.make("public Integer getId(){return id;}", ctClass);
ctClass.addMethod(ctMethod);

// 将生成好的类输出在本地磁盘上
ctClass.writeFile("F:/");
System.out.println("执行成功!");
```

> 获取类的基本信息

```java
ClassPool classPool = ClassPool.getDefault();
		
CtClass ctClass = classPool.get("com.znsd.javassist.Student");

System.out.println("获取类路径: " + ctClass.getName());
System.out.println("获取类名: " + ctClass.getSimpleName());
System.out.println("获取父类: " + ctClass.getSuperclass());
System.out.println("获取接口: " + ctClass.getInterfaces());
```

> 动态创建方法

```java
ClassPool classPool = ClassPool.getDefault();

CtClass ctClass = classPool.get("com.znsd.javassist.Student");

/** 声明好一个方法 */
CtMethod ctMethod = 
    CtNewMethod.make("public int add(int i, int j){return i + j;}", ctClass);

ctClass.addMethod(ctMethod);

/** 获取 Class 对象用于调用创建好的方法 */
Class classes = ctClass.toClass();

Object newInstance = classes.newInstance();
Method declaredMethod = classes.getDeclaredMethod("add", int.class, int.class);
Object result = declaredMethod.invoke(newInstance, 110, 120);

System.out.println(result);
```

> 实现 aop 编程

```java
ClassPool classPool = ClassPool.getDefault();
CtClass ctClass = classPool.get("com.znsd.javassist.Student");

CtMethod ctMethod = ctClass.getDeclaredMethod("show");

// 在方法前插入一些代码
ctMethod.insertBefore("System.out.println(\"aop:before\");");
// 在方法后插入一些代码
ctMethod.insertAfter("System.out.println(\"aop:after\");");
// 指定在哪一行插入一些代码
ctMethod.insertAt(29, "System.out.println(\"insertAt\");");

Class classes = ctClass.toClass();
Object newInstance = classes.newInstance();
Method method = classes.getDeclaredMethod("show");
method.invoke(newInstance);
```

