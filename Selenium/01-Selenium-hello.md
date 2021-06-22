> ## Selenium Hello

> 导入 selenium 依赖

```xml
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>3.14.0</version>
</dependency>
```

> 下载 chromedriver 

* https://chromedriver.storage.googleapis.com/index.html
* **我的Chrome版本 `80.0.3987.100`  注意下载版本时执行对于版本或者相似版本**

> 编写 selenium 逻辑

```java
// 加载 chromedriver 驱动
System.setProperty("webdriver.chrome.driver", "E:\\temp\\chromedriver.exe");
// 创建一个 chrome
WebDriver driver = new ChromeDriver();
// 打开莫个网站
driver.get("https://blog.csdn.net/qq_42920045");
// 获取当前页面的 title 和 当前 url
System.out.println("Title:" + driver.getTitle());
System.out.println("currentUrl:" + driver.getCurrentUrl());
// 根据 css 获取元素并点击
WebElement e = driver.findElement(By.cssSelector("#mainBox > div:nth-child(1) > a"));
e.click();
// 获取浏览器cookie
Set<Cookie> set = driver.manage().getCookies();
Iterator<Cookie> iterator = set.iterator();
while (iterator.hasNext()) {
    Cookie c = iterator.next();
    System.out.println(JSON.toJSONString(c));
}
```

