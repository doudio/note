> ### google - baidu - 搜索引擎

>  常用的 GoogHacking 语法

| 指令     | 说明                                         |
| -------- | -------------------------------------------- |
| intext   | 将网页中的正文内容中的某个字符作为搜索的条件 |
| intitle  | 将网页标题中的某个字符作为搜索的条件         |
| cache    | 搜索引擎里关于某些内容的缓存                 |
| filetype | 指定一个格式类型, 的文件作为搜索对象         |
| inurl    | 模糊匹配 url                                 |
| site     | 在指定的站点搜索相关内容                     |

> GoogHacking 其他语法

| 操作符 | 说明                                   |
| ------ | -------------------------------------- |
| ""     | 将关键字打上引号后, 作为一个整体来搜索 |
| or     | 搜索两个或多个的关键字                 |
| link   | 搜索某个网站的链接                     |

> 常见操作, 直接搜索获取后台等的信息：

* 找管理后台地址

```xml-dtd
site:www.***.com intext:管理|后台|登陆|用户名|密码|验证码|系统|帐号|manage|admin|login|system
site:www.***.com inurl:login|admin|manage|manager|admin_login|login_admin|system

site:www.***.com intitle:管理|后台|登陆|
site:www.***.com intext:验证码
```

* 找上传类漏洞地址

```xml-dtd
site:www.***.com inurl:file
site:www.***.com inurl:upload
```

* 找注入页面

```xml-dtd
site:www.***.com inurl:?id=
```

* 找编辑页面

```xml-dtd
site:www.***.com inurl:ewebeditor
```

