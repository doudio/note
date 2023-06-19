## WebSocket

​	WebSocket 与 Http 连接不同，Http 属于一种短连接的形式单页面打开时浏览器渲染完成连接就关闭了，而 WebSocket 会在页面打开直到页面关闭都会与服务器进行连接。

```properties
# 长连接状态
Request URL: ws://localhost:8080/WebSocket
Request Method: GET
Status Code: 101 
```



[TOC]



### SpringBoot 编写一个 Socket

#### 在 pom 文件中添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

#### 编写 webSocket 配置类

```java
@Slf4j
@Configuration
public class WebSocketConfig {
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}
```

#### 编写服务端交互

```java
@Component
@ServerEndpoint(value = "/WebSocket/{authorization}")
public class WebSocketDemo {

	@OnOpen/** 页面打开时 */
	public void onOpen(Session session, @PathParam("authorization") String authorization) {
		System.out.println(String.format("opOpen: {session: %s, authorization: %s}", session, authorization));
	}

	@OnClose/** 页面关闭时 */
	public void onClose() {
		System.out.println(String.format("onClose: {this: %s}", this));
	}

	@OnMessage/** 监听消息 */
	public void onMessage(String message, Session session) {
		System.out.println(String.format("server and html: {message: %s, session: %s}",
                                         message, session));
	}

	@OnError/** 出现异常 */
	public void onError(Session session, Throwable error) {
		System.out.println(String.format("http href onError: {session: %s, error: %s}",
                                         session, error));
	}

}
```

#### 前端页面交互

```html
<!DOCTYPE HTML>
<html>
<head>
    <title>My WebSocket</title>
</head>

<body>
	Welcome<br/>
	<input id="text" type="text" />
	<button onclick="send()">Send</button>
	<button onclick="closeWebSocket()">Close</button>
	<div id="message">
</div>
</body>

<script type="text/javascript">
    var websocket = null;

    if('WebSocket' in window){
        websocket = new WebSocket("ws://localhost:8080/WebSocketDemo");
    }
    else{
        alert('Not support websocket')
    }

    websocket.onerror = function(){
        setMessageInnerHTML("error");
    };

    websocket.onopen = function(event){
        setMessageInnerHTML("open");
    }

    websocket.onmessage = function(event){
        setMessageInnerHTML(event.data);
    }

    websocket.onclose = function(){
        setMessageInnerHTML("close");
    }

    window.onbeforeunload = function(){
        websocket.close();
    }

    function setMessageInnerHTML(innerHTML){
        document.getElementById('message').innerHTML += innerHTML + '<br/>';
    }

    function closeWebSocket(){
        websocket.close();
    }

    function send(){
        var message = document.getElementById('text').value;
        websocket.send(message);
    }
</script>
</html>
```

---

### 在Socket中获取SpringIOC容器中的bean

```java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

import java.util.Locale;
import java.util.Map;

/**
 * 获取对象bean
 */
@Component
public class SpringContextUtil implements ApplicationContextAware {
    private static ApplicationContext applicationContext = null;
    @Override
    public void setApplicationContext(ApplicationContext context) throws BeansException {
        applicationContext = context;
    }
    /**
     * 获取applicationContext对象
     */
    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }
    /**
     * 根据bean的id来查找对象
     */
    public static <T> T getBeanById(String id) {
        return (T) applicationContext.getBean(id);
    }
    /**
     * 根据bean的class来查找对象
     */
    public static <T> T getBeanByClass(Class c) {
        return (T) applicationContext.getBean(c);
    }
    /**
     * 根据bean的class来查找所有的对象(包括子类)
     */
    public static Map getBeansByClass(Class c) {
        return applicationContext.getBeansOfType(c);
    }
    public static String getMessage(String key) {
        return applicationContext.getMessage(key, null, Locale.getDefault());
    }
}
```

---

> ## SpringbootWebsocket 不能注入Bean

*  Spring 或 Springboot 的 websocket 里面使用 @Autowired 注入 service 或 bean 时, 报空指针异常, service 为 null(并不是不能被注入)

* 解决方法：将要注入的 service 改成 static，就不会为null了

```java
@Controller
@ServerEndpoint(value="/chatSocket")
public class ChatSocket {
    //  这里使用静态，让 service 属于类
    private static ChatService chatService;

    // 注入的时候，给类的 service 注入
    @Autowired
    public void setChatService(ChatService chatService) {
        ChatSocket.chatService = chatService;
    }
}
```

* 本质原因:
* spring管理的都是单例和websocket多例相冲突。
* 详细解释: 
  * 项目启动时初始化，会初始化 websocket （非用户连接的），spring 同时会为其注入 service，该对象的 service 不是 null，被成功注入。但是，由于 spring 默认管理的是单例，所以只会注入一次 service。当新用户进入聊天时，系统又会创建一个新的 websocket 对象，这时矛盾出现了：spring 管理的都是单例，不会给第二个 websocket 对象注入 service，所以导致只要是用户连接创建的 websocket 对象，都不能再注入了
  * 像 controller 里面有 service， service 里面有 dao。因为 controller，service ，dao 都有是单例，所以注入时不会报 null。但是 websocket 不是单例，所以使用spring注入一次后，后面的对象就不会再注入了，会报null。

---

### Nginx配置WebSocket

* 根据是否有SSL证书就使用: `wss`

```json
location / {
    proxy_pass   http://127.0.0.1:8080/; # 代理转发地址
    proxy_read_timeout   3600s; # 超时设置
    # 启用支持websocket连接
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

