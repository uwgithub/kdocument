#完全案例                                                                   
##contents                                                                
- [源于项目](#源于项目) 
- [调用函式](#调用函式) 



#源于项目
   从实际项目中来的，掌握它，理解它，就是提升项目开发能力。我们看之，想之，就会之。沉淀是框架的力量。


#调用函式
  调用函数，存储过程，唯有参数不同，就书写一统一模式，供通用调用.
  

##service中组装参数(调用方式)
  
       /**
    	 * 按月创建日志表
    	 * @return
    	 */
    	@Override
    	public String createLogTableBy(){
    		Map<String,FunctionModel> paraMap = new LinkedHashMap<String,FunctionModel>();
    		paraMap.put(BaseDatabaseConstant.KEY_FUN_NAME,new FunctionModel().setValue(BaseDatabaseConstant.FUNCTION_CREATE_PARTITION_TABLE));//创建日志月表的存储过程名
    		paraMap.put(BaseDatabaseConstant.KEY_OLD_TABLE,new FunctionModel().setValue(BaseDatabaseConstant.TABLE_APP_LOG));//日志原表(不带月份)
    		paraMap.put(BaseDatabaseConstant.KEY_P_PRIMARY, new FunctionModel().setValue(DataBaseConstant.COLUMN_ID));//主键列id
    		paraMap.put(BaseDatabaseConstant.KEY_P_INDEX, new FunctionModel().setValue(DataBaseConstant.CREATE_TIME_TABLE));//索引列创建时间
    		return appLogMapper.callinFunction(paraMap);
    	}  

        
   说明:  
          
        1.使用LinkedHashMap,保持存储过程参数列表和map参数添加顺序一致;
        2.map中每个value都是实体FunctionModel,表名/存储过程也一样
        
##map中定义调用参数代名词
  
     /**
	 *  调用存储过程,没有out参数
	 */
	String callinFunction(@Param("para")Map<String, FunctionModel> paraMap);
    
    注意:@Param就是指定map参数的代名词，xml中使用它表示整个map


##xml中循环map key/value

   <!-- 调用存储过程,没有out参数 -->
    <select id="callinFunction" resultType="String"  parameterType="map" statementType="CALLABLE"  useCache="false">
       <!--<![CDATA[-->
           select ${para.funName.value}
             <foreach collection="para" index="key" item="value" open="(" close=")" separator=",">
                <if test="key !=null and key != 'funName'">
                     #{value.value,mode=IN,jdbcType=${value.jdbcType}}
                </if>
             </foreach>
     <!--  ]]>-->
    </select>
    注意:  collection="para"中para就是指代map, 
          index="key"   key是当次迭代的键值对之key
          item="value"  value是当次迭代的键值对之value
          ${value.value。。。 表示map中一个value的FunctionModel实体的属性value
           


     
 