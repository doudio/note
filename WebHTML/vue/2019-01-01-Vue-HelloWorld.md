> #### Vue HelloWorld

获取`Vue.js`

> 点击获取 [Vue.js](https://vuejs.org/js/vue.min.js) 

> 国内CDN: <https://cdn.staticfile.org/vue/2.2.2/vue.min.js>

---

> Hello World : 声明式渲染

```html
<script src="js/vue.min.js" type="text/javascript" charset="utf-8"></script>
		
<div id="app">
    <ul>
        <li>{{ msg }}</li>
        <li>{{ demo() }}</li>
        <li v-html="vHtml"></li>
        <li v-text="vHtml"></li>
    </ul>
</div>

<script type="text/javascript">
   var vue = new Vue({
        el: '#app',
        data: {
            msg: 'Hello Vue.js',
            vHtml: "<b>Vue.js</b>"
        }, methods: {
            demo: function() {
                return this.msg + " Demo()";
            }
        }
    });
</script>
```

> el : 选择一个 DOM 对象
>
> data : 定义数据
>
> methods : 定义方法

> vue : 条件与循环

```html
<div id="app">
    <ul>
        <li v-if="mark">mark value eq true</li>
        <li v-for="i in ary">{{ i.name }}</li>
    </ul>
</div>

<script type="text/javascript">
    var vue = new Vue({
        el: '#app',
        data: {
            mark: true,
            ary: [
                {id: 1, name: '张三', gender: '男'},
                {id: 2, name: '李四', gender: '女'},
                {id: 3, name: '王五', gender: '男'}
            ]
        }
    });
    vue.ary.push({id: 4, name: 'new Object', gender: '男'});
</script>
```

> vue : 处理用户输入

```html
<div id="app-2">
    <ul>
        <li>{{info}}</li>
        <li><input v-model="info" /></li>
        <li><button v-on:click="resolveInfo">resolveInfo</button></li>
    </ul>
</div>

<script type="text/javascript">
    var vue = new Vue({
        el: '#app-2',
        data: {
            info: 'Hello Vue.js'
        }, methods: {
            resolveInfo: function() {
                this.info = this.info.split('').reverse().join('');
            }
        }
    });
</script>
```

> vue : 组件化应用构建

```html
<ul id="app">
    <vue-component></vue-component>
</ul>		

<script type="text/javascript">
    Vue.component('vue-component', {
        template: '<li>new vue component</li>'
    });
    var vue = new Vue({
        el: '#app'
    });
</script>
```

> 注意 定义组件要在: new Vue() 对象之前注册

```html
<ul id="app">
    <vue-component msg='Hello Vue Component'></vue-component>
</ul>		

<script type="text/javascript">
    Vue.component('vue-component', {
        props: ['msg'],
        template: '<li>{{msg}}</li>'
    });
    var vue = new Vue({
        el: '#app'
    });
</script>
```

> props : 起到了一个定义变量的作用

### `v-bind` 缩写

```h&#39;t
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>
```

### `v-on` 缩写

```html
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>
```