#  防火墙配置                                                                     
## 目录  
- [参考](#参考)                                                               
- [下载](#下载)                                                        
- [配置](#配置)     
- [系统右键注册](#系统右键注册) 
- [ls中文支持](#ls中文支持)                                                
  

#参考
   [https://www.jianshu.com/p/bdec97cfa605](https://www.jianshu.com/p/bdec97cfa605)


#下载
 
   Cmder可以说是Windows下一款非常好用的cmd替代品了，无需安装，解压即可使用。
   支持多标签、快捷键、搜索、锁定视窗、初始任务等。

   官网：http://cmder.net/

   cmder有两种安装包可选择：

mini版 功能简单，很小巧，只有4M多，主要是cmd和powershell
full版 功能强大，包含了git、powershell、bash、chocolatey、Cygwin、SDK等功能
需要注意的是，如果本地没有安装Git时选择了mini安装包，此时的命令行下是没有Linux相关命令的。


#配置
配置其能够在Win+R 中打开
将cmder的文件路径添加到环境变量PATH中即可


#系统右键注册
在管理员权限下打开cmd，输入以下命令
Cmder.exe /REGISTER ALL


#ls中文支持
默认安装好后，ls命令会显示中文乱码，需要打开setting窗口(win+alt+p)，在Settings > Startup > Environment里添加：set LANG=zh_CN.UTF8


