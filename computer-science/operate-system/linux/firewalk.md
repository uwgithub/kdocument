#  防火墙配置                                                                     
## 目录                                                                
- [Redhat防火墙](#redhat防火墙)                                                        
- [iptables](#iptables)     
- [开放端口](#开放端口)                                                
  

#  默认防火墙

虚拟机新装了一个RedHat 7/CentOs7，然后做防火墙配置的时候找不到iptables文件,因为默认使用的是firewall作为防火墙，可以把他停掉装个iptable.
Failed to restart iptables.service: Unit not found.
Redhat 7 由firewalld来管理，
重新载入
firewall-cmd --reload
查看
firewall-cmd --zone=public --query-port=80/tcp
删除
firewall-cmd --zone=public --remove-port=80/tcp --permanent


Redhat 7 开通防火墙端口
1.查看防火墙状态，root用户登录，执行命令systemctl status firewalld

2.开启防火墙：systemctl start firewalld

3.关闭防火墙：systemctl stop firewalld

4.放行端口：firewall-cmd --add-port=15672/tcp，此处需要注意cmd和--之间有空格


# iptables

## 安装iptables
  systemctl status firewalld --查看防火墙firwalld是否开启
  systemctl stop firewalld   -- 停掉防火墙firwalld
  systemctl mask firewalld

  yum install -y iptables 
  yum install iptables-services  --如果报错，缺依赖包，获取方法详见本目录<<linux yum依赖包>>中找

##开启服务 
systemctl start iptables.service

systemctl restart iptables.service // 重启防火墙使配置生效 
systemctl enable iptables.service // 设置防火墙开机启动

其他命令： 
检查是否安装了iptables 
service iptables status 
安装iptables 
yum install -y iptables 
升级iptables 
yum update iptables 
安装iptables-services 
yum install iptables-services

systemctl disable iptables #禁止iptables服务 
systemctl stop iptables #暂停服务 
systemctl enable iptables #解除禁止iptables 

systemctl start iptables #开启服务


#开放端口
linux系统对外开放80、8080等端口，防火墙设置
我们很多时候在liunx系统上安装了web服务应用后（如tomcat、apache等），需要让其它电脑能访问到该应用，而Linux系统（centos、redhat等）的防火墙是默认只对外开放了22端口。

下面的命令可以关闭/打开防火墙（需要重启系统）

﻿﻿1.下面的命令可以关闭/打开防火墙（需要重启系统）

开启： chkconfig iptables on 

关闭： chkconfig iptables off 

下面的代码可以启动和停止防火墙（立即生效，重启后失效）

启动：service iptables start 

关闭：service iptables stop iptables

方式设置防火墙规则在wmware中安装linux后安装好数据库，JDK及tomcat后启动服务，虚拟机中可以访问，但是主机却无法访问，但是同时主机和虚拟机之间可以ping的通，网上查阅资料后，

解决方法是

##方法1.用root登录后，执行 service iptables stop --停止 service iptables start --启动 

 但是在实际应用中，关闭防火墙降低的服务器的安全性，不能关闭防火墙。

 如果在宿主机的dos窗口下telnet虚拟机的8080窗口，会失败，由此可以确定是虚拟机的8080窗口有问题，应该是被防火墙堵住了。因此修改防火墙设置即可。 

##方法2.修改Linux系统防火墙配置需要修改 /etc/sysconfig/iptables 这个文件，如果要开放哪个端口，在里面添加一条
 vi  /etc/sysconfig/iptables
 /etc/sysconfig/iptables
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
systemctl reload iptables
service iptables save
systemctl restart iptables  --不能重启,不然就失效了

事实上使用(不要service iptables save,否则丢失配置值):
-A IN_public_allow -p tcp -m tcp --dport 6379 -m conntrack --ctstate NEW -j ACCEPT
-A IN_public_allow -p tcp -m tcp --dport 7379 -m conntrack --ctstate NEW -j ACCEPT
systemctl stop iptables
systemctl start iptables

 -A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT 就可以了，其中 8080 是要开放的端口号，然后重新启动linux的防火墙服务，

 /etc/init.d/iptables restart。 

##方法3.在命令行添加外网端口访问 linux开启允许外网访问的端口 LINUX开启允许对外访问的网络端口 LINUX通过下面的命令可以开启允许对外访问的网络端口：

 /sbin/iptables -I INPUT -p tcp --dport 8000 -j ACCEPT 

 开启8000端口 /etc/rc.d/init.d/iptables save 

 保存配置 /etc/rc.d/init.d/iptables restart

 重启服务 查看端口是否已经开放 /etc/init.d/iptables status


