#整合到springboot                                                                   
##contents  
- [案例主参考](#案例主参考)                                                               
- [Quartz官方的脚本](#Quartz官方的脚本) 
- [2.x自动化配置](#2.x自动化配置)
- [自定义任务表](#自定义任务表)
- [使用JPA](#使用JPA)
- [动态配置任务](#动态配置任务)
- [cronExpression表达式](#cronExpression表达式)
- [疑难问题定位](#疑难问题定位)


#案例主参考
   https://www.cnblogs.com/ealenxie/p/9134602.html

#Quartz官方的脚本
 下载网址: https://github.com/quartz-scheduler/quartz/tree/master/quartz-core/src/main/resources/org/quartz/impl/jdbcjobstore                                              
 选择相应数据库的脚本
  ![avatar][downloadScriptBase64]
 
  postgresql脚本内容:

    -- Thanks to Patrick Lightbody for submitting this...
    --
    -- In your Quartz properties file, you'll need to set 
    -- org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.PostgreSQLDelegate
    DROP TABLE IF EXISTS QRTZ_FIRED_TRIGGERS;
    DROP TABLE IF EXISTS QRTZ_PAUSED_TRIGGER_GRPS;
    DROP TABLE IF EXISTS QRTZ_SCHEDULER_STATE;
    DROP TABLE IF EXISTS QRTZ_LOCKS;
    DROP TABLE IF EXISTS QRTZ_SIMPLE_TRIGGERS;
    DROP TABLE IF EXISTS QRTZ_CRON_TRIGGERS;
    DROP TABLE IF EXISTS QRTZ_SIMPROP_TRIGGERS;
    DROP TABLE IF EXISTS QRTZ_BLOB_TRIGGERS;
    DROP TABLE IF EXISTS QRTZ_TRIGGERS;
    DROP TABLE IF EXISTS QRTZ_JOB_DETAILS;
    DROP TABLE IF EXISTS QRTZ_CALENDARS;
    
    CREATE TABLE QRTZ_JOB_DETAILS
    (
      SCHED_NAMEVARCHAR(120) NOT NULL,
      JOB_NAME  VARCHAR(200) NOT NULL,
      JOB_GROUP VARCHAR(200) NOT NULL,
      DESCRIPTION   VARCHAR(250) NULL,
      JOB_CLASS_NAMEVARCHAR(250) NOT NULL,
      IS_DURABLEBOOL NOT NULL,
      IS_NONCONCURRENT  BOOL NOT NULL,
      IS_UPDATE_DATABOOL NOT NULL,
      REQUESTS_RECOVERY BOOL NOT NULL,
      JOB_DATA  BYTEANULL,
      PRIMARY KEY (SCHED_NAME, JOB_NAME, JOB_GROUP)
    );
    
    CREATE TABLE QRTZ_TRIGGERS
    (
      SCHED_NAME VARCHAR(120) NOT NULL,
      TRIGGER_NAME   VARCHAR(200) NOT NULL,
      TRIGGER_GROUP  VARCHAR(200) NOT NULL,
      JOB_NAME   VARCHAR(200) NOT NULL,
      JOB_GROUP  VARCHAR(200) NOT NULL,
      DESCRIPTIONVARCHAR(250) NULL,
      NEXT_FIRE_TIME BIGINT   NULL,
      PREV_FIRE_TIME BIGINT   NULL,
      PRIORITY   INTEGER  NULL,
      TRIGGER_STATE  VARCHAR(16)  NOT NULL,
      TRIGGER_TYPE   VARCHAR(8)   NOT NULL,
      START_TIME BIGINT   NOT NULL,
      END_TIME   BIGINT   NULL,
      CALENDAR_NAME  VARCHAR(200) NULL,
      MISFIRE_INSTR  SMALLINT NULL,
      JOB_DATA   BYTEANULL,
      PRIMARY KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP),
      FOREIGN KEY (SCHED_NAME, JOB_NAME, JOB_GROUP)
      REFERENCES QRTZ_JOB_DETAILS (SCHED_NAME, JOB_NAME, JOB_GROUP)
    );
    
    CREATE TABLE QRTZ_SIMPLE_TRIGGERS
    (
      SCHED_NAME  VARCHAR(120) NOT NULL,
      TRIGGER_NAMEVARCHAR(200) NOT NULL,
      TRIGGER_GROUP   VARCHAR(200) NOT NULL,
      REPEAT_COUNTBIGINT   NOT NULL,
      REPEAT_INTERVAL BIGINT   NOT NULL,
      TIMES_TRIGGERED BIGINT   NOT NULL,
      PRIMARY KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP),
      FOREIGN KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
      REFERENCES QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
    );
    
    CREATE TABLE QRTZ_CRON_TRIGGERS
    (
      SCHED_NAME  VARCHAR(120) NOT NULL,
      TRIGGER_NAMEVARCHAR(200) NOT NULL,
      TRIGGER_GROUP   VARCHAR(200) NOT NULL,
      CRON_EXPRESSION VARCHAR(120) NOT NULL,
      TIME_ZONE_IDVARCHAR(80),
      PRIMARY KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP),
      FOREIGN KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
      REFERENCES QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
    );
    
    CREATE TABLE QRTZ_SIMPROP_TRIGGERS
    (
      SCHED_NAMEVARCHAR(120)   NOT NULL,
      TRIGGER_NAME  VARCHAR(200)   NOT NULL,
      TRIGGER_GROUP VARCHAR(200)   NOT NULL,
      STR_PROP_1VARCHAR(512)   NULL,
      STR_PROP_2VARCHAR(512)   NULL,
      STR_PROP_3VARCHAR(512)   NULL,
      INT_PROP_1INTNULL,
      INT_PROP_2INTNULL,
      LONG_PROP_1   BIGINT NULL,
      LONG_PROP_2   BIGINT NULL,
      DEC_PROP_1NUMERIC(13, 4) NULL,
      DEC_PROP_2NUMERIC(13, 4) NULL,
      BOOL_PROP_1   BOOL   NULL,
      BOOL_PROP_2   BOOL   NULL,
      PRIMARY KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP),
      FOREIGN KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
      REFERENCES QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
    );
    
    CREATE TABLE QRTZ_BLOB_TRIGGERS
    (
      SCHED_NAMEVARCHAR(120) NOT NULL,
      TRIGGER_NAME  VARCHAR(200) NOT NULL,
      TRIGGER_GROUP VARCHAR(200) NOT NULL,
      BLOB_DATA BYTEANULL,
      PRIMARY KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP),
      FOREIGN KEY (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
      REFERENCES QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP)
    );
    
    CREATE TABLE QRTZ_CALENDARS
    (
      SCHED_NAMEVARCHAR(120) NOT NULL,
      CALENDAR_NAME VARCHAR(200) NOT NULL,
      CALENDAR  BYTEANOT NULL,
      PRIMARY KEY (SCHED_NAME, CALENDAR_NAME)
    );
    
    
    CREATE TABLE QRTZ_PAUSED_TRIGGER_GRPS
    (
      SCHED_NAMEVARCHAR(120) NOT NULL,
      TRIGGER_GROUP VARCHAR(200) NOT NULL,
      PRIMARY KEY (SCHED_NAME, TRIGGER_GROUP)
    );
    
    CREATE TABLE QRTZ_FIRED_TRIGGERS
    (
      SCHED_NAMEVARCHAR(120) NOT NULL,
      ENTRY_ID  VARCHAR(95)  NOT NULL,
      TRIGGER_NAME  VARCHAR(200) NOT NULL,
      TRIGGER_GROUP VARCHAR(200) NOT NULL,
      INSTANCE_NAME VARCHAR(200) NOT NULL,
      FIRED_TIMEBIGINT   NOT NULL,
      SCHED_TIMEBIGINT   NOT NULL,
      PRIORITY  INTEGER  NOT NULL,
      STATE VARCHAR(16)  NOT NULL,
      JOB_NAME  VARCHAR(200) NULL,
      JOB_GROUP VARCHAR(200) NULL,
      IS_NONCONCURRENT  BOOL NULL,
      REQUESTS_RECOVERY BOOL NULL,
      PRIMARY KEY (SCHED_NAME, ENTRY_ID)
    );
    
    CREATE TABLE QRTZ_SCHEDULER_STATE
    (
      SCHED_NAMEVARCHAR(120) NOT NULL,
      INSTANCE_NAME VARCHAR(200) NOT NULL,
      LAST_CHECKIN_TIME BIGINT   NOT NULL,
      CHECKIN_INTERVAL  BIGINT   NOT NULL,
      PRIMARY KEY (SCHED_NAME, INSTANCE_NAME)
    );
    
    CREATE TABLE QRTZ_LOCKS
    (
      SCHED_NAME VARCHAR(120) NOT NULL,
      LOCK_NAME  VARCHAR(40)  NOT NULL,
      PRIMARY KEY (SCHED_NAME, LOCK_NAME)
    );
    
    CREATE INDEX IDX_QRTZ_J_REQ_RECOVERY
      ON QRTZ_JOB_DETAILS (SCHED_NAME, REQUESTS_RECOVERY);
    CREATE INDEX IDX_QRTZ_J_GRP
      ON QRTZ_JOB_DETAILS (SCHED_NAME, JOB_GROUP);
    
    CREATE INDEX IDX_QRTZ_T_J
      ON QRTZ_TRIGGERS (SCHED_NAME, JOB_NAME, JOB_GROUP);
    CREATE INDEX IDX_QRTZ_T_JG
      ON QRTZ_TRIGGERS (SCHED_NAME, JOB_GROUP);
    CREATE INDEX IDX_QRTZ_T_C
      ON QRTZ_TRIGGERS (SCHED_NAME, CALENDAR_NAME);
    CREATE INDEX IDX_QRTZ_T_G
      ON QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_GROUP);
    CREATE INDEX IDX_QRTZ_T_STATE
      ON QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_STATE);
    CREATE INDEX IDX_QRTZ_T_N_STATE
      ON QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP, TRIGGER_STATE);
    CREATE INDEX IDX_QRTZ_T_N_G_STATE
      ON QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_GROUP, TRIGGER_STATE);
    CREATE INDEX IDX_QRTZ_T_NEXT_FIRE_TIME
      ON QRTZ_TRIGGERS (SCHED_NAME, NEXT_FIRE_TIME);
    CREATE INDEX IDX_QRTZ_T_NFT_ST
      ON QRTZ_TRIGGERS (SCHED_NAME, TRIGGER_STATE, NEXT_FIRE_TIME);
    CREATE INDEX IDX_QRTZ_T_NFT_MISFIRE
      ON QRTZ_TRIGGERS (SCHED_NAME, MISFIRE_INSTR, NEXT_FIRE_TIME);
    CREATE INDEX IDX_QRTZ_T_NFT_ST_MISFIRE
      ON QRTZ_TRIGGERS (SCHED_NAME, MISFIRE_INSTR, NEXT_FIRE_TIME, TRIGGER_STATE);
    CREATE INDEX IDX_QRTZ_T_NFT_ST_MISFIRE_GRP
      ON QRTZ_TRIGGERS (SCHED_NAME, MISFIRE_INSTR, NEXT_FIRE_TIME, TRIGGER_GROUP, TRIGGER_STATE);
    
    CREATE INDEX IDX_QRTZ_FT_TRIG_INST_NAME
      ON QRTZ_FIRED_TRIGGERS (SCHED_NAME, INSTANCE_NAME);
    CREATE INDEX IDX_QRTZ_FT_INST_JOB_REQ_RCVRY
      ON QRTZ_FIRED_TRIGGERS (SCHED_NAME, INSTANCE_NAME, REQUESTS_RECOVERY);
    CREATE INDEX IDX_QRTZ_FT_J_G
      ON QRTZ_FIRED_TRIGGERS (SCHED_NAME, JOB_NAME, JOB_GROUP);
    CREATE INDEX IDX_QRTZ_FT_JG
      ON QRTZ_FIRED_TRIGGERS (SCHED_NAME, JOB_GROUP);
    CREATE INDEX IDX_QRTZ_FT_T_G
      ON QRTZ_FIRED_TRIGGERS (SCHED_NAME, TRIGGER_NAME, TRIGGER_GROUP);
    CREATE INDEX IDX_QRTZ_FT_TG
      ON QRTZ_FIRED_TRIGGERS (SCHED_NAME, TRIGGER_GROUP);
    
    
    COMMIT;
    
#2.x自动化配置
  只需添加依赖
      > <dependency>
>        <groupId>org.springframework.boot</groupId>
>        <artifactId>spring-boot-starter-quartz</artifactId>
>       </dependency>

  有这个spring-boot-starter-quartz已经为我们自动化配置好,我们不再需要QuartzConfiguration配置类来完成了Quartz需要 的一系列配置，如：JobFactory、SchedulerFactoryBean等,直接删除QuartzConfiguration配置类。
  具体参考: https://www.jianshu.com/p/056281e057b3

##spring.quartz配置
   yml配置文件:
     
    spring:
      quartz:
      #相关属性配置
        properties:
          org:
            quartz:
              scheduler:
                instanceName: clusteredScheduler
                instanceId: AUTO
              jobStore:
                class: org.quartz.impl.jdbcjobstore.JobStoreTX
                driverDelegateClass: org.quartz.impl.jdbcjobstore.PostgreSQLDelegate
                tablePrefix: QRTZ_
                isClustered: true
                clusterCheckinInterval: 10000
                useProperties: false
              threadPool:
                class: org.quartz.simpl.SimpleThreadPool
                threadCount: 10
                threadPriority: 5
                threadsInheritContextClassLoaderOfInitializingThread: true

      #数据库方式
      job-store-type: jdbc
      #初始化表结构
      #jdbc:
        #initialize-schema: never

    注意: 对于postgresql：
    -- In your Quartz properties file, you'll need to set 
    -- org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.PostgreSQLDelegate
    不然报错:
org.quartz.JobPersistenceException: Couldn't obtain triggers for job: 不良的类型值 long : \x [See nested exception: org.postgresql.util.PSQLException: 不良的类型值 long : \x]
其它数据库用:
org.quartz.impl.jdbcjobstore.StdJDBCDelegate 
可以参考:  https://blog.csdn.net/weixin_34114823/article/details/91882387
     


##spring.quartz.properties
    spring.quartz.properties其实代替了之前的quartz.properties
    
##spring.quartz.job-store-type
   设置quartz任务的数据持久化方式，默认是内存方式，我们这里沿用之前的方式，配置JDBC以使用数据库方式持久化任务。

##spring.quartz.jdbc.initialize-schema
   该配置目前版本没有生效，根据官网文档查看，其目的是自动将quartz需要的数据表通过配置方式进行初始化。


#自定义任务表和日志表
             drop index if exists idx_qtz_job_ct;
             
             drop index if exists Idx_qtz_job_name;
             
             drop index if exists idx_qtz_job_group;
             
             drop table if exists app_quartz_job  CASCADE;
             
             drop index if exists idx_qtz_log_ct;
             
             drop index if exists Idx_qtz_job_id;
             
             drop table if exists app_quartz_log  CASCADE;
             
             /*==============================================================*/
             /* Table: app_quartz_job                                        */
             /*==============================================================*/
             create table app_quartz_job (
                id                   VARCHAR(64)          not null,
                name                 VARCHAR(255)         null,
                job_group            VARCHAR(255)         null,
                cron_expression      VARCHAR(255)         null,
                bean_class           VARCHAR(255)         null,
                method_name          VARCHAR(255)         null,
                parameter            VARCHAR(255)         null,
                vm_param             VARCHAR(255)         null,
                jar_path             VARCHAR(255)         null,
                status               CHAR(1)              null,
                del_flag             CHAR(1)              not null default '0'::bpchar,
                remarks              VARCHAR(500)         null,
                create_by            VARCHAR(32)          null,
                create_time          TIMESTAMP            null,
                update_by            VARCHAR(32)          null,
                update_time          TIMESTAMP            null,
                constraint PK_app_quartz_job primary key (id)
             )
             without oids;
             
             comment on table app_quartz_job is
             'qrtz定时器';
             
             comment on column app_quartz_job.name is
             '任务类名';
             
             comment on column app_quartz_job.job_group is
             '任务组名';
             
             comment on column app_quartz_job.cron_expression is
             'cron表达式';
             
             comment on column app_quartz_job.bean_class is
             '任务执行时调用哪个类的方法:包名+类名';
             
             comment on column app_quartz_job.method_name is
             '任务执行时调用类的哪个方法';
             
             comment on column app_quartz_job.parameter is
             '参数';
             
             comment on column app_quartz_job.vm_param is
             'vm参数';
             
             comment on column app_quartz_job.jar_path is
             'jar路径';
             
             comment on column app_quartz_job.status is
             '状态:|0:CLOSE,1:OPEN';
             
             comment on column app_quartz_job.del_flag is
             '删除状态| 0:正常,1:已删除';
             
             comment on column app_quartz_job.remarks is
             '备注信息';
             
             comment on column app_quartz_job.create_by is
             '创建人';
             
             comment on column app_quartz_job.create_time is
             '创建时间';
             
             comment on column app_quartz_job.update_by is
             '修改人';
             
             comment on column app_quartz_job.update_time is
             '修改时间';
             
             delete from "public"."app_quartz_job" where id in('1','2','3','4','5');
             INSERT INTO "public"."app_quartz_job"("id", "name", "job_group", "cron_expression", "parameter", "remarks","vm_param","jar_path","status", "del_flag", "create_time", "create_by", "update_time", "update_by") VALUES ('1',  'first', 'helloworld', '0/2 * * * * ? ', '1', '第一个', '', null,  '0', '0', '2019-07-31 00:33:00', ' system/system', '2019-07-31 00:33:00', ' system/system');
             INSERT INTO "public"."app_quartz_job"("id", "name", "job_group", "cron_expression", "parameter", "remarks","vm_param","jar_path","status", "del_flag", "create_time", "create_by", "update_time", "update_by")  VALUES ('2',  'second', 'helloworld', '0/5 * * * * ? ', '2', '第二个', null, null,  '0', '0', '2019-07-31 00:33:00', ' system/system', '2019-07-31 00:33:00', ' system/system');
             INSERT INTO "public"."app_quartz_job"("id", "name", "job_group", "cron_expression", "parameter", "remarks","vm_param","jar_path","status", "del_flag", "create_time", "create_by", "update_time", "update_by")  VALUES ('3',  'third', 'helloworld', '0/15 * * * * ? ', '3', '第三个', null, null,  '0', '0', '2019-07-31 00:33:00', ' system/system', '2019-07-31 00:33:00', ' system/system');
             INSERT INTO "public"."app_quartz_job"("id", "name", "job_group", "cron_expression", "parameter", "remarks","vm_param","jar_path","status", "del_flag", "create_time", "create_by", "update_time", "update_by")  VALUES ('4',  'four', 'helloworld', '0 0/1 * * * ? *', '4', '第四个', null, null,  '0', '0', '2019-07-31 00:33:00', ' system/system', '2019-07-31 00:33:00', ' system/system');
             INSERT INTO "public"."app_quartz_job"("id", "name", "job_group", "cron_expression", "parameter", "remarks","vm_param","jar_path","status", "del_flag", "create_time", "create_by", "update_time", "update_by")  VALUES ('5',  'OLAY Job', 'Nomal', '0 0/2 * * * ?', '5', '第五个', null, 'C:EalenXieDownloadJDE-Order-1.0-SNAPSHOT.jar', '0', '0', '2019-07-31 00:33:00', ' system/system', '2019-07-31 00:33:00', ' system/system');
             
             commit;
             
             /*==============================================================*/
             /* Index: idx_qtz_job_group                                     */
             /*==============================================================*/
             create  index idx_qtz_job_group on app_quartz_job (
             job_group
             );
             
             /*==============================================================*/
             /* Index: Idx_qtz_job_name                                      */
             /*==============================================================*/
             create  index Idx_qtz_job_name on app_quartz_job (
             name
             );
             
             /*==============================================================*/
             /* Index: idx_qtz_job_ct                                        */
             /*==============================================================*/
             create  index idx_qtz_job_ct on app_quartz_job (
             create_time
             );
             
             /*==============================================================*/
             /* Table: app_quartz_log                                        */
             /*==============================================================*/
             create table app_quartz_log (
                id                   VARCHAR(64)          not null,
                job_id               VARCHAR(64)          null,
                status               CHAR(1)              null,
                del_flag             CHAR(1)              not null default '0'::bpchar,
                remarks              VARCHAR(500)         null,
                create_by            VARCHAR(32)          null,
                create_time          TIMESTAMP            null,
                update_by            VARCHAR(32)          null,
                update_time          TIMESTAMP            null,
                constraint PK_app_quartz_log primary key (id),
                constraint FK_APP_QUAR_REFERENCE_APP_QUAR foreign key (job_id)
                   references app_quartz_job (id)
                   on delete cascade on update cascade
             )
             without oids;
             
             comment on table app_quartz_log is
             'qrtz任务执行日志';
             
             comment on column app_quartz_log.job_id is
             '任务id';
             
             comment on column app_quartz_log.status is
             '状态:|0:执行失败,1:执行成功';
             
             comment on column app_quartz_log.del_flag is
             '删除状态| 0:正常,1:已删除';
             
             comment on column app_quartz_log.remarks is
             '备注信息';
             
             comment on column app_quartz_log.create_by is
             '创建人';
             
             comment on column app_quartz_log.create_time is
             '创建时间';
             
             comment on column app_quartz_log.update_by is
             '修改人';
             
             comment on column app_quartz_log.update_time is
             '修改时间';
             
             /*==============================================================*/
             /* Index: Idx_qtz_job_id                                        */
             /*==============================================================*/
             create  index Idx_qtz_job_id on app_quartz_log (
             job_id
             );
             
             /*==============================================================*/
             /* Index: idx_qtz_log_ct                                        */
             /*==============================================================*/
             create  index idx_qtz_log_ct on app_quartz_log (
             create_time
             );
             



#使用JPA
    使用JPA 取任务列表，注意同一个接口不混合使用JPA repository和mybatis plus的mapper
    引入spring jpa 依赖
    <!--spring data jpa-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>



#动态配置任务
  quartz的调度和任务存取api,一经过封装，后续，只需配置。
  具体参考: https://blog.csdn.net/xlgdst/article/details/79104273  


#cronExpression表达式
                   字段	 	允许值	 	允许的特殊字符
                   秒	 	0-59	 	, - * /
                   分	 	0-59	 	, - * /
                   小时	 	0-23	 	, - * /
                   日期	 	1-31	 	, - *   / L W C
                   月份	 	1-12 或者 JAN-DEC	 	, - * /
                   星期	 	1-7 或者 SUN-SAT	 	, - *   / L C #
                   年（可选）	 	留空, 1970-2099	 	, - * /
                   　 
                   如上面的表达式所示: 
                   
                   “*”字符被用来指定所有的值。如：”*“在分钟的字段域里表示“每分钟”。 
                   
                   “-”字符被用来指定一个范围。如：“10-12”在小时域意味着“10点、11点、12点”。
                    
                   “,”字符被用来指定另外的值。如：“MON,WED,FRI”在星期域里表示”星期一、星期三、星期五”. 
                   
                   “?”字符只在日期域和星期域中使用。它被用来指定“非明确的值”。当你需要通过在这两个域中的一个来指定一些东西的时候，它是有用的。看下面的例子你就会明白。 
                   
                   
                   “L”字符指定在月或者星期中的某天（最后一天）。即“Last ”的缩写。但是在星期和月中“Ｌ”表示不同的意思，如：在月子段中“L”指月份的最后一天-1月31日，2月28日，如果在星期字段中则简单的表示为“7”或者“SAT”。如果在星期字段中在某个value值得后面，则表示“某月的最后一个星期value”,如“6L”表示某月的最后一个星期五。
                   
                   “W”字符只能用在月份字段中，该字段指定了离指定日期最近的那个星期日。
                   
                   “#”字符只能用在星期字段，该字段指定了第几个星期value在某月中
                   
                   
                   
                   每一个元素都可以显式地规定一个值（如6），一个区间（如9-12），一个列表（如9，11，13）或一个通配符（如*）。“月份中的日期”和“星期中的日期”这两个元素是互斥的，因此应该通过设置一个问号（？）来表明你不想设置的那个字段。表7.1中显示了一些cron表达式的例子和它们的意义：
                   
                   表达式
                   
                    	意义
                   "0 0 12 * * ?"	 	每天中午12点触发
                   "0 15 10 ? * *"	 	每天上午10:15触发
                   "0 15 10 * * ?"	 	每天上午10:15触发
                   "0 15 10 * * ? *"	 	每天上午10:15触发
                   "0 15 10 * * ? 2005"	 	2005年的每天上午10:15触发
                   "0 * 14 * * ?"	 	在每天下午2点到下午2:59期间的每1分钟触发
                   "0 0/5 14 * * ?"	 	在每天下午2点到下午2:55期间的每5分钟触发
                   "0 0/5 14,18 * * ?"	 	在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
                   "0 0-5 14 * * ?"	 	在每天下午2点到下午2:05期间的每1分钟触发
                   "0 10,44 14 ? 3 WED"	 	每年三月的星期三的下午2:10和2:44触发
                   "0 15 10 ? * MON-FRI"	 	周一至周五的上午10:15触发
                   "0 15 10 15 * ?"	 	每月15日上午10:15触发
                   "0 15 10 L * ?"	 	每月最后一日的上午10:15触发
                   "0 15 10 ? * 6L"	 	每月的最后一个星期五上午10:15触发 
                   "0 15 10 ? * 6L 2002-2005"	 	2002年至2005年的每月的最后一个星期五上午10:15触发
                   "0 15 10 ? * 6#3"	 	每月的第三个星期五上午10:15触发

                   　 每天早上6点 0 6 * * * 
                      每两个小时 0 */2 * * * 
                      晚上11点到早上8点之间每两个小时，早上八点 0 23-7/2，8 * * * 
                      每个月的4号和每个礼拜的礼拜一到礼拜三的早上11点 0 11 4 * 1-3 
                      1月1日早上4点 0 4 1 1 *
   具体参考: https://www.cnblogs.com/lic309/p/4089633.html
 


#疑难问题定位
   定位问题，比如启动时报错，实例化不了哪个类 

   - 就要从这个类的上层类有没有实例化(加了@Component?/@Configure/@Bean)
   - 这是接口，看有没有实现类
   -  java.lang.ArrayStoreException
      这个问题不明确但是通过报错明细，打断点调试，是可以找出问题的:quartz使用spring boot quartz 2.x,是可以自动配置，实例化的，不需要config类，现在报这个错，我直接进入报错的AutoConfigueDepency相关的类，发现报红，原来是依赖 liquibase,于是导入 liquibase 依赖包:
      <!--quartz的autoconfigure自动配置需要liquibase-->
        <dependency>
            <groupId>org.liquibase</groupId>
            <artifactId>liquibase-core</artifactId>
            <version>${liquibase-core.version}</version>
        </dependency>
       这个 liquibase,实际上是一个类似于flyway的数据库版本管理插件，这里我们不用，只是为了跳过报错。但是，还是报错,需要
       db/liquibase/master.yam
       是可以从官网上下，其实也是一个空文档，就直接在application.yml中配置关闭liquibase:
         
         liquibase:
           enabled: false

       有关liquibase,可参考: http://www.tianshouzhi.com/api/tutorials/springboot/366
                            https://www.cnblogs.com/itrena/p/5927145.html
       最好完美解决所有问题。
       定位问题的方法，可参考: http://ju.outofmemory.cn/entry/346650


[downloadScriptBase64]:
data:image/png;base64,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



