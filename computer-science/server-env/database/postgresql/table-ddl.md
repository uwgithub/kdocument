#table ddl                                                                   
##contents 
- [删除表](#删除表) 
- [重命名表](#重命名表)
- [删除索引](#删除索引) 
- [列修改](#列修改)                                                                
- [约束修改](#约束修改) 
- [异常错误码](#异常错误码) 


#删除表
drop table if exists tag_category_teacher CASCADE;

#重命名表
  DO $$
    BEGIN
        BEGIN
            alter table 表名 rename to 新表名
        EXCEPTION
            WHEN undefined_table THEN RAISE NOTICE 'table xx not exists.';
        END;
    END;
  $$;

#删除索引
drop index if exists ind_tag_context_course;

#列修改
##增加列
ALTER TABLE app_quartz_job ADD COLUMN IF NOT EXISTS jar_path  VARCHAR(255)  null;

##删除列
ALTER TABLE app_post DROP COLUMN IF EXISTS  external_title;

##修改列类型
  DO $$
    BEGIN
        BEGIN
            alter table app_quartz_job alter COLUMN remarks type VARCHAR(500);
            alter table goods alter column sid type character varying;
        EXCEPTION
            WHEN undefined_column THEN RAISE NOTICE 'column remarks not exists in app_quartz_job.';
        END;
    END;
  $$;


##修改列名
  DO $$
    BEGIN
        BEGIN
            alter table app_quartz_job rename column remarks to remarks;
            alter table goods rename column sid to ssid;
        EXCEPTION
            WHEN undefined_column THEN RAISE NOTICE 'column remarks not exists in app_quartz_job.';
        END;
    END;
  $$;

##删除列默认值
alter table goods  alter column sid drop default;


#约束修改
##添加主键
alter table cities add PRIMARY KEY(name);


##添加外键
--讲师表增加外键
DO $$
    BEGIN
        BEGIN
            alter table tenant_teacher add constraint FK_TENANT_TEACHER_REF_LEVEL foreign key (level_id) references tenant_teacher_level (id)
               on delete cascade on update cascade;
            alter table tenant_teacher add constraint FK_TENANT_TEACHER_REF_CATEGORY foreign key (category_id) references tenant_teacher_category (id)
               on delete cascade on update cascade;
        EXCEPTION
            WHEN undefined_table THEN RAISE NOTICE 'table tenant_teacher not exists.';
        END;
    END;
$$;

on update cascade: 被引用行更新时，引用行自动更新； 
on update restrict: 被引用的行禁止更新；
on delete cascade: 被引用行删除时，引用行也一起删除；
on dellete restrict: 被引用的行禁止删除；

##删除外键
alter table orders drop constraint orders_goods_id_fkey;

##添加唯一约束
alter table goods add constraint unique_goods_sid unique(sid);





#异常错误码
   几个常见错误码,异常处理时使用:
   undefined_column : 列没有定义,列不存在
   duplicate_column : 列重复，已经存在
   
  
   详见: https://www.yiibai.com/manual/postgresql/errcodes-appendix.html
  PostgreSQL 错误代码
错误代码	含义	常量
类 00 — 成功完成
00000	成功完成
(SUCCESSFUL COMPLETION)	successful_completion
类 01 — 警告
01000	警告
(WARNING)	warning
0100C	返回了动态结果
(DYNAMIC RESULT SETS RETURNED)	dynamic_result_sets_returned
01008	警告，隐含补齐了零比特位
(IMPLICIT ZERO BIT PADDING)	implicit_zero_bit_padding
01003	在集合函数里消除了 NULL
(NULL VALUE ELIMINATED IN SET FUNCTION)	null_value_eliminated_in_set_function
01007	没有赋予权限
(PRIVILEGE NOT GRANTED)	privilege_not_granted
01006	没有撤销权限
(PRIVILEGE NOT REVOKED)	privilege_not_revoked
01004	字符串数据在右端截断
(STRING DATA RIGHT TRUNCATION)	string_data_right_truncation
01P01	废弃的特性
(DEPRECATED FEATURE)	deprecated_feature
类 02 — 没有数据(按照 SQL 标准的要求，这也是警告类)
02000	没有数据
(NO DATA)	no_data
02001	返回了没有附加动态结果集
(NO ADDITIONAL DYNAMIC RESULT SETS RETURNED)	no_additional_dynamic_result_sets_returned
类 03 — SQL 语句尚未结束
03000	SQL 语句尚未结束
(SQL STATEMENT NOT YET COMPLETE)	sql_statement_not_yet_complete
类 08 — 连接异常
08000	连接异常
(CONNECTION EXCEPTION)	connection_exception
08003	连接不存在
(CONNECTION DOES NOT EXIST)	connection_does_not_exist
08006	连接失败
(CONNECTION FAILURE)	connection_failure
08001	SQL 客户端不能建立 SQL 连接
(SQLCLIENT UNABLE TO ESTABLISH SQLCONNECTION)	sqlclient_unable_to_establish_sqlconnection
08004	SQL 服务器拒绝建立 SQL 连接
(SQLSERVER REJECTED ESTABLISHMENT OF SQLCONNECTION)	sqlserver_rejected_establishment_of_sqlconnection
08007	未知的事务解析
(TRANSACTION RESOLUTION UNKNOWN)	transaction_resolution_unknown
08P01	违反协议
(PROTOCOL VIOLATION)	protocol_violation
类 09 — 触发器动作异常
09000	触发器动作异常
(TRIGGERED ACTION EXCEPTION)	triggered_action_exception
类 0A — 不支持特性
0A000	不支持此特性
(FEATURE NOT SUPPORTED)	feature_not_supported
类 0B — 非法事务初始化
0B000	非法事务初始化
(INVALID TRANSACTION INITIATION)	invalid_transaction_initiation
类 0F — 定位器异常
0F000	定位器异常
(LOCATOR EXCEPTION)	locator_exception
0F001	非法的定位器声明
(INVALID LOCATOR SPECIFICATION)	invalid_locator_specification
类 0L — 非法赋权人
0L000	非法赋权人
(INVALID GRANTOR)	invalid_grantor
0LP01	非法赋权操作
(INVALID GRANT OPERATION)	invalid_grant_operation
类 0P — 非法角色声明
0P000	非法角色声明
(INVALID ROLE SPECIFICATION)	invalid_role_specification
类 21 — 势违例
21000	势违例
(CARDINALITY VIOLATION)	cardinality_violation
类 22 — 数据异常
22000	数据异常
(DATA EXCEPTION)	data_exception
2202E	数组下标错误
(ARRAY SUBSCRIPT ERROR)	array_subscript_error
22021	字符不在准备好的范围内
(CHARACTER NOT IN REPERTOIRE)	character_not_in_repertoire
22008	日期时间字段溢出
(DATETIME FIELD OVERFLOW)	datetime_field_overflow
22012	被零除
(DIVISION BY ZERO)	division_by_zero
22005	赋值中出错
(ERROR IN ASSIGNMENT)	error_in_assignment
2200B	逃逸字符冲突
(ESCAPE CHARACTER CONFLICT)	escape_character_conflict
22022	指示器溢出
(INDICATOR OVERFLOW)	indicator_overflow
22015	内部字段溢出
(INTERVAL FIELD OVERFLOW)	interval_field_overflow
2201E	对数运算的非法参数
(INVALID ARGUMENT FOR LOGARITHM)	invalid_argument_for_logarithm
2201F	指数函数的非法参数
(INVALID ARGUMENT FOR POWER FUNCTION)	invalid_argument_for_power_function
2201G	宽桶函数的非法参数
(INVALID ARGUMENT FOR WIDTH BUCKET FUNCTION)	invalid_argument_for_width_bucket_function
22018	类型转换时非法的字符值
(INVALID CHARACTER VALUE FOR CAST)	invalid_character_value_for_cast
22007	非法日期时间格式
(INVALID DATETIME FORMAT)	invalid_datetime_format
22019	非法的逃逸字符
(INVALID ESCAPE CHARACTER)	invalid_escape_character
2200D	非法的逃逸字节
(INVALID ESCAPE OCTET)	invalid_escape_octet
22025	非法逃逸序列
(INVALID ESCAPE SEQUENCE)	invalid_escape_sequence
22P06	非标准使用逃逸字符
(NONSTANDARD USE OF ESCAPE CHARACTER)	nonstandard_use_of_escape_character
22010	非法指示器参数值
(INVALID INDICATOR PARAMETER VALUE)	invalid_indicator_parameter_value
22020	非法限制值
(INVALID LIMIT VALUE)	invalid_limit_value
22023	非法参数值
(INVALID PARAMETER VALUE)	invalid_parameter_value
2201B	非法正则表达式
(INVALID REGULAR EXPRESSION)	invalid_regular_expression
22009	非法时区显示值
(INVALID TIME ZONE DISPLACEMENT VALUE)	invalid_time_zone_displacement_value
2200C	非法使用逃逸字符
(INVALID USE OF ESCAPE CHARACTER)	invalid_use_of_escape_character
2200G	最相关类型不匹配
(MOST SPECIFIC TYPE MISMATCH)	most_specific_type_mismatch
22004	不允许 NULL 值
(NULL VALUE NOT ALLOWED)	null_value_not_allowed
22002	NULL 值不能做指示器参数
(NULL VALUE NO INDICATOR PARAMETER)	null_value_no_indicator_parameter
22003	数字值超出范围
(NUMERIC VALUE OUT OF RANGE)	numeric_value_out_of_range
22026	字符串数据长度不匹配
(STRING DATA LENGTH MISMATCH)	string_data_length_mismatch
22001	字符串数据右边被截断
(STRING DATA RIGHT TRUNCATION)	string_data_right_truncation
22011	抽取子字符串错误
(SUBSTRING ERROR)	substring_error
22027	截断错误
(TRIM ERROR)	trim_error
22024	未结束的 C 字符串
(UNTERMINATED C STRING)	unterminated_c_string
2200F	零长度的字符串
(ZERO LENGTH CHARACTER STRING)	zero_length_character_string
22P01	浮点异常
(FLOATING POINT EXCEPTION)	floating_point_exception
22P02	非法文本表现形式
(INVALID TEXT REPRESENTATION)	invalid_text_representation
22P03	非法二进制表现形式
(INVALID BINARY REPRESENTATION)	invalid_binary_representation
22P04	错误的 COPY 格式
(BAD COPY FILE FORMAT)	bad_copy_file_format
22P05	不可翻译字符
(UNTRANSLATABLE CHARACTER)	untranslatable_character
类 23 — 违反完整性约束
23000	违反完整性约束
(INTEGRITY CONSTRAINT VIOLATION)	integrity_constraint_violation
23001	违反限制
(RESTRICT VIOLATION)	restrict_violation
23502	违反非空
(NOT NULL VIOLATION)	not_null_violation
23503	违反外键约束
(FOREIGN KEY VIOLATION)	foreign_key_violation
23505	违反唯一约束
(UNIQUE VIOLATION)	unique_violation
23514	违反检查
(CHECK VIOLATION)	check_violation
类 24 — 非法游标状态
24000	非法游标状态
(INVALID CURSOR STATE)	invalid_cursor_state
类 25 — 非法事务状态
25000	非法事务状态
(INVALID TRANSACTION STATE)	invalid_transaction_state
25001	活跃的 SQL 状态
(ACTIVE SQL TRANSACTION)	active_sql_transaction
25002	分支事务已经激活
(BRANCH TRANSACTION ALREADY ACTIVE)	branch_transaction_already_active
25008	持有的游标要求同样的隔离级别
(HELD CURSOR REQUIRES SAME ISOLATION LEVEL)	held_cursor_requires_same_isolation_level
25003	对分支事务的不恰当的访问方式
(INAPPROPRIATE ACCESS MODE FOR BRANCH TRANSACTION)	inappropriate_access_mode_for_branch_transaction
25004	对分支事务的不恰当的隔离级别
(INAPPROPRIATE ISOLATION LEVEL FOR BRANCH TRANSACTION)	inappropriate_isolation_level_for_branch_transaction
25005	分支事务没有活跃的 SQL 事务
(NO ACTIVE SQL TRANSACTION FOR BRANCH TRANSACTION)	no_active_sql_transaction_for_branch_transaction
25006	只读的 SQL 事务
(READ ONLY SQL TRANSACTION)	read_only_sql_transaction
25007	不支持混和的模式和数据语句
(SCHEMA AND DATA STATEMENT MIXING NOT SUPPORTED)	schema_and_data_statement_mixing_not_supported
25P01	没有活跃的 SQL 事务
(NO ACTIVE SQL TRANSACTION)	no_active_sql_transaction
25P02	在失败的 SQL 事务中
(IN FAILED SQL TRANSACTION)	in_failed_sql_transaction
类 26 — 非法 SQL 语句名
26000	非法 SQL 语句名
(INVALID SQL STATEMENT NAME)	invalid_sql_statement_name
类 27 — 触发的数据改变违规
27000	触发的数据改变违规
(TRIGGERED DATA CHANGE VIOLATION)	triggered_data_change_violation
类 28 — 非法授权声明
28000	非法授权声明
(INVALID AUTHORIZATION SPECIFICATION)	invalid_authorization_specification
类 2B — 依然存在依赖的优先级描述符
2B000	依然存在依赖的优先级描述符
(DEPENDENT PRIVILEGE DESCRIPTORS STILL EXIST)	dependent_privilege_descriptors_still_exist
2BP01	依赖性对象仍然存在
(DEPENDENT OBJECTS STILL EXIST)	dependent_objects_still_exist
类 2D — 非法的事务终止
2D000	非法的事务终止
(INVALID TRANSACTION TERMINATION)	invalid_transaction_termination
类 2F — SQL 过程异常
2F000	SQL 过程异常
(SQL ROUTINE EXCEPTION)	sql_routine_exception
2F005	执行的函数没有返回语句
(FUNCTION EXECUTED NO RETURN STATEMENT)	function_executed_no_return_statement
2F002	不允许修改 SQL 数据
(MODIFYING SQL DATA NOT PERMITTED)	modifying_sql_data_not_permitted
2F003	企图使用禁止的 SQL 语句
(PROHIBITED SQL STATEMENT ATTEMPTED)	prohibited_sql_statement_attempted
2F004	不允许读取 SQL 数据
(READING SQL DATA NOT PERMITTED)	reading_sql_data_not_permitted
类 34 — 非法游标名
34000	非法游标名
(INVALID CURSOR NAME)	invalid_cursor_name
类 38 — 外部过程异常
38000	外部过程异常
(EXTERNAL ROUTINE EXCEPTION)	external_routine_exception
38001	不允许包含的 SQL
(CONTAINING SQL NOT PERMITTED)	containing_sql_not_permitted
38002	不允许修改 SQL 数据
(MODIFYING SQL DATA NOT PERMITTED)	modifying_sql_data_not_permitted
38003	企图使用禁止的 SQL 语句
(PROHIBITED SQL STATEMENT ATTEMPTED)	prohibited_sql_statement_attempted
38004	不允许读取 SQL 数据
(READING SQL DATA NOT PERMITTED)	reading_sql_data_not_permitted
类 39 — 外部过程调用异常
39000	外部过程调用异常
(EXTERNAL ROUTINE INVOCATION EXCEPTION)	external_routine_invocation_exception
39001	返回了非法的 SQLSTATE
(INVALID SQLSTATE RETURNED)	invalid_sqlstate_returned
39004	不允许 NULL
(NULL VALUE NOT ALLOWED)	null_value_not_allowed
39P01	违反触发器协议
(TRIGGER PROTOCOL VIOLATED)	trigger_protocol_violated
39P02	违反 SRF 协议
(SRF PROTOCOL VIOLATED)	srf_protocol_violated
类 3B — 保存点异常
3B000	保存点异常
(SAVEPOINT EXCEPTION)	savepoint_exception
3B001	无效的保存点声明
(INVALID SAVEPOINT SPECIFICATION)	invalid_savepoint_specification
类 3D — 非法数据库名
3D000	非法数据库名
(INVALID CATALOG NAME)	invalid_catalog_name
类 3F — 非法模式名
3F000	非法模式名
(INVALID SCHEMA NAME)	invalid_schema_name
类 40 — 事务回滚
40000	事务回滚
(TRANSACTION ROLLBACK)	transaction_rollback
40002	违反事务完整性约束
(TRANSACTION INTEGRITY CONSTRAINT VIOLATION)	transaction_integrity_constraint_violation
40001	串行化失败
(SERIALIZATION FAILURE)	serialization_failure
40003	不知道语句是否结束
(STATEMENT COMPLETION UNKNOWN)	statement_completion_unknown
40P01	侦测到死锁
(DEADLOCK DETECTED)	deadlock_detected
类 42 — 语法错误或者违反访问规则
42000	语法错误或者违反访问规则
(SYNTAX ERROR OR ACCESS RULE VIOLATION)	syntax_error_or_access_rule_violation
42601	语法错误
(SYNTAX ERROR)	syntax_error
42501	权限不够
(INSUFFICIENT PRIVILEGE)	insufficient_privilege
42846	无法进行类型转换
(CANNOT COERCE)	cannot_coerce
42803	分组错误
(GROUPING ERROR)	grouping_error
42830	非法的外键
(INVALID FOREIGN KEY)	invalid_foreign_key
42602	非法名字
(INVALID NAME)	invalid_name
42622	名字太长
(NAME TOO LONG)	name_too_long
42939	保留名字
(RESERVED NAME)	reserved_name
42804	数据类型不匹配
(DATATYPE MISMATCH)	datatype_mismatch
42P18	未决的数据类型
(INDETERMINATE DATATYPE)	indeterminate_datatype
42809	错误的对象类型
(WRONG OBJECT TYPE)	wrong_object_type
42703	未定义的字段
(UNDEFINED COLUMN)	undefined_column
42883	未定义的函数
(UNDEFINED FUNCTION)	undefined_function
42P01	未定义的表
(UNDEFINED TABLE)	undefined_table
42P02	未定义的参数
(UNDEFINED PARAMETER)	undefined_parameter
42704	未定义对象
(UNDEFINED OBJECT)	undefined_object
42701	重复的字段
(DUPLICATE COLUMN)	duplicate_column
42P03	重复的游标
(DUPLICATE CURSOR)	duplicate_cursor
42P04	重复的数据库
(DUPLICATE DATABASE)	duplicate_database
42723	重复的函数
(DUPLICATE FUNCTION)	duplicate_function
42P05	重复的预备语句
(DUPLICATE PREPARED STATEMENT)	duplicate_prepared_statement
42P06	重复的模式
(DUPLICATE SCHEMA)	duplicate_schema
42P07	重复的表
(DUPLICATE TABLE)	duplicate_table
42712	重复的别名
(DUPLICATE ALIAS)	duplicate_alias
42710	重复的对象
(DUPLICATE OBJECT)	duplicate_object
42702	模糊的字段
(AMBIGUOUS COLUMN)	ambiguous_column
42725	模糊的函数
(AMBIGUOUS FUNCTION)	ambiguous_function
42P08	模糊的参数
(AMBIGUOUS PARAMETER)	ambiguous_parameter
42P09	模糊的别名
(AMBIGUOUS ALIAS)	ambiguous_alias
42P10	非法字段引用
(INVALID COLUMN REFERENCE)	invalid_column_reference
42611	非法字段定义
(INVALID COLUMN DEFINITION)	invalid_column_definition
42P11	非法游标定义
(INVALID CURSOR DEFINITION)	invalid_cursor_definition
42P12	非法数据库定义
(INVALID DATABASE DEFINITION)	invalid_database_definition
42P13	非法函数定义
(INVALID FUNCTION DEFINITION)	invalid_function_definition
42P14	非法预备语句定义
(INVALID PREPARED STATEMENT DEFINITION)	invalid_prepared_statement_definition
42P15	非法模式定义
(INVALID SCHEMA DEFINITION)	invalid_schema_definition
42P16	非法表定义
(INVALID TABLE DEFINITION)	invalid_table_definition
42P17	非法对象定义
(INVALID OBJECT DEFINITION)	invalid_object_definition
类 44 — 违反 WITH CHECK 选项
44000	违反 WITH CHECK 选项
(WITH CHECK OPTION VIOLATION)	with_check_option_violation
类 53 — 资源不够
53000	资源不够
(INSUFFICIENT RESOURCES)	insufficient_resources
53100	磁盘满
(DISK FULL)	disk_full
53200	内存耗尽
(OUT OF MEMORY)	out_of_memory
53300	太多连接
(TOO MANY CONNECTIONS)	too_many_connections
类 54 — 超过程序限制
54000	超过程序限制
(PROGRAM LIMIT EXCEEDED)	program_limit_exceeded
54001	语句太复杂
(STATEMENT TOO COMPLEX)	statement_too_complex
54011	字段太多
(TOO MANY COLUMNS)	too_many_columns
54023	参数太多
(TOO MANY ARGUMENTS)	too_many_arguments
类 55 — 对象不在预先要求的状态
55000	对象不在预先要求的状态
(OBJECT NOT IN PREREQUISITE STATE)	object_not_in_prerequisite_state
55006	对象在使用中
(OBJECT IN USE)	object_in_use
55P02	无法修改运行时参数
(CANT CHANGE RUNTIME PARAM)	cant_change_runtime_param
55P03	锁不可获得
(LOCK NOT AVAILABLE)	lock_not_available
类 57 — 操作者干涉
57000	操作者干涉
(OPERATOR INTERVENTION)	operator_intervention
57014	查询被取消
(QUERY CANCELED)	query_canceled
57P01	管理员关机
(ADMIN SHUTDOWN)	admin_shutdown
57P02	崩溃关机
(CRASH SHUTDOWN)	crash_shutdown
57P03	现在无法连接
(CANNOT CONNECT NOW)	cannot_connect_now
类 58 — 系统错误(PostgreSQL 自己内部的错误)
58030	IO 错误
(IO ERROR)	io_error
58P01	未定义的文件
(UNDEFINED FILE)	undefined_file
58P02	重复的文件
(DUPLICATE FILE)	duplicate_file
类 F0 — 配置文件错误
F0000	配置文件错误
(CONFIG FILE ERROR)	config_file_error
F0001	锁文件存在
(LOCK FILE EXISTS)	lock_file_exists
类 P0 — PL/pgSQL 错误
P0000	PLPGSQL 错误
(PLPGSQL ERROR)	plpgsql_error
P0001	抛出异常
(RAISE EXCEPTION)	raise_exception
P0002	未找到数据
(NO DATA FOUND)	no_data_found
P0003	行太多
(TOO MANY ROWS)	too_many_rows
类 XX — 内部错误
XX000	内部错误
(INTERNAL ERROR)	internal_error
XX001	数据损坏
(DATA CORRUPTED)	data_corrupted
XX002	索引损坏
(INDEX CORRUPTED)	index_corrupted

