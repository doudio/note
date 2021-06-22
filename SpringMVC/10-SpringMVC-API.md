```java
((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest()
```

> 能在同一线程中获取到：Request 对象，其底层使用 ThreadLocal来保存的。

```java
@RestControllerAdvice

@ExceptionHandler
```

> 可以作系统全局异常处理。