# Nexus3.X忘记admin密码找回 #

一、问题背景

nexus3 这种东西，传完一次，很少动了，很容易忘记密码，不要急有方法找回。

官方网站关于解决该问题的方法：

    https://support.sonatype.com/hc/en-us/articles/213467158-How-to-reset-a-forgotten-admin-password-in-Nexus-3-x

二、解决

2.1 第一步：停止服务

将nexus停止运行

2.2 第二步：进入OrientDB控制台

进入nexus的安装目录下：

博主本人安装目录在：

```
[018@localhost ~]$ ll /application/nexus/
总用量 72
drwxr-xr-x.  3 018 018    73 5月  14 00:10 bin
drwxr-xr-x.  2 018 018    26 5月  14 00:06 deploy
drwxr-xr-x.  7 018 018   104 5月  14 00:06 etc
drwxr-xr-x.  4 018 018   184 5月  14 00:06 lib
-rw-r--r--.  1 018 018   395 7月  12 2018 NOTICE.txt
-rw-r--r--.  1 018 018 17321 7月  12 2018 OSS-LICENSE.txt
-rw-r--r--.  1 018 018 39222 7月  12 2018 PRO-LICENSE.txt
drwxr-xr-x.  3 018 018  4096 5月  14 00:06 public
drwxr-xr-x. 22 018 018  4096 5月  24 15:40 system
```

进入该目录下：

	[018@localhost nexus]$ cd /application/nexus/

Linux、Windows、Mac有不同的进入方法，方法如下：

linux:  

	java -jar ./lib/support/nexus-orient-console.jar
 
win:  

	java -jar lib\support\nexus-orient-console.jar
 
mac: 

	.install4j/jre.bundle/Contents/Home/jre/bin/java -jar ./lib/support/nexus-orient-console.jar

2.3 第三步：进入数据库

	connect plocal:../sonatype-work/nexus3/db/security admin admin

2.4 第四步：重置密码

此处我们将admin用户密码重置为admin123，具体执行如下：

	update user SET password="$shiro1$SHA-512$1024$NE+wqQq/TmjZMvfI7ENh/g==$V4yPw8T64UQ6GfJfxYq2hLsVrBY8D1v+bktfOxGdt4b/9BthpWPNUy/CBk6V9iA0nHpzYzJFWO8v/tZFtES8CA==" UPSERT WHERE id="admin"

2.5 第五步：启动服务

启动nexus服务

转载于:https://www.cnblogs.com/forever521Lee/p/10919512.html