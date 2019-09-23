#  yum安装依赖包                                                                    
## 目录                                                                
- [yum安装依赖包](#yum安装依赖包)                                                        
- [iptables依赖包](#iptables依赖包)     

 
#  yum安装依赖包                                                                     
## 目录                                                                
- [iptables依赖包](#iptables依赖包)  


#iptables依赖包
##安装iptables时缺失包:
anaconda-core-21.48.22.156-1.el7.x86_64 has missing requires of yum-utils >= ('0', '1.1.11', '3')
rhn-check-2.0.2-24.el7.x86_64 has missing requires of yum-rhn-plugin >= ('0', '1.6.4', '1')
Requires: glibc-common = 2.17-260.el7_6.6 
glibc = 2.17-260.el7 is needed by glibc-common-2.17-260.el7.x86_64  

我们使用的yum是centos7的，从  http://mirrors.163.com/centos/7.6.1810/os/x86_64/Packages/ 找
yum-utils  =>  yum-utils-1.1.31-50.el7.noarch.rpm
yum-rhn-plugin => yum-rhn-plugin-2.0.1-10.el7.noarch.rpm

##下载和安装
wget http://mirrors.163.com/centos/7.6.1810/os/x86_64/Packages/yum-utils-1.1.31-50.el7.noarch.rpm
wget http://mirrors.163.com/centos/7.6.1810/os/x86_64/Packages/yum-rhn-plugin-2.0.1-10.el7.noarch.rpm
wget http://mirrors.163.com/centos/7.6.1810/os/x86_64/Packages/glibc-common-2.17-260.el7.x86_64.rpm
wget http://mirrors.163.com/centos/7.6.1810/os/x86_64/Packages/glibc-2.17-260.el7.x86_64.rpm 


rpm -ivh yum-utils-1.1.31-50.el7.noarch.rpm yum-rhn-plugin-2.0.1-10.el7.noarch.rpm glibc-common-2.17-260.el7.x86_64.rpm glibc-2.17-260.el7.x86_64.rpm

