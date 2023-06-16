> 安装 tmux 指令

```shell
yum install tmux -y
```

> 使用

```shell
# 创建窗口
tmux

# 竖向分屏
`ctrl + b` + %

# 横向分屏
`ctrl + b` + "

# 上下左右切换屏
`ctrl + b` + `上下左右 箭头`

# 放大缩小屏幕
`ctrl + b` + `z`

# 关闭当前窗口
`ctrl + b` + `x`

# 退出tmux
`ctrl + b` + `d`

# 查看现在有窗口列表
tmux ls

# 进入窗口
tmux a -t 0
```