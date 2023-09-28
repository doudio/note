



# Git

​	Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

​	Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

​	Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持

##### Git 与 SVN 区别

* GIT是分布式的
* GIT把内容按元数据方式存储，而SVN是按文件;SVN把文件的元信息隐藏在一个类似.svn,.cvs等的文件夹里
* GIT分支和SVN的分支不同：分支在SVN中一点不特别，就是版本库中的另外的一个目录
* GIT没有一个全局的版本号，而SVN有
* GIT的内容完整性要优于SVN

##### 安装 Git

​	在 Windows 上安装 Git 也有几种安装方法。 官方版本可以在 Git 官方网站下载。 打开 <http://git-scm.com/download/win>，下载会自动开始。 要注意这是一个名为 Git for Windows的项目（也叫做 msysGit），和 Git 是分别独立的项目；更多信息请访问 <http://msysgit.github.io/>。

另一个简单的方法是安装 GitHub for Windows。 该安装程序包含图形化和命令行版本的 Git。 它也能支持 Powershell，提供了稳定的凭证缓存和健全的 CRLF 设置。 稍后我们会对这方面有更多了解，现在只要一句话就够了，这些都是你所需要的。 你可以在 GitHub for Windows 网站下载，网址为 [http://windows.github.com](http://windows.github.com/)。

##### Git 工作流程

![](img\GIT工作流程.png)

##### Git 工作区域

* **工作区：**就是你在电脑里能看到的目录

* **暂存区：**英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）

* **版本库：**工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库

  * 仓库
    * 本地库：工作区中的版本库就是本地的仓库
    * 远程仓库：从远程服务端clone到本地库，通过commit提交到本地库，push推送到远程服务器仓库

  ![](img\git区域.png)

##### 仓库操作

- 克隆现有仓库

  ```properties
  git clone url
  url支持git、ssh、http、https等各种协议
  
  git clone [--template = <template_directory>]
  	  [-l] [-s] [ -  no-hardlinks] [-q] [-n] [--bare] [--mirror]
  	  [-o <name>] [-b <name>] [-u <upload-pack>] [ -  reference <repository>]
  	  [--dissociate] [--separate-git-dir <git dir>]
  	  [--depth <depth>] [ -  [no-] single-branch] [ -  no-tags]
  	  [--rerserse-submodules] [ -  [no-] shallow-submodules]
  	  [--jobs <n>] [ - ] <repository> [<directory>]
  	  
  	e.g:
  	git clone git@192.168.0.25:/znsd/17H1/oa/.git
  	
  ```

- 创建仓库

  ```properties
  git init [-q | --quiet] [--bare] [--template=<template_directory>]
  	  [--separate-git-dir <git dir>]
  	  [--shared[=<permissions>]] [directory]
  
  git init :在当前目录创建git仓库
  git init [dir] :在dir目录创建git仓库
  git init --bare :创建一个空git仓库
  
  e.g:
  git init 
  	在当前文件夹下生成 .git目录，完成初始化,所有文件处于unstaged状态
  git文件的各个状态： 
  1. unstaged git仓库中没有此文件的相关记录 
  2. modified git仓库中有这个文件的记录，并且此文件当前有改动 
  3. staged 追加，删除或修改的文件被暂时保存，这些追加、删除和修改并没有提交到git仓库 
  4. commited 追加或修改的文件被提交到本地git仓库
  	
  ```

- 连接远程仓库

  ```properties
  1：git remote add [shortname][url]
  	
  e.g:
  	git remote add origin git://192.168.0.25:/znsd/17H1/oa/.git
  	将本地仓库与远程仓库关联
  2：重定向远程仓库	
  	git remote set-url origin url	
  3：查看远程仓库
  	git remote 或 git remote -v 或 git remote show [remoteName]
  4：修改远程仓库
  	git remote rename [oldname] [newname]
  e.g:
  	git remote rename origin oa
  	将仓库名origin 改为 oa
  5：删除远程仓库
  	git remote rm [remote-name]
  	
  ```

- 更新数据

  ```properties
  git pull 
  ，并且自动合并到当前分支
  
  git pull oa master --allow-unrelated-histories
  添加远程仓库无法更新代码时，先查看.git目录下的config文件，然后执行该命令
  
  
  git fetch [remote-name] 
  获取仓库所有更新，但是不自动合并当前分支	
  ```

- 上传数据

  ```properties
  git push [remote-name] [branch-name]
  
  e.g:
  	git push origin master 将数据推送至origin库的master分支
  ```


#####  基本命令

* git add :将文件添加到缓存区

  ```properties
  git add 
  	[--verbose | -v] [--dry-run | -n] [--force | -f] [--interactive | -i] [--patch | -p]
  	  [--edit | -e] [--[no-]all | --[no-]ignore-removal | [--update | -u]]
  	  [--intent-to-add | -N] [--refresh] [--ignore-errors] [--ignore-missing]
  	  [--chmod=(+|-)x] [--] [<pathspec>…]
  
  e.g:
  	git add README.txt
  ```

* git status ：查看工作区状态

* git diff ： git diff 来查看执行 git status 的结果的详细信息，git diff 命令显示已写入缓存与已修改但尚未写入缓存的改动的区别

  - 尚未缓存的改动：**git diff**
  - 查看已缓存的改动： **git diff --cached**
  - 查看已缓存的与未缓存的所有改动：**git diff HEAD**
  - 显示摘要而非整个 diff：**git diff --stat**

* git commit ：将缓存区内容添加到仓库中

  ```properties
  git commit
  [-a | --interactive | --patch] [-s] [-v] [-u<mode>] [--amend]
  	   [--dry-run] [(-c | -C | --fixup | --squash) <commit>]
  	   [-F <file> | -m <msg>] [--reset-author] [--allow-empty]
  	   [--allow-empty-message] [--no-verify] [-e] [--author=<author>]
  	   [--date=<date>] [--cleanup=<mode>] [--[no-]status]
  	   [-i | -o] [-S[<keyid>]] [--] [<file>…]
  	   
  e.g:
  	git commit -m "提交readme文件"
  ```

* git reset  ：取消已缓存的内容

  ```properties
  git reset [-q] [<tree-ish>] [--] <paths>
  
  e.g:
  	git reset HEAD README.TXT
  ```

* git rm   ：删除文件

  ```properties
  git rm [-f | --force] [-n] [-r] [--cached] [--ignore-unmatch] [--quiet] [--] <file>…
  
  e.g:
  	git rm -f <file>   ：删除之前修改过并且已经放到暂存区域的文件
  	git rm --cached <file>  ：文件从暂存区域移除
  	git rm –r *     ：递归删除 * 目录下的所有文件及子目录
  ```

* git mv  ：移动、重命名一个文件或目录、软链接

  ```properties
  git mv [<options>] <source>... <destination>
  	-v, --verbose         be verbose
      -n, --dry-run         dry run
      -f, --force           force move/rename even if target exists
      -k                    skip move/rename errors
  
  e.g:
  	git mv README  read.txt  :将README文件重命名为read.txt
  ```



##### 分支管理

* git branch

```properties
创建分支：
	git branch (branchName) 
切换分支：
	git checkout branchName
合并分支：
	git merge branchName   （合并branchName分支到当前分支）
删除分支：
	git branch -d (branchname)

e.g:
	在当次代码版本创建mirror分支：
	git branch mirror
	切换至mirror分支：
	git checkout mirror
	修改mirror分支下的文件，然后提交至版本库
	git add .  ->  git commit 
	切换至master主分支
	git checkout master
	查看在mirror分支修改过并提交的文件，mirror分支修改后的代码是否有在master分支体现
	在master分支修改同一个文件，并提交至版本库
	再切换至mirror分支，查看改动的文件是否有变
	合并mirror分支至master
	切换至master分支
	git checkout master
	合并mirror至master
	git merge mirror
	会看到提示合并失败，因为产生了版本冲突，进行手工修改原文件冲突
	然后进行提交
	git add .   -->   git commit -m ""   ---> git push
```

##### 标签

​	git tag ： 为某一次提交的版本打上一个标记，便于查看提交记录及修改内容！

```properties
创建标签： git tag -a tag_name 或 git tag tag_name
	git tag -a v1.0 || git tag  v1.0 
查看标签：
	git tag
显示标签提交详情：
	git show tag_name
```
