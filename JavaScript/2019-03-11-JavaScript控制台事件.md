> ## JavaScript 控制台事件

* 在我们使用浏览器访问网页内容时我们会发现在浏览器的控制台中可以任意修改网页内容
* 治标不治本的方法有：
  * F12 按键事件
  * 鼠标右击事件修改
  * 网页滚动条事件
  * 等等。。。
* 今天带来一个浏览器 控制台事件 只要控制台被打开就会调用该事件

> ### Object.defineProperty

```javascript
var x = document.createElement('div');
Object.defineProperty(x, 'id', {
    get: function () {
        console.log('来了老弟!');
    }
});
console.log(x);
```

> 更详细的 JS 反调用技术可以跳转到：https://eller.tech/post/29