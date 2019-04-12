---

layout: mypost

title: Springboot

categories: [Springboot]

---

<iframe src="//music.163.com/outchain/player?type=2&id=5257963&auto=0&height=66" frameborder="0" width="100%" height="86px" ></iframe>


# Springboot-Mybatis

>* 关于Mybatis的一些官方文档:
>* Mybatis中文文档:   http://www.mybatis.org/mybatis-3/zh/index.html
>* Mybatis Generator：http://www.mybatis.org/generator/index.html


[TOC]


----------


## 1.Myabtis简介

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和Java的POJO(Plain Old Java Objects，普通老式Java 对象）为数据库中的记录。Mybatis的前身是IBatis。

## 2.Mybatis的配置
### 2.1 全局XML配置文件

> MyBatis 全局XML配置文件包含影响MyBatis行为的设置和属性

XML定义

* 文档类型约束方式
DTD：Document Type Definition
http://mybatis.org/dtd/mybatis-3-config.dtd
> 每个XML文件都有相应的dtd约束，可以在官方文档里找到。dtd文件里，明确表明了该XML文件应该有的配置、设置里面的选项、以及对应的位置等等
* mybatis全局配置文件开头的dtd约束

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
```

dtd约束

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!--

      Copyright 2009-2016 the original author or authors.

       Licensed under the Apache License, Version 2.0 (the "License");
       you may not use this file except in compliance with the License.
       You may obtain a copy of the License at

          http://www.apache.org/licenses/LICENSE-2.0

       Unless required by applicable law or agreed to in writing, software
       distributed under the License is distributed on an "AS IS" BASIS,
       WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
       See the License for the specific language governing permissions and
       limitations under the License.

-->
<!ELEMENT configuration (properties?, settings?, typeAliases?, typeHandlers?, objectFactory?, objectWrapperFactory?, reflectorFactory?, plugins?, environments?, databaseIdProvider?, mappers?)>

<!ELEMENT databaseIdProvider (property*)>
<!ATTLIST databaseIdProvider
type CDATA #REQUIRED
>

<!ELEMENT properties (property*)>
<!ATTLIST properties
resource CDATA #IMPLIED
url CDATA #IMPLIED
>

<!ELEMENT property EMPTY>
<!ATTLIST property
name CDATA #REQUIRED
value CDATA #REQUIRED
>

<!ELEMENT settings (setting+)>

<!ELEMENT setting EMPTY>
<!ATTLIST setting
name CDATA #REQUIRED
value CDATA #REQUIRED
>

<!ELEMENT typeAliases (typeAlias*,package*)>

<!ELEMENT typeAlias EMPTY>
<!ATTLIST typeAlias
type CDATA #REQUIRED
alias CDATA #IMPLIED
>

<!ELEMENT typeHandlers (typeHandler*,package*)>

<!ELEMENT typeHandler EMPTY>
<!ATTLIST typeHandler
javaType CDATA #IMPLIED
jdbcType CDATA #IMPLIED
handler CDATA #REQUIRED
>

<!ELEMENT objectFactory (property*)>
<!ATTLIST objectFactory
type CDATA #REQUIRED
>

<!ELEMENT objectWrapperFactory EMPTY>
<!ATTLIST objectWrapperFactory
type CDATA #REQUIRED
>

<!ELEMENT reflectorFactory EMPTY>
<!ATTLIST reflectorFactory
type CDATA #REQUIRED
>

<!ELEMENT plugins (plugin+)>

<!ELEMENT plugin (property*)>
<!ATTLIST plugin
interceptor CDATA #REQUIRED
>

<!ELEMENT environments (environment+)>
<!ATTLIST environments
default CDATA #REQUIRED
>

<!ELEMENT environment (transactionManager,dataSource)>
<!ATTLIST environment
id CDATA #REQUIRED
>

<!ELEMENT transactionManager (property*)>
<!ATTLIST transactionManager
type CDATA #REQUIRED
>

<!ELEMENT dataSource (property*)>
<!ATTLIST dataSource
type CDATA #REQUIRED
>

<!ELEMENT mappers (mapper*,package*)>

<!ELEMENT mapper EMPTY>
<!ATTLIST mapper
resource CDATA #IMPLIED
url CDATA #IMPLIED
class CDATA #IMPLIED
>

<!ELEMENT package EMPTY>
<!ATTLIST package
name CDATA #REQUIRED
>
```

* 子元素

1.configuration（配置）
MybatisXML配置的根节点

2.properties（属性）
配置属性时作为占位符使用,这些属性都是可外部配置且可动态替换的，既可以在典型的 Java 属性文件中配置，亦可通过 properties 元素的子元素来传递。然后其中的属性就可以在整个配置文件中被用来替换需要动态配置的属性值。
```XML
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```

```XML
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```
API配置方式

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);

// ... 或者 ...

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```

如果属性在不只一个地方进行了配置，那么 MyBatis 将按照下面的顺序来加载：
_1. `<property>` 元素
_2. `<properties>` 元素 resource属性
_3. SqlSessionFactoryBuilder().build(reader, props)
通过方法参数传递的属性具有最高优先级，resource/url 属性中指定的配置文件次之，最低优先级的是 properties 属性中指定的属性。



3、settings（设置）
用于修改MyBatis的运行时行为
```XML
<!-- 全局参数 -->
	<settings>
		<!-- 使全局的映射器启用或禁用缓存。 -->
		<setting name="cacheEnabled" value="true"/>
		
		<!-- 全局启用或禁用延迟加载。当禁用时，所有关联对象都会即时加载。 -->
		<setting name="lazyLoadingEnabled" value="true"/>
		
		<!-- 当启用时，有延迟加载属性的对象在被调用时将会完全加载任意属性。否则，每种属性将会按需要加载。 -->
		<setting name="aggressiveLazyLoading" value="true"/>
		
		<!-- 是否允许单条sql 返回多个数据集  (取决于驱动的兼容性) default:true -->
		<setting name="multipleResultSetsEnabled" value="true"/>
		
		<!-- 是否可以使用列的别名 (取决于驱动的兼容性) default:true -->
		<setting name="useColumnLabel" value="true"/>
		
		<!-- 允许JDBC 生成主键。需要驱动器支持。如果设为了true，这个设置将强制使用被生成的主键，有一些驱动器不兼容不过仍然可以执行。  default:false  -->
		<setting name="useGeneratedKeys" value="false"/>
		
		<!-- 指定 MyBatis 如何自动映射 数据基表的列 NONE：不隐射　PARTIAL:部分  FULL:全部  -->  
		<setting name="autoMappingBehavior" value="PARTIAL"/>
		
		<!-- 这是默认的执行类型  （SIMPLE: 简单； REUSE: 执行器可能重复使用prepared statements语句；BATCH: 执行器可以重复执行语句和批量更新）  -->
		<setting name="defaultExecutorType" value="SIMPLE"/>
		
		<!-- 使用驼峰命名法转换字段。 -->
		<setting name="mapUnderscoreToCamelCase" value="true"/>
		
		<!-- 设置本地缓存范围 session:就会有数据的共享  statement:语句范围 (这样就不会有数据的共享 ) defalut:session -->
        <setting name="localCacheScope" value="SESSION"/>
		
        <!-- 设置但JDBC类型为空时,某些驱动程序 要指定值,default:OTHER，插入空值时不需要指定类型 -->
        <setting name="jdbcTypeForNull" value="NULL"/>

		<!--设置为null值的时候允许返回字段-->
		<setting name="callSettersOnNulls" value="true"/>

	</settings>

```
4、typeAliases（类型别名）
为Java类型建立别名，一般使用更短的名称替代
```java
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```
也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean。每一个在包 domain.blog 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 domain.blog.Author 的别名为 author；若有注解，则别名为其注解值。见下面的例子：
```XML
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

```java
@Alias("author")
public class Author {
    ...
}
```
5、typeHandlers（类型处理器） 
用于将预编译语句（PreparedStatement）或结果集（ResultSet）中的JDBC类型转化成Java 类型。如：BooleanTypeHandler  将 JDBC类型中的BOOLEAN转化成Java类型中的java.lang.Boolean 或者 boolean。
若需要转换 Java 8 新增的Date与Time API，即JSR-310，需要再引入mybatis-typehandlers-jsr310：
```
<dependency>
 	<groupId>org.mybatis</groupId>
 	<artifactId>mybatis-typehandlers-jsr310</artifactId>
	 <version>1.0.2</version>
</dependency>
```

6、objectFactory（对象工厂）
用于创建结果对象实例，提供默认构造器或者执行构造参数初始化目标类型的对象。通常使用场景，不需要调整默认的实现。
```java
// ExampleObjectFactory.java
public class ExampleObjectFactory extends DefaultObjectFactory {
  public Object create(Class type) {
    return super.create(type);
  }
  public Object create(Class type, List<Class> constructorArgTypes, List<Object> constructorArgs) {
    return super.create(type, constructorArgTypes, constructorArgs);
  }
  public void setProperties(Properties properties) {
    super.setProperties(properties);
  }
  public <T> boolean isCollection(Class<T> type) {
    return Collection.class.isAssignableFrom(type);
  }}
```
```xml
<!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```
7、plugins（插件）
MyBatis 允许你在已映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：

* Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
* ParameterHandler (getParameterObject, setParameters)
* ResultSetHandler (handleResultSets, handleOutputParameters)
* StatementHandler (prepare, parameterize, batch, update, query)
```java
// ExamplePlugin.java
@Intercepts({@Signature(
  type= Executor.class,
  method = "update",
  args = {MappedStatement.class,Object.class})})
public class ExamplePlugin implements Interceptor {
  public Object intercept(Invocation invocation) throws Throwable {
    return invocation.proceed();
  }
  public Object plugin(Object target) {
    return Plugin.wrap(target, this);
  }
  public void setProperties(Properties properties) {
  }
}
```
```xml
<!-- mybatis-config.xml -->
<plugins>
  <plugin interceptor="org.mybatis.example.ExamplePlugin">
    <property name="someProperty" value="100"/>
  </plugin>
</plugins>
```
上面的插件将会拦截在 Executor 实例中所有的 “update” 方法调用， 这里的 Executor 是负责执行低层映射语句的内部对象


8、environments（环境配置）

MyBatis 允许配置多个环境，在运行时，通过传递环境信息，切换关联的SqlSessionFactory 实例。因此，MyBatis 中的环境（environments）类似于Maven 或者 Spring 中的Profile。
```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

9、databaseIdProvider（数据库厂商标识）
MyBatis 是面向SQL的映射框架，所执行SQL语句的语法依赖于数据库提供商的实现，比如：MySQL、Oracle、SQL Server等。在配置映射SQL语句时，可为其指定具体的数据库提供商的实现。因此，在全局XML配置文件中可以定义多个数据库标识供应器（databaseIdProvider）。
```xml
<databaseIdProvider type="DB_VENDOR">
  <property name="SQL Server" value="sqlserver"/>
  <property name="DB2" value="db2"/>
  <property name="Oracle" value="oracle" />
</databaseIdProvider>
```
10、mappers（映射器）
```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```
```xml
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```
```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```
```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

* API接口
    org.apache.ibatis.session.Configuration


### 2.2 SQL Mapper XML配置
SQL Mapper XML 配置用于映射SQL 模板语句与Java类型的配置。
SQL 映射文件只有很少的几个顶级元素:

 - cache – 对给定命名空间的缓存配置。 
 - cache-ref – 对其他命名空间缓存配置的引用。 
 - resultMap –是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。 
 - sql –可被其他语句引用的可重用语句块。 
 - insert – 映射插入语句 
 - update – 映射更新语句 
 - delete – 映射删除语句
 - select – 映射查询语句

**select**
```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
  SELECT * FROM PERSON WHERE ID = #{id}
</select>
```
接受一个 int（或 Integer）类型的参数，并返回一个 HashMap 类型的对象，其中的键是列名，值便是结果行中的对应值。其中里面的过程就类似于
```java
// 近似的 JDBC 代码，非 MyBatis 代码...
String selectPerson = "SELECT * FROM PERSON WHERE ID=?";
PreparedStatement ps = conn.prepareStatement(selectPerson);
ps.setInt(1,id);
```
select元素的一些属性
```xml
<select
  id="selectPerson" 
  parameterType="int"
  parameterMap="deprecated"
  resultType="hashmap"
  resultMap="personResultMap"
  flushCache="false"
  useCache="true"
  timeout="10"
  fetchSize="256"
  statementType="PREPARED"
  resultSetType="FORWARD_ONLY">
```

**insert, update 和 delete**
```xml
<insert id="insertAuthor">
  insert into Author (id,username,password,email,bio)
  values (#{id},#{username},#{password},#{email},#{bio})
</insert>

<update id="updateAuthor">
  update Author set
    username = #{username},
    password = #{password},
    email = #{email},
    bio = #{bio}
  where id = #{id}
</update>

<delete id="deleteAuthor">
  delete from Author where id = #{id}
</delete>
```
一些属性
```xml
<insert
  id="insertAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">

<update
  id="updateAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">

<delete
  id="deleteAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```

**sql**

用来定义一个公用的SQL片段，方便重用
```xml
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
```
使用：
```xml
<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>
```

**参数**

 - 简单参数，单个参数。
```java
public List<XXBean> getXXBeanList(String xxCode);  
```
```XML
<select id="getXXXBeanList" parameterType="java.lang.String" resultType="XXBean">
　　select t.* from tableName t where t.id= #{id}  
</select>  
```

其中方法名和ID一致，#{}中的参数名与方法中的参数名一直， 我这里采用的是XXXBean是采用的短名字,select后的字段列表要和bean中的属性名一致， 如果不一致的可以用 as 来补充。

 - 多个参数。
```java
public List<XXXBean> getXXXBeanList(String xxId, String xxCode);  
```
```xml
<select id="getXXXBeanList" resultType="XXBean">
　　select t.* from tableName where id = #{0} and name = #{1}  
</select>  
```
由于是多参数那么就不能使用parameterType， 改用#｛index｝是第几个就用第几个的索引，索引从0开始。也可以使用@Param注解。
```java
public AddrInfo getAddrInfo(@Param("corpId")int corpId, @Param("addrId")int addrId);
```

```xml
<select id="getAddrInfo"  resultMap="com.xxx.xxx.AddrInfo">
       SELECT * FROM addr__info 
　　　　where addr_id=#{addrId} and corp_id=#{corpId}
</select>
```
- javaBean 传递

```
<insert id="insertUser" parameterType="User">
  insert into users (id, username, password)
  values (#{id}, #{username}, #{password})
</insert>
```
和Map是类似的。但是要指定parameterType
-  Map传递
```java
public List<XXXBean> getXXXBeanList(HashMap map);  
```
```xml
<select id="getXXXBeanList" parameterType="hashmap" resultType="XXBean">
　　select 字段... from XXX where id=#{xxId} code = #{xxCode}  
</select>  
```
hashmap是mybatis自带的TypeHandler。map中key的名字是那个就在#{}使用

 -  List传递
```java
public List<XXXBean> getXXXBeanList(List<String> list);  
```
```xml
<select id="getXXXBeanList" resultType="XXBean">
　　select 字段... from XXX where id in
　　<foreach item="item" index="index" collection="list" open="(" separator="," close=")">  
　　　　#{item}  
　　</foreach>  
</select>  
```
foreach 最后的效果是select 字段... from XXX where id in ('1','2','3','4') 

 - 混合类型传递
 应对所需参数既要包含String类型，又要包含List类型时的处理方法
```java
List<String> list_3 = new ArrayList<String>();
Map<String, Object> map2 = new HashMap<String, Object>();

list.add("1");
list.add("2");
map2.put("list", list); //网址id
map2.put("siteTag", "0");//网址类型

```

```java
public List<SysWeb> getSysInfo(Map<String, Object> map2) {
　　return getSqlSession().selectList("sysweb.getSysInfo", map2);
}
```

```xml
<select id="getSysInfo" parameterType="java.util.Map" resultType="SysWeb">
　　select 
    　　t.sysSiteId, 
    　　t.siteName,
    　　t1.mzNum as siteTagNum, 
    　　t1.mzName as siteTag,
    　　t.url, 
    　　t.iconPath
   from TD_WEB_SYSSITE t
   left join TD_MZ_MZDY t1 
    on t1.mzNum = t.siteTag and t1.mzType = 10
   WHERE t.siteTag = #{siteTag } 
   and t.sysSiteId not in 
   <foreach collection="list" item="item" index="index" open="(" close=")" separator=",">
       #{item}
   </foreach>
 </select>
```
本质上是综合了List和Map的用法。

- 需要注意的地方
 MyBatis 的其他部分一样，参数也可以指定一个特殊的数据类型。javaType一般可以根据参数对象的类型确定下来。如果该对象是一个hashMap,可以显式指定javaType来确保正确的类型TypeHandler被使用。另外要为可能为空的列指定 jdbcType。
```xml
#{property,javaType=int,jdbcType=NUMERIC}
```

要更进一步地自定义类型处理方式，你也可以指定一个特殊的类型处理器类（或别名）
```xml
#{age,javaType=int,jdbcType=NUMERIC,typeHandler=MyTypeHandler}
```
数值类型，还有一个小数保留位数的设置，来指定小数点后保留的位数
```xml
#{height,javaType=double,jdbcType=NUMERIC,numericScale=2}
```
mode 属性允许你指定 IN，OUT 或 INOUT 参数。如果参数的 mode 为 OUT 或 INOUT，就像你在指定输出参数时所期望的行为那样，参数对象的属性实际值将会被改变。 如果 mode 为 OUT（或 INOUT），而且 jdbcType 为 CURSOR（也就是 Oracle 的 REFCURSOR），你必须指定一个 resultMap 引用来将结果集 ResultMap 映射到参数的类型上。要注意这里的 javaType 属性是可选的，如果留空并且 jdbcType 是 CURSOR，它会被自动地被设为 ResultMap。
```xml
#{department, mode=OUT, jdbcType=CURSOR, javaType=ResultSet, resultMap=departmentResultMap}
```

**字符串替换**
直接在 SQL 语句中插入一个不转义的字符串。 比如，像 ORDER BY，可以这样使用
```xml
ORDER BY ${columnName}
```
那么下面这两个是等同的
```java
@Select("select * from user where id = #{id}")
User findById(@Param("id") long id);

@Select("select * from user where name = #{name}")
User findByName(@Param("name") String name);

@Select("select * from user where email = #{email}")
User findByEmail(@Param("email") String email);
```

```java
@Select("select * from user where ${column} = #{value}")
User findByColumn(@Param("column") String column, @Param("value") String value);

```
Tips:${column} 会被直接替换，而 #{value} 会被使用 ? 预处理。用这种方式接受用户的输入，并将其用于语句中的参数是不安全的，会导致潜在的 SQL 注入攻击，因此要么不允许用户输入这些字段，要么自行转义并检验


**结果映射**

- 简单映射
```xml
<!--映射到HashMap上，大部分情况下都够用-->
<select id="selectUsers" resultType="map">
  select id, username, hashedPassword
  from some_table
  where id = #{id}
</select>
```

```xml
<!--映射到JavaBean或者POJO-->
<select id="selectUsers" resultType="com.someapp.model.User">
  select id, username, hashedPassword
  from some_table
  where id = #{id}
</select>
```
```xml
<!-- mybatis-config.xml 中,配置类型别名，就不用输入完全限定名称 -->
<typeAlias type="com.someapp.model.User" alias="User"/>


<!-- SQL 映射 XML 映射到JavaBean或者POJO-->
<select id="selectUsers" resultType="User">
  select
      id, 
      username, 
      hashedPassword
  from some_table
  where id = #{id}
</select>
```
如果映射的字段名字和javaBean的属性名字不太一致的话，有两种方法。一种方法是在sql映射里使用as关键字。二是自定义一个ResultMap,在引用它的语句中使用 resultMap 属性就行了。如下
```
<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>

<select id="selectUsers" resultMap="userResultMap">
  select user_id, user_name, hashed_password
  from some_table
  where id = #{id}
</select>
```

- 高级结果映射
```xml
<!-- 非常复杂的语句 -->
<select id="selectBlogDetails" resultMap="detailedBlogResultMap">
  select
       B.id as blog_id,
       B.title as blog_title,
       B.author_id as blog_author_id,
       A.id as author_id,
       A.username as author_username,
       A.password as author_password,
       A.email as author_email,
       A.bio as author_bio,
       A.favourite_section as author_favourite_section,
       P.id as post_id,
       P.blog_id as post_blog_id,
       P.author_id as post_author_id,
       P.created_on as post_created_on,
       P.section as post_section,
       P.subject as post_subject,
       P.draft as draft,
       P.body as post_body,
       C.id as comment_id,
       C.post_id as comment_post_id,
       C.name as comment_name,
       C.comment as comment_text,
       T.id as tag_id,
       T.name as tag_name
  from Blog B
       left outer join Author A on B.author_id = A.id
       left outer join Post P on B.id = P.blog_id
       left outer join Comment C on P.id = C.post_id
       left outer join Post_Tag PT on PT.post_id = P.id
       left outer join Tag T on PT.tag_id = T.id
  where B.id = #{id}
</select>

<!-- 非常复杂的结果映射 -->
<resultMap id="detailedBlogResultMap" type="Blog">
  <constructor>
    <idArg column="blog_id" javaType="int"/>
  </constructor>
  <result property="title" column="blog_title"/>
  <association property="author" javaType="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
    <result property="favouriteSection" column="author_favourite_section"/>
  </association>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <association property="author" javaType="Author"/>
    <collection property="comments" ofType="Comment">
      <id property="id" column="comment_id"/>
    </collection>
    <collection property="tags" ofType="Tag" >
      <id property="id" column="tag_id"/>
    </collection>
    <discriminator javaType="int" column="draft">
      <case value="1" resultType="DraftPost"/>
    </discriminator>
  </collection>
</resultMap>
```
 constructor : 用于在实例化类时，注入结果到构造方法中
 -- idArg :ID 参数；标记出作为 ID 的结果可以帮助提高整体性能
 -- arg : 将被注入到构造方法的一个普通结果
 
 id : 一个 ID 结果；标记出作为 ID 的结果可以帮助提高整体性能
 
 result : 注入到字段或 JavaBean 属性的普通结果
 
 association – 一个复杂类型的关联；许多结果将包装成这种类型
--嵌套结果映射 – 关联本身可以是一个 resultMap 元素，或者从别处引用一个

collection – 一个复杂类型的集合
--嵌套结果映射 – 集合本身可以是一个 resultMap 元素，或者从别处引用一个

discriminator – 使用结果值来决定使用哪个 resultMap
--case – 基于某些值的结果映射
----嵌套结果映射 – case 本身可以是一个 resultMap元素，因此可以具有相同的结构和元素，或者从别处引用一个

ResultMap的属性列表
| 属性|描述|
|:---:|:--:|
|id	|当前命名空间中的一个唯一标识，用于标识一个结果映射。|
|type|	类的完全限定名, 或者一个类型别名（关于内置的类型别名，可以参考上面的表格）。|
|autoMapping|	如果设置这个属性，MyBatis将会为本结果映射开启或者关闭自动映射。 这个属性会覆盖全局的属性 autoMappingBehavior。默认值：未设置（unset）。|

id & result
```xml
<id property="id" column="post_id"/>
<result property="subject" column="post_subject"/>
```
id & result的属性

| 属性 |	描述|
|:----:|:----:|
|property|	映射到列结果的字段或属性。如果用来匹配的 JavaBean 存在给定名字的属性，那么它将会被使用。否则 MyBatis 将会寻找给定名称的字段。 无论是哪一种情形，你都可以使用通常的点式分隔形式进行复杂属性导航。 比如，你可以这样映射一些简单的东西：“username”，或者映射到一些复杂的东西上：“address.street.number”。|
|column	|数据库中的列名，或者是列的别名。一般情况下，这和传递给 resultSet.getString(columnName) 方法的参数一样。javaType一个 Java类的完全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。|
|jdbcType|	JDBC 类型，所支持的 JDBC 类型参见这个表格之后的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可能存在空值的列指定这个类型。|
|typeHandler|	我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的完全限定名，或者是类型别名。|


构造方法
```java
public class User {
   //...
   public User(Integer id, String username, int age) {
     //...
  }
//...
}
```
在ResultMap中应该这么定义
```xml
<constructor>
   <idArg column="id" javaType="int"/>
   <arg column="username" javaType="String"/>
   <arg column="age" javaType="_int"/>
</constructor>

<!--如果构造函数与javaBean里的顺序不一致，也可以用name来标记 -->
<constructor>
   <idArg column="id" javaType="int" name="id" />
   <arg column="age" javaType="_int" name="age" />
   <arg column="username" javaType="String" name="username" />
</constructor>

```


constructor里面的一些属性

| 属性 |	描述|
|:----:|:----:|
|column	|数据库中的列名，或者是列的别名。一般情况下，这和传递给 resultSet.getString(columnName) 方法的参数一样。|
|javaType|	一个 Java 类的完全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。
|jdbcType|	JDBC 类型，所支持的 JDBC 类型参见这个表格之前的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可能存在空值的列指定这个类型。|
|typeHandler|	我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的完全限定名，或者是类型别名。|
|select	|用于加载复杂类型属性的映射语句的 ID，它会从 column 属性中指定的列检索数据，作为参数传递给此 select 语句。具体请参考关联元素。|
|resultMap	|结果映射的 ID，可以将嵌套的结果集映射到一个合适的对象树中。 它可以作为使用额外 select 语句的替代方案。它可以将多表连接操作的结果映射成一个单一的 ResultSet。这样的 ResultSet 将会将包含重复或部分数据重复的结果集。为了将结果集正确地映射到嵌套的对象树中，MyBatis 允许你 “串联”结果映射，以便解决嵌套结果集的问题。想了解更多内容，请参考下面的关联元素。|
|name|	构造方法形参的名字。从 3.4.3 版本开始，通过指定具体的参数名，你可以以任意顺序写入 arg 元素。参看上面的解释|


关联
```xml
<association property="author" column="blog_author_id" javaType="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
</association>
```
关联（association）元素处理“有一个”类型的关系
MyBatis 有两种不同的方式加载关联：
--嵌套 Select 查询：通过执行另外一个 SQL 映射语句来加载期望的复杂类型。
--嵌套结果映射：使用嵌套的结果映射来处理连接结果的重复子集

属性
|属性	|描述|
|:----:|:----:|
|property|	映射到列结果的字段或属性。如果用来匹配的 JavaBean 存在给定名字的属性，那么它将会被使用。否则 MyBatis 将会寻找给定名称的字段。 无论是哪一种情形，你都可以使用通常的点式分隔形式进行复杂属性导航。 比如，你可以这样映射一些简单的东西：“username”，或者映射到一些复杂的东西上：“address.street.number”。|
|javaType|	一个 Java 类的完全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。|
|jdbcType	|JDBC 类型，所支持的 JDBC 类型参见这个表格之前的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可能存在空值的列指定这个类型。|
|typeHandler|	我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的完全限定名，或者是类型别名。|


关联的嵌套 Select 查询

|属性|	描述|
|:----:|:----:|
|column	|数据库中的列名，或者是列的别名。一般情况下，这和传递给 resultSet.getString(columnName) 方法的参数一样。 注意：在使用复合主键的时候，你可以使用 column="{prop1=col1,prop2=col2}" 这样的语法来指定多个传递给嵌套Select 查询语句的列名。这会使得 prop1 和 prop2 作为参数对象，被设置为对应嵌套 Select 语句的参数。|
|select	|用于加载复杂类型属性的映射语句的 ID，它会从 column 属性指定的列中检索数据，作为参数传递给目标 select 语句。 具体请参考下面的例子。注意：在使用复合主键的时候，你可以使用 column="{prop1=col1,prop2=col2}" 这样的语法来指定多个传递给嵌套 Select 查询语句的列名。这会使得 prop1 和 prop2 作为参数对象，被设置为对应嵌套 Select 语句的参数。|
|fetchType|	可选的。有效值为 lazy 和 eager。 指定属性后，将在映射中忽略全局配置参数 lazyLoadingEnabled，使用属性的值。|

eg:
```xml
<resultMap id="blogResult" type="Blog">
  <association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectAuthor" resultType="Author">
  SELECT * FROM AUTHOR WHERE ID = #{id}
</select>
```

这样做虽然简单，但是在大型数据集或者大型数据表上表现不佳。N+1查询问题。


关联的嵌套结果映射


















 
### 2.3 SQL Mapper Annotation
SQL Mapper Annotation是Java Annotation的方式替代SQL Mapper XML配置文件。


## 3、Mybatis Generator自动化工具

## 4、Mybatis与Springboot




MyBatis ：框架简介、核心API说明
配置 ：全局XML配置、SQL Mapper XML 配置、SQL Mapper Annotation
自动生成器 ：实例讲解代码、配置自动生成
Spring Boot 整合：整合 mybatis-spring-boot-starter