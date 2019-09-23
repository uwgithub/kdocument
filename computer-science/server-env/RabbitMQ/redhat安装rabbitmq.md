#  Redhat安装rebbitmq                                                                   
## 目录                                                                
- [安装epel](#安装epel)                                                        
- [安装erlang](#安装erlang) 
- [安装RabbitMQ](#安装RabbitMQ) 
- [配置网页管理插件](#配置网页管理插件) 


#安装epel
redhat提供的，自动配置yum的软件仓库
http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
http://mirrors.sohu.com/fedora-epel/epel-release-latest-7.noarch.rpm
cat /etc/redhat-release 
按redhat版本来安装，其实centos也这样查看版本
图片: https://uploader.shimo.im/f/suZq4XmOHcU9SHH4.png
是7.版本，就下载安装上面的rpm
wget http://mirrors.sohu.com/fedora-epel/epel-release-latest-7.noarch.rpm
rpm -ivh epel-release-latest-7.noarch.rpm
rpm -Uvh epel-release-latest-7.noarch.rpm
图片: https://uploader.shimo.im/f/MKgQTLSkclsPa0QA.png
清除建立缓存，使用yum安装nginx服务测试一下
 yum clean all
 yum makecache
 yum install nginx -y
到此epel源就安装完成了，通过epel源可以安装大部分的软件包

#安装erlang 
yum -y install erlang
图片: https://uploader.shimo.im/f/d343miVmqSkhVx51.png

#安装RabbitMQ
yum -y install rabbitmq-server

#配置网页管理插件
mkdir /etc/rabbitmq
cd /usr/lib/rabbitmq/bin
./rabbitmq-plugins enable rabbitmq_management
5.启动服务
service rabbitmq-server start 
service rabbitmq-server stop
6.访问页面
http://host:15672 
输入默认用户名及密码：guest/guest 





 