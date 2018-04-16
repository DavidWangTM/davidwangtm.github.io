---
layout:     post
title:      "Spring回顾 (五)"
subtitle:   "Spring JDBC 写 ORM框架"
date:       2016-11-14 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Spring
--- 

## Spring Framework JDBC 简介

Spring Framework JDBC抽象提供的增值可能通过下表中:

![basic_design_patterns_27](/img/in-post/basic_design_patterns/basic_design_patterns_27.png)

Spring框架帮助我们做的事情有：
- 打开数据库连接
- 执行相关sql语句
- 处理异常
- 操作事务 
- 关闭数据库连接

### 选择JDBC数据库访问的方法

除了三种风格的JdbcTemplate之外，新的SimpleJdbcInsert和SimplejdbcCall方法优化了数据库元数据，RDBMS对象样式采用了类似于JDO Query设计的面向对象方法。一旦你开始使用这些方法之一，你仍然可以混合和匹配，以包括来自不同方法的功能。所有方法都需要符合JDBC 2.0的驱动程序，一些高级功能需要JDBC 3.0驱动程序。

- JdbcTemplate是一种经典且最流行的Spring JDBC方法，这是一种最底层（lowest level）的方法，其他方法内部都借助与JdbcTemplate来完成。

- NamedParameterJdbcTemplate 封装了JdbcTemplate以提供命名参数，而不是传统的JDBC“？”占位符。

- SimpleJdbcInsert和SimpleJdbcCall优化数据库元数据，以限制必要配置的数量。这种方法简化了编码，只需要提供表或过程的名称，并提供与列名匹配的参数映射。这仅在数据库提供足够的元数据时有效。如果数据库不提供此元数据，则必须提供参数的显式配置。

- RDBMS Objects 包括 MappingSqlQuery, SqlUpdate 和 StoredProcedure，需要你在数据访问层初始化期间建立可重用的并且是线程安全的对象。此方法在JDO Query之后建模，你可以在其中定义查询字符串，声明参数并编译查询。一旦你这样做，执行方法可以多次调用传入的各种参数值。

## 基于JdbcTemplate 写 ORM

```
<bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource" >
	<property name="driverClassName" value="com.mysql.jdbc.Driver" />
    <!--注意一下&characterEncoding要修改为&amp;characterEncoding-->
    <property name="url" value="jdbc:mysql://localhost:3306/dbstudent?useSSL=false"/>
    <property name="username" value="root"/>
    <property name="password" value="123456" />
</bean>
		
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate" abstract="false" lazy-init="false" autowire="default" >
    <property name="dataSource">
    	<ref bean="datasource" />
    </property>
 </bean>
```

创建组装Sql的基础接口类:

```
public interface BaseSql
{
	
	public String getSql() throws Exception;

	public List<Object> getParam();

	public void setSql(BaseSql query) throws Exception;

	public String toString();
}
```

创建BaseSql工具类:

```
public class BaseSqlUtils
{
	public static <T> BaseSql createSelect(T obj)
	{
		return new SelectBaseSql<T>(obj);
	}

	public static <T> BaseSql createInster(T obj)
	{
		return new InsertBaseSql<T>(obj);
	}

	public static <T> BaseSql createUpdate(T obj)
	{
		return new UpdateBaseSql<T>(obj);
	}

	public static <T> BaseSql createDeleteByObj(T obj)
	{
		return new DeleteByObjBaseSql<T>(obj);
	}

	
}
```

基础实体转化类:

```
public abstract class BaseEntity implements Serializable
{
	private static final long serialVersionUID = -696673736536660920L;

	public abstract String toString();

	public abstract int hashCode();

	public abstract boolean equals(Object obj);
	
	/**
	 * map转对象。
	 * @param map
	 * @param objClass
	 * @return
	 */
	public static <T> T mapToObj(Map<String, Object> map, Class<T> objClass)
	{
		if (map == null || objClass == null)
			return null;
		
		Field[] fields = getFieldByClass(objClass);
		T obj = null;

		try
		{
			obj = objClass.newInstance();
		}
		catch (InstantiationException e)
		{
			e.printStackTrace();
			return null;
		}
		catch (IllegalAccessException e1)
		{
			e1.printStackTrace();
			return null;
		}
		setFieldValue(map, obj, fields, true);
		return obj;
	}

	public static <T> List<T> mapToObjs(List<Map<String, Object>> lstMap, Class<T> objClass)
	{
		if (lstMap == null || lstMap.isEmpty())
			return null;
		List<T> lst = new ArrayList<T>();
		for (Map<String, Object> map : lstMap)
		{
			lst.add(BaseEntity.mapToObj(map, objClass));
		}
		return lst;
	}

	/**
	 * 调用set方法。
	 * @param obj
	 * @param att
	 * @param value
	 * @param type
	 */
	private static void setter(Object obj, String att, Object value, Class<?> type)
	{
		att = StringUtil.toUpperCaseFirstOne(att);
		try
		{
			Method method = obj.getClass().getMethod("set" + att, type);
			method.invoke(obj, value);
		}
		catch (Exception e)
		{
			e.printStackTrace();
		}
	}

	/**
	 * 调用get方法
	 * @param obj
	 * @param att
	 * @return
	 */
	public static Object getter(Object obj, String att)
	{
		att = StringUtil.toUpperCaseFirstOne(att);
		try
		{
			Method method = obj.getClass().getMethod("get" + att);
			return method.invoke(obj);
		}
		catch (Exception e)
		{
			e.printStackTrace();
			return null;
		}
	}

	/**
	 * 初始化方法，用户子类带参数得构造方法
	 * @param map
	 * @param objClass
	 * @param obj
	 * @return
	 */
	public <T> T init(Map<String, Object> map, T obj)
	{
		if (map == null || obj == null)
			return null;

		Field[] fields = getFieldByClass(obj.getClass());

		setFieldValue(map, obj, fields, false);
		return obj;
	}

	/**
	 * 设置字段值。
	 * @param map
	 * @param obj
	 * @param fields
	 * @param isMethod
	 */
	private static <T> void setFieldValue(Map<String, Object> map, T obj, Field[] fields, boolean isMethod)
	{
		TableColumn column = null;
		Object value = null;
		String key = null;

		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (column == null)
			{
				continue;
			}
			key = f.getName();
			if (StringUtil.isNotEmpty(column.value()))
				key = column.value();
			value = map.get(key);

			if (value == null)
				continue;
			
			if (isMethod)
			{
				setter(obj, f.getName(), value, f.getType());
				continue;
			}
			f.setAccessible(true);
			try
			{
				f.set(obj, value);
			}
			catch (IllegalArgumentException e)
			{
				e.printStackTrace();
			}
			catch (IllegalAccessException e)
			{
				e.printStackTrace();
			}
		}
	}

	/**
	 * 对象转map。
	 * @param obj
	 * @param objClass
	 * @return
	 */
	public <T> Map<String, Object> objToMap(T obj)
	{
		Field[] fields = getFieldByClass(obj.getClass());
		TableColumn column = null;
		Object value = null;
		String key = null;
		Map<String, Object> map = new HashMap<String, Object>();

		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (column == null)
				continue;
			key = f.getName();
			if (StringUtil.isNotEmpty(column.value()))
				key = column.value();
			
			f.setAccessible(true);
			try
			{
				value = f.get(obj);
				if (value == null)
					continue;

				map.put(key, value);
			}
			catch (IllegalArgumentException e)
			{
				e.printStackTrace();
			}
			catch (IllegalAccessException e)
			{
				e.printStackTrace();
			}
		}
		return map;
	}

	/**
	 * 获取实体主键或数据库字段主键名称。
	 * @param objClass
	 * @param isDataColumn true 返回数据库对应列名  false 返回属性列名。
	 * @return
	 * @throws Exception
	 */
	public static <T> String getKeyByClass(Class<T> objClass, boolean isDataColumn) throws Exception
	{
		Field[] lstField = getFieldByClass(objClass);
		TableColumn column = null;
		String objColumn = null;
		String dataColumn = null;
		for (Field f : lstField)
		{
			column = f.getAnnotation(TableColumn.class);
			if (column == null)
				continue;
			if (column.isKey())
			{
				objColumn = f.getName();
				dataColumn = column.value();
				if (dataColumn == null || "".equals(dataColumn))
					dataColumn = objColumn;
				break;
			}
		}
		if (objColumn == null || "".equals(objColumn))
			throw new Exception("Entity primary key not found");
		return isDataColumn ?  dataColumn : objColumn;
	}

	/**
	 * 获取主键的值。
	 * @param lst
	 * @param objClass
	 * @return
	 * @throws Exception
	 */
	public static <T> Collection<Serializable> getKeysValues(List<T> lst, Class<T> objClass) throws Exception
	{
		Collection<Serializable> lstResult = new LinkedHashSet<Serializable>();
		String objColumn = getKeyByClass(objClass, false);

		for (T t : lst)
		{
			lstResult.add((Serializable)getter(t, objColumn));
		}
		return lstResult;
	}

	/**
	 * 获取所有成员变量（包括继承的私有成员变量）
	 * @param objClass
	 * @return
	 */
	public static Field[] getFieldByClass(Class<?> objClass)
	{
		List<Field> lstField = new ArrayList<Field>();
		Field[] tempField = null;
		if (objClass == null || objClass.getDeclaredFields() == null || objClass.equals(Object.class))
			return null;
		if (objClass.getSuperclass() != null)
		{
			tempField = getFieldByClass(objClass.getSuperclass());
			if (tempField != null)
			{
				for (Field f : tempField)
				{
					if (!f.getName().equals("serialVersionUID"))
						lstField.add(f);
				}
			}
		}
		tempField = objClass.getDeclaredFields();
		if (tempField != null)
		{
			for (Field f : tempField)
			{
				if (!f.getName().equals("serialVersionUID"))
					lstField.add(f);
			}
		}
		return lstField.toArray(new Field[0]);
	}
}
```

创建拼接接口基础实现类:

```
public abstract class Sql<T> implements BaseSql
{
	protected StringBuffer sql;
	protected Class<?> objClass;
	protected T obj;
	protected List<Object> lstParam;
	protected Field[] fields;
	private String tableName;

	public Sql(Class<?> objClass)
	{
		this.objClass = objClass;
		//this.obj = obj;
		fields = BaseEntity.getFieldByClass(objClass);
	}

	/**
	 * 获取字段列表(插入使用)
	 * @return
	 */
	public String getFields()
	{
		StringBuffer sqlField = new StringBuffer();
		TableColumn column = null;
		String key = null;

		int count = 0;
		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (!this.valdateTableColumnNullAndIncrement(column))
				continue;
			key = f.getName();
			if (StringUtil.isNotEmpty(column.value()))
				key = column.value();

			if (count > 0)
				sqlField.append(",");
			sqlField.append(key);
			count++;
		}
		return sqlField.toString();
	}

	/**
	 * 获取字段参数位置。
	 * @return
	 */
	public String getFieldsParamIndex()
	{
		int count = this.getFieldsCount();
		StringBuffer sql = new StringBuffer();
		for (int n = 0; n < count - 1; n++)
			sql.append("?").append(',');
		sql.append("?");
		return sql.toString();
	}

	/**
	 * 获取字段数量。（插入使用）
	 * @return
	 */
	public int getFieldsCount()
	{
		int count = 0;

		for (Field f : fields)
		{
			if (!this.valdateTableColumnNullAndIncrement(f.getAnnotation(TableColumn.class)))
			{
				continue;
			}
			count++;
		}
		return count;
	}

	/**
	 * 获取表明
	 * @return
	 * @throws Exception
	 */
	public String getTableName() throws Exception
	{
		if (tableName != null && !"".equals(tableName))
			return tableName;
		Table table = (Table) objClass.getAnnotation(Table.class);
		if (table == null || table.name().equals(""))
			throw new Exception("get table name error, annotation not exist");
		tableName = table.name();
		return tableName;
	}

	/**
	 * 验证表字段 Annotation
	 * @param column 
	 * @return
	 */
	public boolean valdateTableColumnNullAndIncrement(TableColumn column)
	{
		if (column == null || column.increment() == true)
		{
			return false;
		}
		return true;
	}

	/**
	 * 验证参数默认值。
	 * @param obj
	 * @return
	 */
	public boolean valdateParamDefault(Object obj)
	{
		if (obj == null)
			return false;
		if (Integer.class.equals(obj.getClass()))
		{
			if ((Integer)obj == Constants.ENTITY_NULLITY_DEFAULT)
				return false;
		}
		if (Long.class.equals(obj.getClass()))
		{
			if ((Long)obj == Constants.ENTITY_NULLITY_DEFAULT)
				return false;
		}
		if (Float.class.equals(obj.getClass()))
		{
			if ((Float)obj == Constants.ENTITY_NULLITY_DEFAULT)
				return false;
		}
		if (Double.class.equals(obj.getClass()))
		{
			if ((Double)obj == Constants.ENTITY_NULLITY_DEFAULT)
				return false;
		}
		return true;
	}
	
	/**
	 * 获取查询参数
	 * @return
	 */
	public List<Object> getSelectParam()
	{
		lstParam = new ArrayList<Object>();
		Object value = null;

		for (Field f : fields)
		{
			if (null == f.getAnnotation(TableColumn.class))
				continue;
			value = BaseEntity.getter(obj, f.getName());
			if (!this.valdateParamDefault(value))
				continue;
			lstParam.add(value);
		}
		return lstParam;
	}

	/**
	 * 获取插入参数
	 * @return
	 */
	public List<Object> getInsertParam()
	{
		lstParam = new ArrayList<Object>();

		for (Field f : fields)
		{
			if (!this.valdateTableColumnNullAndIncrement(f.getAnnotation(TableColumn.class)))
				continue;
			lstParam.add(BaseEntity.getter(obj, f.getName()));
		}
		return lstParam;
	}

	/**
	 * 获取where主键条件
	 * @return
	 * @throws Exception
	 */
	public String getKeyWhere() throws Exception
	{
		StringBuffer sql = new StringBuffer();
		TableColumn column = null;
		String key = null;
		Object value = null;
		lstParam = new ArrayList<Object>();

		sql.append(" where ");
		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (column == null || !column.isKey())
				continue;
			key = f.getName();
			if (StringUtil.isNotEmpty(column.value()))
				key = column.value();

			value = BaseEntity.getter(obj, f.getName());
			if (!this.valdateParamDefault(value))
				throw new Exception("key value is null");
			lstParam.add(value);
			sql.append(key).append(" = ").append("? ");
			break;
		}
		return sql.toString();
	}

	/**
	 * 获取查询语句。
	 * @return
	 */
	public String getSelectSql()
	{
		StringBuffer sql = new StringBuffer();
		TableColumn column = null;
		String key = null;

		sql.append(" where 1=1 ");
		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (column == null)
			{
				continue;
			}
			key = f.getName();
			if (StringUtil.isNotEmpty(column.value()))
				key = column.value();

			if (!this.valdateParamDefault(BaseEntity.getter(obj, f.getName())))
				continue;
			sql.append(" and ").append(key).append(" = ? ");
		}
		return sql.toString();
	}
}
```
SelectBaseSql 为查询实体操作类

```
public class SelectBaseSql<T> extends Sql<T> implements BaseSql
{
	private String[] showColumns = null;

	public SelectBaseSql(T obj)
	{
		super(obj.getClass());
		super.obj = obj;
	}
	
	public String getSql() throws Exception
	{
		sql = new StringBuffer();
		sql.append(" select ").append(this.getShowColumns()).append(" from ")
				.append(this.getTableName()).append(this.getSelectSql());
		return sql.toString();
	}

	private String getShowColumns()
	{
		if (this.showColumns == null || this.showColumns.length == 0)
			return " * ";
		StringBuffer strColumns = new StringBuffer();

		for (int n = 0; n < this.showColumns.length - 1; n++)
			strColumns.append(this.showColumns[n]).append(',');
		strColumns.append(this.showColumns[this.showColumns.length - 1]).append(' ');

		return strColumns.toString();
	}

	public List<Object> getParam()
	{
		return super.getSelectParam();
	}

	public void setSql(CombineSql query)
	{
		// TODO Auto-generated method stub
	}

	public String toString()
	{
		try
		{
			return this.getSql();
		}
		catch (Exception e)
		{
		}
		return "-----";
	}

}
```

InsertBaseSql 为插入实体操作类

```
public class InsertBaseSql<T> extends Sql<T> implements BaseSql
{
	public InsertBaseSql(T obj)
	{
		super(obj.getClass()); // 初始化
		super.obj = obj;
	}

	@Override
	public String getSql() throws Exception
	{
		sql = new StringBuffer();
		sql.append("insert into ").append(super.getTableName())
		.append(" (").append(this.getFields()).append(") ")
		.append("values(").append(this.getFieldsParamIndex()).append(")");
		return sql.toString();
	}

	@Override
	public List<Object> getParam()
	{
		return super.getInsertParam();
	}

	@Override
	public void setSql(CombineSql query)
	{
	}
	
	public String toString()
	{
		try
		{
			return this.getSql();
		}
		catch (Exception e)
		{
		}
		return "-----";
	}
}

```

UpdateBaseSql 为更新操作实现类：

```
public class UpdateBaseSql<T> extends Sql<T> implements BaseSql
{

	private List<Object> lstParam;

	public UpdateBaseSql(T obj)
	{
		super(obj.getClass());
		super.obj = obj;
		lstParam = new ArrayList<Object>();
	}

	public String getSql() throws Exception
	{
		sql = new StringBuffer();
		sql.append(" update ").append(this.getTableName()).append(" ")
				.append(this.getUpdateSql());
		sql.append(this.getKeyWhere());
		return sql.toString();
	}

	public List<Object> getParam()
	{
		lstParam = this.getUpdateParam();
		lstParam.add(this.getKeyParam());
		return lstParam;
	}

	public void setSql(CombineSql query)
	{
		// TODO Auto-generated method stub
	}

	public String toString()
	{
		try
		{
			return this.getSql();
		}
		catch (Exception e)
		{
		}
		return "-----";
	}

	public String getUpdateSql()
	{
		StringBuffer sql = new StringBuffer();
		TableColumn column = null;
		String key = null;
		int count = 0;
		Object value;

		sql.append(" set ");
		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (!this.valdateTableColumnNullAndIncrement(column) || column.isKey())
				continue;
			key = f.getName();
			if (StringUtil.isNotEmpty(column.value()))
				key = column.value();

			value = BaseEntity.getter(obj, f.getName());
			if (!this.valdateParamDefault(value))
				continue;
			if (count > 0)
				sql.append(", ");
			sql.append(key).append(" = ? ");
			count++;
		}
		return sql.toString();
	}

	public Object getKeyParam()
	{
		TableColumn column = null;
		Object value = null;

		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (!this.valdateTableColumnNullAndIncrement(column) || !column.isKey())
				continue;
			value = BaseEntity.getter(obj, f.getName());
			break;
		}
		return value;
	}

	public List<Object> getUpdateParam()
	{
		lstParam = new ArrayList<Object>();
		Object value = null;
		TableColumn column = null;

		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (!this.valdateTableColumnNullAndIncrement(f.getAnnotation(TableColumn.class))
					|| column.isKey())
				continue;
			value = BaseEntity.getter(obj, f.getName());
			if (!this.valdateParamDefault(value))
				continue;
			lstParam.add(value);
		}
		return lstParam;
	}
}
```

DeleteBaseSql 为删除操作实现类:

```
public class DeleteBaseSql<T> extends Sql<T> implements BaseSql
{
	private Serializable id;
	public DeleteBaseSql(Class<?> objClass, Serializable id)
	{
		super(objClass);
		this.id = id;
	}

	public String getSql() throws Exception
	{
		sql = new StringBuffer();
		sql.append("delete from ").append(this.getTableName()).append(" ").append(this.getKeyWhere());

		return sql.toString();
	}

	public List<Object> getParam()
	{
		if (this.lstParam == null)
		{
			try
			{
				this.getSql();
			}
			catch (Exception e)
			{
				e.printStackTrace();
			}
		}
		return this.lstParam;
	}

	public void setSql(CombineSql query)
	{
		// TODO Auto-generated method stub
	}

	@Override
	public String getKeyWhere() throws Exception
	{
		StringBuffer sql = new StringBuffer();
		TableColumn column = null;
		String key = null;
		Object value = null;
		lstParam = new ArrayList<Object>();

		sql.append(" where ");
		for (Field f : fields)
		{
			column = f.getAnnotation(TableColumn.class);
			if (!this.valdateTableColumnNullAndIncrement(column) || !column.isKey())
				continue;
			key = f.getName();
			if (StringUtil.isNotEmpty(column.value()))
				key = column.value();

			value = id;
			if (!this.valdateParamDefault(value))
				throw new Exception("key value is null");
			lstParam.add(value);
			sql.append(key).append(" = ").append("? ");
			break;
		}
		return sql.toString();
	}
	
	public String toString()
	{
		try
		{
			return this.getSql();
		}
		catch (Exception e)
		{
		}
		return "-----";
	}

}

```

Dao实现方法

```
public interface BaseDao<T>{
	public List<T> search(T obj);
	public int add(T obj);
	public int update(T obj);
	public int delete(T obj);
}
```
DaoImpl 实现

```
public class BaseDaoImpl<T> implements BaseDao<T>
{
	@Resource
	private JdbcTemplate jdbcTemplate;
	
	//BaseSql 是组装sql 的基础接口
	//BaseSqlUtils 是 BaseSql 工具类
	//BaseEntity 转换实体类	

	public List<T> search(T obj) throws Exception
	{
		BaseSql comSql = BaseSqlUtils.createSelect(obj);
		return (List<T>) BaseEntity.mapToObjs(this.querySqlByBaseSql(comSql), obj.getClass());
	}
	
	public int add(T obj) throws Exception
	{
		BaseSql comSql = BaseSqlUtils.createInster(obj);
		return this.updateSqlByBaseSql(comSql);
	}

	public int update(T obj) throws Exception
	{
		BaseSql comSql = BaseSqlUtils.createUpdate(obj);
		return this.updateSqlByBaseSql(comSql);
	}

	public int delete(Class<T> objClass, Serializable id) throws Exception
	{
		BaseSql comSql = BaseSqlUtils.createDelete(objClass, id);
		return this.updateSqlByBaseSql(comSql);
	}

	protected List<Map<String, Object>> querySqlByBaseSql(BaseSql sql) throws Exception
	{
		if (sql.getParam() == null || sql.getParam().isEmpty())
			return jdbcTemplate.queryForList(sql.getSql());
		return jdbcTemplate.queryForList(sql.getSql(),sql.getParam().toArray(new Object[0]));
		
	}

	protected int updateSqlByBaseSql(BaseSql sql) throws Exception
	{
		if (sql.getParam() == null || sql.getParam().isEmpty())
			return jdbcTemplate.update(sql.getSql());
		return jdbcTemplate.update(sql.getSql(), sql.getParam().toArray(new Object[0]));
	}

}
```
