#autowired                                                                   
##contents                                                                
- [三种实例化](#三种实例化) 
- [指定boot基目录](#指定boot基目录) 


#三种实例化
##spring容器autowired
 spring boot启动过程中，实例化了@component @service @configure等注解的类


##注解反射实例化
  实现BaseAutoAware


##intercerptor只能手动实例化
  使用DongleApplicationUtil.getBean(xxx.class)


##多种实例化方式冲突
  总有一种先执行，注意判断非空性。也要使用@Lazy注解.


#指定boot基目录
   同一个工程，同一包前缀
   无论是mybatis plus: mapper + service;还是 spring data jpa: repository + service,都一样由spring boot autoconfig,一样实例化service(service类有@service),一样实例化mapper/respository
   