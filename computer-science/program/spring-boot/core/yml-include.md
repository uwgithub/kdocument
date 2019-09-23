#include-yml                                                                   
##contents                                                                
- [使用外部yml](#使用外部yml) 



#使用外部yml
 https://blog.csdn.net/Lonely_Devil/article/details/81875890
 https://blog.csdn.net/yuhui123999/article/details/80585027
 
     spring:
       profiles:
         include: email,xmyb

    值得注意的是： 比如application-email.yml的前缀一定要和主在配置文件application.yml的名字一致

