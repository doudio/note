> #### git eclipse

> 工程初始化为本地仓库: 
>
> ​	`选中工程` `>` `右击` `>` `Team` `>` `Share Project` `>` `Git`
>
> ​	`点击:Use or create repository in parent folder of project`  `>` `选中工程:点击 Create Repository` `>` `finish`

> 了解 eclipse 特定文件

1. `.classpath`
2. `.project`
3. `settings`

> 忽略 eclipse 特定文件

> [打开](https://github.com/github/gitignore/blob/master/Java.gitignore): https://github.com/github/gitignore/blob/master/`Java.gitignore`

```cmd
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*

.classpath
.project
.settings
target
```

> 在该目录下创建: C:\Users\Administrator\ `Java.gitignore`

> 在该文件中添加特定配置: C:\Users\Administrator\ `.gitconfig`

```cmd
[core]
	excludesfile = C:/Users/Administrator/Java.gitignore
```

