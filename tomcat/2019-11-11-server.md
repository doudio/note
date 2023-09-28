> ### server.xml

> 配置本地虚拟路径

1. 找到 Engine 节点
2. 在 Engine 节点内进行配置
3. 在页面使用

> 在 Engine 节点内进行配置

```xml
<Engine>

    <!-- 配置虚拟路径: docBase=本地磁盘路径,  path=虚拟路径名, reloadable=?  -->
	<Context docBase="F:/tempfile" path="/path" reloadable="true"/>

</Engine>
```

> 在页面使用

```html
<img alt="使用tomcat虚拟路径" src="/path/demo.jpg" />
```

