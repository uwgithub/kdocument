#alibaba-druid                                                                   
##contents                                                                
- [druid报错](#druid报错) 



#druid报错
## maxEvictableIdleTimeMillis must be grater than minEvictableIdleTimeMillis
 https://www.cnblogs.com/wangiqngpei557/p/7136455.html?utm_source=itdadao&utm_medium=referral
 https://blog.csdn.net/qq_26975307/article/details/83819542
 
  一：先把配置的minEvictableIdleTimeMillis、maxEvictableIdleTimeMillis获取进来

    @Data
    @EqualsAndHashCode
    @ConfigurationProperties(prefix = "ecommon.order.druid")
    public class SqlServerDruidConfig {
    private Long minEvictableIdleTimeMillis;
    private Long maxEvictableIdleTimeMillis;
    }
二：然后自己先设置一下这两个属性，springboot autoconfig 的时候就不会出错

     @Autowired
    private SqlServerDruidConfig druidConfig;
     
    @Bean(name = "dataSource")
    @ConfigurationProperties(prefix = "ecommon.order.druid")
    public DataSource getOrderDataSource() {
     
    DruidDataSource dataSource = new DruidDataSource();
     
    /**setMinEvictableIdleTimeMillis需要先设置*/
    dataSource.setMinEvictableIdleTimeMillis(druidConfig.getMinEvictableIdleTimeMillis());
    dataSource.setMaxEvictableIdleTimeMillis(druidConfig.getMaxEvictableIdleTimeMillis());
     
    return dataSource;
    }
     
    @Bean
    public SqlServerDruidConfig getDruidConfig() {
    return new SqlServerDruidConfig();
    }
 

