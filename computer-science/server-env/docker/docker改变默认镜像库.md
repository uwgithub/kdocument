#改变默认镜像库                                                                    
## 目录                                                                
- [参考](#参考) 
- [提交镜像](#提交镜像)    
- [提交镜像](#提交镜像)   
- [切换基础镜像](#切换基础镜像)



#参考
   https://blog.csdn.net/qq_34777982/article/details/81368578


#更新镜像库
apt-get update && apt-get install vim -y
vim /etc/apt/sources.list

填入以下内容：

deb http://mirrors.aliyun.com/debian/ stretch main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ stretch main non-free contrib
deb http://mirrors.aliyun.com/debian-security stretch/updates main
deb-src http://mirrors.aliyun.com/debian-security stretch/updates main
deb http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib

然 apt-get update

exit

#提交镜像
查看容器ID，将刚才的容器提交为新的镜像

    docker ps -a
    docker commit -m 'debian-aliyun' -a='merry' 78d7f313cb78 debian:aliyun

#切换基础镜像
  修改下载的dockerfile 基础镜像 为刚才提交的镜像

  vim  DockerFile
 




