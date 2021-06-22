## 7、错误机制的处理

### 1、默认的错误处理机制

默认错误页面

原理参照

ErrorMvcAutoConfiguration:错误处理的自动配置

```xml
org\springframework\boot\spring-boot-autoconfigure\1.5.12.RELEASE\spring-boot-autoconfigure-1.5.12.RELEASE.jar!\org\springframework\boot\autoconfigure\web\ErrorMvcAutoConfiguration.class
```

- DefaultErrorAttributes

帮我们在页面共享信息

```java
@Override
public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes,
                                              boolean includeStackTrace) {
    Map<String, Object> errorAttributes = new LinkedHashMap<String, Object>();
    errorAttributes.put("timestamp", new Date());
    addStatus(errorAttributes, requestAttributes);
    addErrorDetails(errorAttributes, requestAttributes, includeStackTrace);
    addPath(errorAttributes, requestAttributes);
    return errorAttributes;
}
```

- BasicErrorController

```java
  @Controller
  @RequestMapping("${server.error.path:${error.path:/error}}")
  public class BasicErrorController extends AbstractErrorController {
      //产生HTML数据
      @RequestMapping(produces = "text/html")
    public ModelAndView errorHtml(HttpServletRequest request,
            HttpServletResponse response) {
        HttpStatus status = getStatus(request);
        Map<String, Object> model = Collections.unmodifiableMap(getErrorAttributes(
                request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        ModelAndView modelAndView = resolveErrorView(request, response, status, model);
        return (modelAndView == null ? new ModelAndView("error", model) : modelAndView);
    }
    //产生Json数据
    @RequestMapping
    @ResponseBody
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        Map<String, Object> body = getErrorAttributes(request,
                isIncludeStackTrace(request, MediaType.ALL));
        HttpStatus status = getStatus(request);
        return new ResponseEntity<Map<String, Object>>(body, status);
    }
```

- ErrorPageCustomizer

```
  @Value("${error.path:/error}")
  private String path = "/error";//系统出现错误以后来到error请求进行处理，(web.xml)
```

- DefaultErrorViewResolver

```java
  @Override
  public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status,
        Map<String, Object> model) {
     ModelAndView modelAndView = resolve(String.valueOf(status), model);
     if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
        modelAndView = resolve(SERIES_VIEWS.get(status.series()), model);
     }
     return modelAndView;
  }

  private ModelAndView resolve(String viewName, Map<String, Object> model) {
      //默认SpringBoot可以找到一个页面？error/状态码
     String errorViewName = "error/" + viewName;
      //如果模板引擎可以解析地址，就返回模板引擎解析
     TemplateAvailabilityProvider provider = this.templateAvailabilityProviders
           .getProvider(errorViewName, this.applicationContext);
     if (provider != null) {
         //有模板引擎就返回到errorViewName指定的视图地址
        return new ModelAndView(errorViewName, model);
     }
      //自己的文件 就在静态文件夹下找静态文件 /静态资源文件夹/404.html
     return resolveResource(errorViewName, model);
  }
```

一旦系统出现4xx或者5xx错误 ErrorPageCustomizer就回来定制错误的响应规则,就会来到 /error请求,BasicErrorController处理，就是一个Controller

1.响应页面,去哪个页面是由 DefaultErrorViewResolver 拿到所有的错误视图

```java
protected ModelAndView resolveErrorView(HttpServletRequest request,
      HttpServletResponse response, HttpStatus status, Map<String, Object> model) {
   for (ErrorViewResolver resolver : this.errorViewResolvers) {
      ModelAndView modelAndView = resolver.resolveErrorView(request, status, model);
      if (modelAndView != null) {
         return modelAndView;
      }
   }
   return null;
}
```

l浏览器发送请求 accpt:text/html

客户端请求：accept:/*

### 2、如何定制错误响应

```java
1）、如何定制错误的页面

    1.有模板引擎：静态资源/404.html,什么错误什么页面；所有以4开头的 4xx.html 5开头的5xx.html

    有精确的404和4xx优先选择404

    页面获得的数据

        timestamp：时间戳

        status：状态码

        error：错误提示

        exception：异常对象

        message：异常信息

        errors:JSR303有关

    2.没有放在模板引擎，放在静态文件夹，也可以显示，就是没法使用模板取值

    3.没有放模板引擎，没放静态，会显示默认的错误

2）、如何定义错误的数据
```

举例子：新建4xx和5xx文件

```xml
<body >
    <p>status: [[${status}]]</p>
    <p>timestamp: [[${timestamp}]]</p>
    <p>error: [[${error}]]</p>
    <p>message: [[${message}]]</p>
    <p>exception: [[${exception}]]</p>
</body>
```

### 3、如何定制Json数据

#### 1、仅发送json数据

```java
public class UserNotExitsException extends  RuntimeException {
    public UserNotExitsException(){
        super("用户不存在");
    }
}
/**
 * 异常处理器
 */
@ControllerAdvice
public class MyExceptionHandler {

    @ResponseBody
    @ExceptionHandler(UserNotExitsException.class)
    public Map<String ,Object> handlerException(Exception e){
        Map<String ,Object> map =new HashMap<>();
        map.put("code", "user not exist");
        map.put("message", e.getMessage());
        return map;
    }
}
```

无法自适应 都是返回的json数据

#### 2、转发到error自适应处理

```java
@ExceptionHandler(UserNotExitsException.class)
public String handlerException(Exception e, HttpServletRequest request){
    Map<String ,Object> map =new HashMap<>();
    //传入自己的状态码
    request.setAttribute("javax.servlet.error.status_code", 432);
    map.put("code", "user not exist");
    map.put("message", e.getMessage());
    //转发到error
    return "forward:/error";
}
```

程序默认获取状态码

```java
protected HttpStatus getStatus(HttpServletRequest request) {
   Integer statusCode = (Integer) request
         .getAttribute("javax.servlet.error.status_code");
   if (statusCode == null) {
      return HttpStatus.INTERNAL_SERVER_ERROR;
   }
   try {
      return HttpStatus.valueOf(statusCode);
   }
   catch (Exception ex) {
      return HttpStatus.INTERNAL_SERVER_ERROR;
   }
```

没有自己写的自定义异常数据

#### 3、自适应和定制数据传入

Spring 默认的原理，出现错误后回来到error请求，会被BasicErrorController处理,响应出去的数据是由BasicErrorController的父类AbstractErrorController(ErrorController)规定的方法getAttributes得到的；

1、编写一个ErrorController的实现类【或者AbstractErrorController的子类】，放在容器中；

2、页面上能用的数据，或者是json数据返回能用的数据都是通过errorAttributes.getErrorAttributes得到；

容器中的DefaultErrorAtrributes.getErrorAtrributees();默认进行数据处理

```java
public class MyErrorAttributes extends DefaultErrorAttributes {
    @Override
    public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
        Map<String, Object> map = super.getErrorAttributes(requestAttributes, includeStackTrace);
        map.put("company", "wdjr");
        return map;
    }
}
```

异常处理：把map方法请求域中

```java
    @ExceptionHandler(UserNotExitsException.class)
    public String handlerException(Exception e, HttpServletRequest request){
        Map<String ,Object> map =new HashMap<>();
        //传入自己的状态码
        request.setAttribute("javax.servlet.error.status_code", 432);
        map.put("code", "user not exist");
        map.put("message", e.getMessage());
        request.setAttribute("ext", map);
        //转发到error
        return "forward:/error";
    }
}
```

在上面的MyErrorAttributes类中加上

```java
//我们的异常处理器
Map<String,Object> ext = (Map<String, Object>) requestAttributes.getAttribute("ext", 0);
map.put("ext", ext);
```

----

> ### 自定义异常处理类

```java
@ControllerAdvice
public class ExceptionHeader {

	@ExceptionHandler(Exception.class)
	public String exections () {
		return "/exception/exception";
	}

	@ExceptionHandler(RuntimeException.class)
	public String runtimeException () {
		return "/exception/runtimeException";
	}

}
```

> 在类上添加: `@ControllerAdvice` 注解

> 设置异常请求响应: `@ExceptionHandler` 具体捕获那些异常