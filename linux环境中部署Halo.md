# linux环境中部署Halo #

1.安装JDK：

	sudo yum install java-11-openjdk -y

2.检查版本：

	java --version

3.创建存放运行包的目录

	mkdir ~/app && cd ~/app

4.创建工作目录

	mkdir ~/.halo && cd ~/.halo

5.下载示例配置文件到工作目录

	wget https://dl.halo.run/config/application-template.yaml -O ./application.yaml

注：如果没有wget 请使用下面命令安装

	yum -y install wget

6.测试运行Halo

	cd ~/app && java -jar halo.jar

注：centos默认没有开启8090端口使用下面命令开启

	firewall-cmd --zone=public --add-port=8090/tcp --permanent     #添加8090端口
	firewall-cmd --reload   #是配置立即生效

到这里恭喜你已经部署自己的blog！


最后打开浏览器输入：127.0.0.1:8090即可访问你的网站，登录后台进行完善网页发布你的第一条blog

