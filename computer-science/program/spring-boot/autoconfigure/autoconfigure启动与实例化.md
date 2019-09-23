#autoconfigure启动与实例化                                                                   
##contents                                                                
- [autoconfigure](#autoconfigure) 
- [启动太慢](#启动太慢) 


#autoconfigure
 就是交给springboot,启动时初始化所有的@service/@bean/@configure;
 实例由springboot统一管理


#启动太慢
   因为随着业务不断发展，开发的实例也日益增加，实例间的关系也变得复杂。

##分模块启动
  让正在开发的模块放入启动环境中，无关模块不加进来。但是，这种方式，启动依然比较慢。

##应发展为微服务
  springboot后续转微服务，单模块远程启动，本地正在开发的模块，通过链接到远程服务。

##使用devtools
 进行热部署，修改代码不用重新启动应用。但是devtools和quartz冲突，导致quartz不能正常启动，所以不使用
  devtools.https://blog.csdn.net/chen15369337607/article/details/78433508
   https://blog.csdn.net/wumanxin2018/article/details/78109355
   https://blog.csdn.net/testcs_dn/article/details/79929886
       
       <!-- 热部署模块 -->
      <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
       </dependency>
       

##实现模块化
    https://www.jb51.net/article/144754.htm

    我们也可以导入自己的Enable配置：
    
    @SpringBootApplication
    @EnableBookingModule
    public class ModularApplication {
     public static void main(String[] args) {
     SpringApplication.run(ModularApplication.class, args);
     }
    }
    上面代码中EnableBookingModule不是Spring自己的注释，而是我们自己的定做的，代码如下：
    
    @Retention(RetentionPolicy.RUNTIME)
    @Target({ElementType.TYPE})
    @Documented
    @Import(BookingModuleConfiguration.class)
    @Configuration
    public @interface EnableBookingModule {
    }

     因为实例不是简单的创建，还要考虑外键关系。