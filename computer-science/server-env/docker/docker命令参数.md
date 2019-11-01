#docker命令参数                                                                    
## 目录 
- [查看版本](#查看版本) 
- [管理镜像](#管理镜像)   
- [开机启动](#开机启动)      
- [root权限](#root权限)                                                          
- [后台执行与交互命令](#后台执行与交互命令)      
- [attatch和logs](#attatch和logs) 
- [rm是一次性的](#rm是一次性的) 




#查看版本
    单杠单字，双杠多字
    docker version
    docker -v
    docker --version


#管理镜像
  --下载最新镜像

  docker pull

  --查看镜像

  docker images

  --防火墙
  docker容器默认已经绕过防火墙，防火墙配置中已经自动添加accept端口

  ---执行pid 

  docker exec -it 43f7a65ec7f8 redis-cli
 
#开机启动
 --restart=always

 随docker启动而启动，所以设置开机启动就只要设置docker为开机启动(安装docker时默认设置为开机启动了)

#root权限
  --privileged=true：

 容器内的root拥有真正root权限，否则容器内root只是外部普通用户权限


#后台执行与交互命令
   [参考](https://www.cnblogs.com/JMLiu/p/10277482.html)
    
   -d 和 -it(你要运行交互的程序，那么就用-it。否则，就什么参数都不带）
   

      docker run -d --name some-mysql -v /my/custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw  mysql:tag
      docker run --name some-mysql -v /my/custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw  mysql:tag
      docker exec -it 43f7a65ec7f8 redis-cli
      


#attatch和logs
  -a STDIN -a STDOUT -a STDERR
  输出比较大不用attach去看，而应该用docker logs去看log


#rm是一次性的
--rm
容器退出后，自动删除容器






