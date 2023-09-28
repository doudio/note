> #### 不使用第三方变量交换 int 值

```java
public static void main(String[] args) {
	
    int i = 1;
    int j = 2;

    i = i + j;// i = 3 : j = 2

    j = i - j;// j = 1 : i = 3

    i = i - j;// i = 2 : j = 1

    System.out.println("i:" + i + ", j:" + j);

}
```

> 执行结果

```result
i:2 , j:1
```

---

> #### 也可以使用 `^` 运算符进行交换

```java
int i = 10;
int j = 20;

i = i ^ j;
j = i ^ j;
i = i ^ j;
```

