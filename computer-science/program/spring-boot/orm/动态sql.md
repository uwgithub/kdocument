#动态sql                                                                   
##contents                                                                
- [实体map等同性](#实体map等同性) 



#实体map等同性
##include拼接
  https://www.cnblogs.com/loaderman/p/10064477.html
  直接取实体的属性名
  
      <sql id="key">
    <trim suffixOverrides=",">
    <if test="id!=null">
    id,
    </if>
    <if test="name!=null">
    name,
    </if>
    <if test="sal!=null">
    sal,
    </if>
    </trim>
    </sql>
    <sql id="value">
    <trim suffixOverrides=",">
    <if test="id!=null">
    #{id},
    </if>
    <if test="name!=null">
    #{name},
    </if>
    <if test="sal!=null">
    #{sal},
    </if>
    </trim>
    </sql>
    <insert id="dynaSQLwithInsert" parameterType="loaderman.Student">
    insert into students(<include refid="key"/>) values(<include refid="value"/>)
    </insert>

    
        
    /**
     * 持久层*/
    public class StudentDao {
    /**
     * 动态SQL--插入
     */
    public void dynaSQLwithInsert(Student student) throws Exception{
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    try{
    sqlSession.insert("mynamespace.dynaSQLwithInsert",student);
    }catch(Exception e){
    e.printStackTrace();
    sqlSession.rollback();
    throw e;
    }finally{
    sqlSession.commit();
    MyBatisUtil.closeSqlSession();
    }
    }
    public static void main(String[] args) throws Exception{
    StudentDao dao = new StudentDao();
    dao.dynaSQLwithInsert(new Student(1,"哈哈",7000D));
    dao.dynaSQLwithInsert(new Student(2,"哈哈",null));
    dao.dynaSQLwithInsert(new Student(3,null,7000D));
    }
    }

##prefix,suffix,suffixOverrides
  https://blog.csdn.net/huangbaokang/article/details/78049041
   
       <insert id="insertSelective" parameterType="com.bootdo.system.domain.LogDO">
    insert into sys_log
    <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="id != null">
    id,
      </if>
      <if test="userId != null">
    user_id,
      </if>
      <if test="username != null">
    username,
      </if>
      <if test="operation != null">
    operation,
      </if>
      <if test="time != null">
    time,
      </if>
      <if test="method != null">
    method,
      </if>
      <if test="params != null">
    params,
      </if>
      <if test="ip != null">
    ip,
      </if>
      <if test="gmtCreate != null">
    gmt_create,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="id != null">
    #{id,jdbcType=BIGINT},
      </if>
      <if test="userId != null">
    #{userId,jdbcType=BIGINT},
      </if>
      <if test="username != null">
    #{username,jdbcType=VARCHAR},
      </if>
      <if test="operation != null">
    #{operation,jdbcType=VARCHAR},
      </if>
      <if test="time != null">
    #{time,jdbcType=INTEGER},
      </if>
      <if test="method != null">
    #{method,jdbcType=VARCHAR},
      </if>
      <if test="params != null">
    #{params,jdbcType=VARCHAR},
      </if>
      <if test="ip != null">
    #{ip,jdbcType=VARCHAR},
      </if>
      <if test="gmtCreate != null">
    #{gmtCreate,jdbcType=TIMESTAMP},
      </if>
    </trim>
      </insert>


##_parameter.containsKey
    https://blog.csdn.net/xl_1803/article/details/83472576
    if标签判断参数map中是否存在某个key
 
   <!-- 动态更新-->
   	<update id="updateBook" parameterType="map">
   		update book
   		
   		<trim prefix="set" suffixOverrides=",">
   			<if test="_parameter.containsKey('bookName')">
   				book_name=#{bookName},
   			</if>
   			<if test="_parameter.containsKey('bookPrice')">
   				book_price=#{bookPrice},
   			</if>
   			<if test="_parameter.containsKey('bookPage')">
   				book_page=#{bookPage}
   			</if>
   		</trim>
   		where id=#{id}
   	</update>



##mybatis如何遍历Map的key和value
     https://blog.csdn.net/baidu_37366055/article/details/81029026

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <!--namespace必须是接口的全类名 -->
    <mapper namespace="com.genius">
    <!-- 1.0 查询表结构是否存在 -->
    <select id="selectOne" parameterType="java.util.HashMap"
    resultType="java.util.HashMap">
    select count(*) as num from ${tableName} where seq =
    #{seq};
    </select>
    <!-- 1.1 插入一条数据 -->
    <insert id="insertOne" parameterType="java.util.Map">
    insert into ${tableName}
    <foreach collection="content.keys" item="key" open="(" close=")"
    separator=",">
    ${key}
    </foreach>
    values
    <foreach collection="content.values" item="value" open="("
    close=")" separator=",">
    #{value}
    </foreach>
    </insert>
    <!-- 1.2 更新记录 -->
    <update id="updateOne" parameterType="java.util.Map">
    UPDATE ${tableName} SET
    <foreach collection="content.keys" item="key" open="" close=""
    separator=",">
    ${key} = #{content[${key}]}
    </foreach>
    where seq = #{content[seq]} and genius_uid <=
    #{content[genius_uid]};
    </update>
    <!-- 1.3 删除无效数据 -->
    <delete id="deleteOne" parameterType="java.util.Map">
    delete from ${tableName}
    where seq = #{content[seq]};
    </delete>
    </mapper>
    2.java代码：
    SqlSession session = MyBatisConnectionFactory.getSession("pg");
    HashMap<String, Object> params = new HashMap<>(); //传入的参数
    params.put("content", tableContent);
    params.put("tableName", tableName);
    params.put("seq", seq);
    int flag = session.delete("deleteOne", params); //删除记录
    HashMap<String, Object> map = session.selectOne("selectOne", params); //查询记录是否存在
    flag = session.update("updateOne", params) > 0 ? true : false; //更新
    flag = session.insert("insertOne", params) > 0 ? true : false; //新增
 

##循环map
    foreach collection=xx.keys/values
    ${key} / ${value}
    xxmap[${key}] = ${value}
     https://blog.csdn.net/qq_41396619/article/details/83828360

     <foreach>标签的用法：

     六个参数：

     collection：要循环的集合

    index：循环索引（不知道啥用。。）

    item：集合中的一个元素（item和collection，按foreach循环理解）

    open：以什么开始

    close：以什么结束

    separator：循环内容之间以什么分隔
 
     
     用这个能取值:
     <foreach collection="condition.keys" item="k" separator="and">   
        <if test="null != condition[k]">    
              ${k} = #{condition[${k}]}  
        </if>  
    </foreach>   

     错误取法,null:
     <foreach collection="condition.keys" item="k" separator="and">   
         <if test="null != condition[k]">    
            ${k} = #{condition[k]}    
         </if>  
    </foreach>   

     
    循环key：  https://blog.csdn.net/zengmingen/article/details/51919279
              https://blog.csdn.net/linminqin/article/details/39154133#

    <foreach collection="condition.keys" item="k" separator="and"> 
    	${k} = #{k}  
    </foreach> 
    
    循环values
    <foreach collection="condition.values" item="v" separator="and"> 
    	${v} = #{v}  
    </foreach> 
    
    循环获取key和值：
    <foreach collection="condition.keys" item="k" separator="and"> 
    <if test="null != condition[k]">  
    		${k} = ${condition[k]}  
    </if>
    </foreach> 
    
    通常我们设置值的时候，会以#{}的方式，而不是${}，如下
    <foreach collection="condition.keys" item="k" separator="and"> 
    <if test="null != condition[k]">  
    		${k} = #{condition[k]}  
    </if>
    </foreach> 
    
    但是用这种方式，会发现，取不了值了，${condition[k]}  能取的出值，但#{condition[k]} 取出来的值却实null，正确的写法应该是：
    <foreach collection="condition.keys" item="k" separator="and"> 
    <if test="null != condition[k]">  
    		${k} = #{condition[${k}]}
    </if>
    </foreach> 

##循环list<map>
   https://www.cnblogs.com/1-Admin/p/8018773.html
    
     
         源数据
    　　maps.put("str", str);
    　　list.add(maps);
    　　List<Map> mapTest = userService.mapTest1(list);
    　　System.out.println(mapTest);
    <foreach item="items" index="index" collection="list" open="("  separator="," close=")"> -->
      <foreach item="item" index="index" collection="items.str" open="("  separator=","  close=")"   >
    #{item}
      </foreach>
       </foreach>



   