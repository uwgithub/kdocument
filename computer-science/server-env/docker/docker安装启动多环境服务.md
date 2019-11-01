#  docker及容器添加开机自启                                                                    
## 目录                                                                
- [docker多环境rabbit](#docker多环境rabbit)                                                        
- [docker多环境redis](#docker多环境redis)  


#docker多环境rabbit
      docker run -d --name rabbit-test -p 5672:5672 -p 15672:15672 --restart=always rabbitmq:3-management
      docker run -d --name rabbit-dev -p 6672:5672 -p 25672:15672 --restart=always rabbitmq:3-management
    
      docker stop rabbit-dev 
      docker start rabbit-dev
      docker rm rabbit-dev

#docker多环境redis
  再考虑:  Docker 安装 Redis
  https://www.runoob.com/docker/docker-install-redis.html

  https://blog.csdn.net/HeatDeath/article/details/80364340

      docker run -p 20001:6379 -d redis redis-server --appendonly yes
      docker run -p 20002:6379 -d redis redis-server --appendonly yes
      docker run -p 20003:6379 -d redis redis-server --appendonly yes
      docker run -p 20004:6379 -d redis redis-server --appendonly yes
      docker run -p 20005:6379 -d redis redis-server --appendonly yes

  docker-redis-
  127.0.0.1
  2000

  端口映射，data目录映射，配置文件映射。

    # docker run -p 6699:6379 --name myredis -v $PWD/redis.conf:/etc/redis/redis.conf -v $PWD/data:/data -d redis:3.2 redis-server /etc/redis/redis.conf --appendonly yes



  https://www.cnblogs.com/cgpei/p/7151612.html
 
  https://blog.csdn.net/flymoringbird/article/details/80717700
  docker 安装redis 并配置外网可以访问

https://blog.csdn.net/u011940361/article/details/79596483



