> ### markdown-开发

1. 解压 `editor.md-master.zip` 
2. 导入 web 项目
3. 编辑 html 页面
4. 编写 servlet 获取值

> 解压 `editor.md-master.zip`  AND 导入 web 项目

---

> editor.md-master.zip: [下载链接](http://pandao.github.io/editor.md/)

---

> 编写 html 页面使用

```html
<!DOCTYPE html>
<html lang="zh">
    <head>
        <meta charset="utf-8" />
        <title>markdown</title>
        <link rel="stylesheet" href="editor/examples/css/style.css" />
        <link rel="stylesheet" href="editor/css/editormd.css" />
        <%-- 需要导入两个 CSS --%>
    </head>
    <body>
        
        <div id="test-editormd">
            <textarea style="display:none;">
            	<%-- 页面一开始显示的内容 --%>
            </textarea>
        </div>
        
       	<button onclick="save()" class="btn">save</button>
       	
        <%-- 导入jquery --%>
        <script src="editor/examples/js/jquery.min.js"></script>
        <%-- 导入 editormd 语法 --%>
		<script src="editor/editormd.min.js"></script>
        
        <script type="text/javascript">
			var editor;

            /** 初始化编辑器:
            		1. 需要注意的 path 目录指定的是 editor 中的 lib/
            */
            $(function() {
                editor = editormd("test-editormd", {
                    width   : "90%",
                    height  : 640,
                    syncScrolling : "single",
                    path    : "editor/lib/",
                    saveHTMLToTextarea : true
                });
            });
            
            /** 获取编辑器的内容 */
            function save() {
            	var markdown = editor.getMarkdown();
            	var HTML = editor.getHTML();                
                
            	console.log(markdown);
            	console.log(HTML);
            	
            	$.ajax({
            		url:"markdownDemo",
            		type:"post",
            		data:{
            			"markdown":markdown,
            			"HTML":HTML
            		},
            		success:function(data) {
            			alert(data);
            		}
            	});
            	
            }
            
        </script>
    </body>
</html>
```

> 编写 servlet 获取值

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) 
    throws ServletException, IOException {
		
		String markdown = request.getParameter("markdown");
		String HTML = request.getParameter("HTML");
		
		System.out.println(markdown);
		System.out.println(HTML);
		
		response.setCharacterEncoding("UTF-8");
		response.getWriter().write("操作成功!");
		
}
```

> 初始化编辑器的值介绍

| 值                 | 描述                  |
| ------------------ | --------------------- |
| width              | 编辑器的宽            |
| height             | 编辑器的高            |
| syncScrolling      | 同步滚动              |
| path               | editor 中的 lib/ 目录 |
| saveHTMLToTextarea | 是否保存 HTML 数据    |

> 页面渲染 markdown 笔记

```html
<!DOCTYPE html>
<html lang="zh">
    <head>
        <meta charset="utf-8" />
        <title>HTML Preview(markdown to html) - Editor.md examples</title>
        <link rel="stylesheet" href="css/style.css" />
        <link rel="stylesheet" href="editor-md/css/editormd.preview.css" />
        <link rel="shortcut icon" href="https://pandao.github.io/editor.md/favicon.ico" type="image/x-icon" />
        <style>            
            .editormd-html-preview {
                width: 90%;
                margin: 0 auto;
            }
        </style>
    </head>
    <body>
    	<span id="id" class="id" ></span>
        <div id="layout">
            <div id="test-editormd-view2">
                <textarea id="append-test" style="display:none;"></textarea>          
            </div>
        </div>
        <script src="js/jquery-2.0.3.min.js"></script>
        <script src="editor-md/lib/marked.min.js"></script>
        <script src="editor-md/lib/prettify.min.js"></script>
        
        <script src="editor-md/lib/raphael.min.js"></script>
        <script src="editor-md/lib/underscore.min.js"></script>
        <script src="editor-md/lib/sequence-diagram.min.js"></script>
        <script src="editor-md/lib/flowchart.min.js"></script>
        <script src="editor-md/lib/jquery.flowchart.min.js"></script>

        <script src="editor-md/editormd.js"></script>
        <script type="text/javascript">
            $(function() {
            	
            	/**
	            	1. 获取到数据 editor-md 源数据
	            	2. 将返回的源数据添加到容器中[id:append-test]
	            	3. 接着下面渲染视图
	            	注意: ajax 可能需要同步请求
	            */
	            $.ajax({
	            	type: "get", 
		            url: "markdown", 
		            data: {
		            	"id": ${动态获取的数据库ID}
		            }, cache:false, async:false, 
		            success: function(result){
		            	console.log(result);
		            	$("#append-test").empty().append(result.putContext);
		            } 
		        });
            	
                var testEditormdView, testEditormdView2;
                
                $.get("editor-md/examples/test.md", function(markdown) {
                    
				    testEditormdView = editormd.markdownToHTML("test-editormd-view", {
                        markdown        : markdown ,//+ "\r\n" + $("#append-test").text(),
                        //htmlDecode      : true,       // 开启 HTML 标签解析，为了安全性，默认不开启
                        htmlDecode      : "style,script,iframe",  // you can filter tags decode
                        //toc             : false,
                        tocm            : true,    // Using [TOCM]
                        //tocContainer    : "#custom-toc-container", // 自定义 ToC 容器层
                        //gfm             : false,
                        //tocDropdown     : true,
                        // markdownSourceCode : true, // 是否保留 Markdown 源码，即是否删除保存源码的 Textarea 标签
                        emoji           : true,
                        taskList        : true,
                        tex             : true,  // 默认不解析
                        flowChart       : true,  // 默认不解析
                        sequenceDiagram : true,  // 默认不解析
                    });
                    
                    //console.log("返回一个 jQuery 实例 =>", testEditormdView);
                    
                    // 获取Markdown源码
                    //console.log(testEditormdView.getMarkdown());
                    
                    //alert(testEditormdView.getMarkdown());
                });
                    
                testEditormdView2 = editormd.markdownToHTML("test-editormd-view2", {
                    htmlDecode      : "style,script,iframe",  // you can filter tags decode
                    emoji           : true,
                    taskList        : true,
                    tex             : true,  // 默认不解析
                    flowChart       : true,  // 默认不解析
                    sequenceDiagram : true,  // 默认不解析
                });
                
                
            });
        </script>
    </body>
</html>
```

