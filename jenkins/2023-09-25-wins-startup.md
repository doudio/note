> wins自启动无窗口java应用程序方案

> 打开wins启动目录

```shell
shell:startup
```

> 创建vbs脚本

```shell
set ws=WScript.CreateObject("WScript.Shell")
ws.Run "C:\project\demo\startup.bat",0
```

* https://www.cnblogs.com/youxin/p/3854703.html

> 语法

```
WshShell.Run (strCommand, [intWindowStyle], [blnWaitOnReturn])
参数
strCommand
    在 strCommand 参数内部的环境变量被自动扩展。
intWindowStyle
    这是为新进程在 STARTUPINFO 结构内设置的 wShowWindow 元素的值。其意义与 ShowWindow 中的 nCmdShow 参数相同，可取以下值之一。名称 值 含义
    SW_HIDE
    0 隐藏窗口并激活另一窗口。
    SW_MINIMIZE
    6 最小化指定窗口并激活按 Z 序排序的下一个顶层窗口。
    SW_RESTORE
    9 激活并显示窗口。若窗口是最小化或最大化，则恢复到原来的大小和位置。在还原应用程序的最小化窗口时，应指定该标志。
    SW_SHOW
    5 以当前大小和位置激活并显示窗口。
    SW_SHOWMAXIMIZED
    3 激活窗口并以最大化显示该窗口。
    SW_SHOWMINIMIZED
    2 激活窗口并以最小化显示该窗口。
    SW_SHOWMINNOACTIVE
    7 最小化显示窗口。活动窗口保持活动。
    SW_SHOWNA
    8 以当前状态显示窗口。活动窗口保持活动。
    SW_SHOWNOACTIVATE
    4 按窗口最近的大小和位置显示。活动窗口保持活动。
    SW_SHOWNORMAL
    1 激活并显示一个窗口。若窗口是最小化或最大化，则恢复到其原来的大小和位置。
blnWaitOnReturn
    如果未指定 blnWaitOnReturn 或其值为 FALSE，则该方法立即返回到脚本继续执行而不等待进程结束。
    若 blnWaitOnReturn 设为 TRUE，则 Run 方法返回由应用程序返回的任何错误代码。如果未指定 blnWaitOnReturn 或其值为 FALSE，则 Run 返回错误代码 0（zero）。

Set WshShell=Wscript.CreateObject("Wscript.Shell")
WshShell.Run "notepad.exe"

保存为notepad.vbs文件，双击会打开notepad。
```

> 创建startup.bat脚本

```shell
cd C:\project\demo\target
java -jar C:\project\demo\target\demo-0.0.1-SNAPSHOT.jar
```

> 停止wins上的java服务

```shell
taskkill /f /t /im java.exe
```