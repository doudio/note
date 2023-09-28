> #### `CAS` 算法

```java
int i = 0;
i = i++;
System.out.println(i);
```

```java
运行结果:
	0 
因为 i++ 的操作在底层主要分为:
	int temp = i;/*定义一个临时变量*/
	i = i + 1;/*进行运算*/
	i = temp;/*将临时变量赋值给i*/
```

---

```java
public static void main(String[] args) {
    CASDemo casDemo = new CASDemo();

    for (int j = 0; j < 10; j++) {
        new Thread(casDemo).start();
    }
}
```

> 对 i 进行 ++

```java
class CASDemo implements Runnable {

	private Integer i = 0;
	
	public void run() {
		
		try {
			Thread.sleep(500);
		} catch (InterruptedException e) {
		}
		
		System.out.println(auto());
	}
	
	public Integer auto() {
		return i++;
	}
	
}
```

```java
运行结果:
	0, 1, 0, 0, 0, 2, 4, 3, 5, 6
```

> 存在多线程安全问题

>  解决方式一: 使用原子变量: JDK1.5 后 `java.util.concurrent.atomic` 包下提供了常用的原子变量
>
> 其中使用:
>
> ​	volatile : 保证内存可见性
>
> ​	CAS ( Compare-And-Swap ) : 算法保证数据的原子性
>
> ​		CAS 算法是硬件对于并发操作共享数据的支持
>
> ​		CAS 包含了三个操作数:
>
> ​			内存值: V
>
> ​			预估值: A
>
> ​			运算值: B
>
> ​			当 V 的值 == A 时, 才进行 B 的赋值

```java
private AtomicInteger i = new AtomicInteger();
return i.getAndIncrement();
```

