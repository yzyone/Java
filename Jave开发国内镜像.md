# Java开发国内镜像 #
---

## Maven国内镜像： ##

官网地址：http://maven.aliyun.com/

使用方法：

找到Maven的setting.xml，然后在镜像配置中加入阿里云的仓库。

```
<mirrors>
  <mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
  </mirror>
</mirrors>
```

## Srping的国内脚手架 ##

官网地址：https://start.aliyun.com/

