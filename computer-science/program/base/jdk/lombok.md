# lombok                                                                   
## contents                                                                
- [@EqualsAndHashCode](#@EqualsAndHashCode)                                                        

#Method
## @EqualsAndHashCode 
   https://blog.csdn.net/zhanlanmg/article/details/50392266
   关于@Data注解包含@EqualsAndHashCode(callSuper=false)的问题 
   我们的实体类都是继承公共父类:  IdDongleEntity,这就可能出现问题: 不同的对象因为@Data生成hashCode/equas相同而出现相同，其实是不同的。所以，应当在使用@Data时使用@EqualsAndHashCode(callSuper=true)
