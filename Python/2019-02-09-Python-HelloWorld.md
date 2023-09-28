> #### Python HelloWorld

1. 安装`python` [官网地址](https://www.python.org/)
   1. 若没有配置环境变量需要添加
      1. `D:\Python\Python37-32`
      2. `D:\Python\Python37-32\Scripts`
2. 打开默认的`python`编程环境`IDLE`
3. 编写`Hello World!`

> 编写`Hello World!`

```python
Python 3.7.0 (v3.7.0:1bf9cc5093, Jun 27 2018, 04:06:47) [MSC v.1914 32 bit (Intel)] on win32
Type "copyright", "credits" or "license()" for more information.
>>> print("Hello World!")
Hello World!
```

> `python`的注解

```python
# 第一个注释
# 第二个注释
 
'''
第三注释
第四注释
'''
 
"""
第五注释
第六注释
"""

\ : 行链接符
```

> 执行后循环画圈

```python
import turtle
t = turtle.Pen()
for x in range(360):
	t.forward(x)
	t.left(59)
```

> 使用画图工具

```python
import turtle					# 导入turtle模块
turtle.showturtle()				# 显示箭头
turtle.write("Hello Python")	# 写字符串
turtle.forward(300)				# 前进 300 像素
turtle.color("red")				# 设置画笔颜色
turtle.left(90)					# 箭头左转 90 度
turtle.forward(300)
turtle.goto(0, 50)				# 去坐标 (0, 50)
turtle.goto(0, 0)
turtle.penup()					# 提笔
turtle.pendown()				# 下笔
turtle.circle(100)				# 画一个半径为 100 的圆
```

> 定义一个变量
>
>  	1. `name` = `value`
>  	2. `name1` = `name2` = `value`
>  	3. `name1, name2` = `name1Value, name2Value`
>  	4. `name1` = `name2` = `value`
>
>
>
> 删除一个变量
>
> 1. `del` `name`
> 2. `del` `name1, name2`


> 两个变量互换:

```python
>>> a, b = 1, 2
>>> a, b = b, a
>>> a
2
>>> b
1
>>> 
```

#### 同一行显示多条语句

Python可以在同一行中使用多条语句，语句之间使用分号(;)分割，以下是一个简单的实例：

```python
import sys; x = 'runoob'; sys.stdout.write(x + '\n')
```

