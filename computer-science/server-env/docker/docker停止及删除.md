#docker及容器添加开机自启                                                                    
## 目录                                                                
- [停止container](#停止container)      
- [启动container](#启动container)                                                  
- [删除containe](#删除containe)  
- [查看images](#查看images)  


#停止container
  停止所有的container，
  这样才能够删除其中的images：

  docker stop $(docker ps -a -q)
  docker stop 容器名

#启动container
  docker start 容器名

#删除container
如果想要删除所有container的话再加一个指令：

 docker rm $(docker ps -a -q)
 docker rm 容器名

#查看images
 查看当前有些什么images

  docker images
   

#删除images
 删除images，通过image的id来指定删除谁

  docker rmi <image id>

想要删除untagged images，也就是那些id为<None>的image的话可以用

docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

要删除全部image的话

docker rmi $(docker images -q)







