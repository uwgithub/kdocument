#mybatisplusSQL                                                                  
##contents                                                                
- [比较#和$](#比较#和$) 
- [使用#和$](#使用#和$) 
- [statementType](#statementType) 
- [存储过程](#存储过程)



#比较#和$
  符号'#' :  预编译,替换为占位符?,添加引号,防止sql注入
  符号'$' :  非预编译,原本替换,不添加引号,可能导致sql注入


#使用#和$
  要实现动态调用表名和字段名，就不能使用预编译了，需添加statementType="STATEMENT"",用$
      
    <select id="getUser" resultType="java.util.Map" parameterType="java.lang.String" statementType="STATEMENT">
    select 
    ${columns}
    from ${tableName}
    where COMPANY_REMARK = ${company}
    </select>


  如${xxx}传入的参数为字符串数据，需在参数传入前加上引号，实现#一样的效果,如：

        String name = "sprite";
        name = "'" + name + "'";
         

#statementType
   statementType：STATEMENT（非预编译），PREPARED（预编译）或CALLABLE中的任意一个，这就告诉 MyBatis 分别使用Statement，PreparedStatement或者CallableStatement。默认：PREPARED。这里显然不能使用预编译，要改成非预编译。


#存储过程
   
  具体参考: https://www.cnblogs.com/jiaoyiping/p/4063805.html
##  1.调用没有OUT参数的存储过程：

  创建存储过程：

      create or replace function get_code(a1 varchar(32)) returns varchar(32) as $$
    　　　　　　declare the_result varchar(32);
    　　　　　　begin
    　　　　　　the_result := name from t_project where id = a1;
    　　　　　　　 return the_result;
      　　　　　　　　end;
    　　　　　　$$
    　　　　   language plpgsql;

   sqlMap配置文件：

    <select id="f1" resultType="String"  parameterType="map" statementType="CALLABLE" useCache="false">
          <![CDATA[
           select get_code(
              #{a1,mode=IN,jdbcType=VARCHAR}
              )    
       ]]>
    </select>

   注：不使用OUT参数的存储过程可以直接用 select

   程序：

    public String generateCode(String a1) {
    Map<String,String> paramMap = new HashMap<String,String>();
    paramMap.put("a1", a1);
    SqlSession sqlSession = getSqlSession();
    String result = sqlSession.selectOne("f1", paramMap);
    return result;
    }

 

##2.使用OUT参数的存储过程：

创建存储过程：

    create or replace function testproc(a1 varchar(32),out a2 varchar(32),out a3 varchar(32)) as $$
      declare   
     begin
            select id  into a2 from t_project where id=a1;
            select name into a3 from t_project where id=a1;
      return;
    end;
    $$
    language plpgsql;


sqlMap配置文件：

     <select id="generateCode1"  parameterType="map" statementType="CALLABLE" useCache="false">
          {
              call testproc(
              #{a1,mode=IN,jdbcType=VARCHAR},
              #{a2,mode=OUT,jdbcType=VARCHAR},
              #{a3,mode=OUT,jdbcType=VARCHAR}
              
           )    
       }
    </select> 

    程序：

    public Map generateCode1(String a1) {
        Map<String,String> paramMap = new HashMap<String,String>();
        paramMap.put("a1", k1);
        SqlSession sqlSession = getSqlSession();
        sqlSession.selectOne("generateCode1", paramMap);
        return paramMap;
    }

   带输出参数的存储过程，sqlSession.selectOne("generateCode1", paramMap); 
   将paramMap传入之后mybatis调用存储过程，将paramMap进行填充 
   paramMap最后的值：{a1=R20148800900, a2=R20148800900, a3=项目名称}
