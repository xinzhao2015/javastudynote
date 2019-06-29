# SpringDataJPA

<!-- toc -->

## SpringDataJPA概述

Spring Data JPA让开发者摆脱了DAO层的操作,基本上所有的CRUD都可以依赖于它实现,实际工作中推荐使用Spring Data JPA+ORM(如hibernate)完成操作

使用了SpringDataJPA,在dao层中只需要写接口,就自动具有了增删改查,分页查询等方法

## 搭建Spring Data JPA的开发环境

### pom依赖

```xml
	<properties>
        <spring.version>4.2.4.RELEASE</spring.version>
        <hibernate.version>5.0.7.Final</hibernate.version>
        <slf4j.version>1.6.6</slf4j.version>
        <log4j.version>1.2.12</log4j.version>
        <c3p0.version>0.9.1.2</c3p0.version>
        <mysql.version>5.1.6</mysql.version>
    </properties>

    <dependencies>
        <!-- junit单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
            <scope>test</scope>
   		</dependency>
        
        <!-- spring beg -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.6.8</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
   		<!-- spring end -->

        <!-- hibernate beg -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.2.1.Final</version>
        </dependency>
        <!-- hibernate end -->

        <!-- c3p0 beg -->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>${c3p0.version}</version>
        </dependency>
        <!-- c3p0 end -->

        <!-- log end -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>

        <dependency>
        	<groupId>org.slf4j</groupId>
 			<artifactId>slf4j-log4j12</artifactId>
        	<version>${slf4j.version}</version>
        </dependency>
        <!-- log end -->

        
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-jpa</artifactId>
            <version>1.9.0.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
        
        <!-- el beg 使用spring data jpa 必须引入 -->
        <dependency>  
            <groupId>javax.el</groupId>  
            <artifactId>javax.el-api</artifactId>  
            <version>2.2.4</version>  
        </dependency>  
          
        <dependency>  
            <groupId>org.glassfish.web</groupId>  
            <artifactId>javax.el</artifactId>  
            <version>2.2.4</version>  
        </dependency> 
        <!-- el end -->
    </dependencies>

```

### 整合Spring Data JPA与Spring

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/data/jpa
		http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

    <!--spring 和 spring data jpa的配置-->

    <!-- 1.创建entityManagerFactory对象交给spring容器管理-->
    <bean id="entityManagerFactoty" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!--配置的扫描的包（实体类所在的包） -->
        <property name="packagesToScan" value="com.zhao.domain" />
        <!-- jpa的实现厂家 -->
        <property name="persistenceProvider">
            <bean class="org.hibernate.jpa.HibernatePersistenceProvider"/>
        </property>

        <!--jpa的供应商适配器 -->
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
                <!--配置是否自动创建数据库表 -->
                <property name="generateDdl" value="false" />
                <!--指定数据库类型 -->
                <property name="database" value="MYSQL" />
                <!--数据库方言：支持的特有语法 -->
                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect" />
                <!--是否显示sql -->
                <property name="showSql" value="true" />
            </bean>
        </property>

        <!--jpa的方言 ：高级的特性 -->
        <property name="jpaDialect" >
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect" />
        </property>

    </bean>

    <!--2.创建数据库连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"></property>
        <property name="password" value="111111"></property>
        <property name="jdbcUrl" value="jdbc:mysql:///jpa" ></property>
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    </bean>

    <!--3.整合spring dataJpa-->
    <jpa:repositories base-package="com.zhao.dao" transaction-manager-ref="transactionManager"
                   entity-manager-factory-ref="entityManagerFactoty" ></jpa:repositories>

    <!--4.配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactoty"></property>
    </bean>

    <!-- 4.txAdvice-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="save*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!-- 5.aop-->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.zhao.service.*.*(..))" />
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut" />
    </aop:config>


    <!--5.声明式事务 -->

    <!-- 6. 配置包扫描-->
    <context:component-scan base-package="com.zhao" ></context:component-scan>
    <!--组装其它 配置文件-->
</beans>
```

### 配置实体类的映射关系

## 使用SpringDataJPA完成需求

### 编写复合SpringDataJPA规范的Dao层接口

1.创建一个Dao层接口,并实现JpaRepository和JpaSpecificationExecutor

2.提供相应的泛型

### 完成基本的CRUD操作

建立测试类,完成增删改查

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:applicationContext.xml")
public class CustomerTest {
    //增
    //删
    //改
    //查
}
```

## Spring Data JPA内部原理

代理子类通过动态代理创建了一个含有增删改查的实现类



## 使用JPQL,SQL的方式查询

### JPQL方式

在继承了JpaRepository和JpaSpecificationExecutor中的接口中使用

```java
public interface CustomerDao extends JpaRepository<Customer,Long>,JpaSpecificationExecutor<Customer> {    
    //@Query 使用jpql的方式查询。
    @Query(value="from Customer")
    public List<Customer> findAllCustomer();
    
    //@Query 使用jpql的方式查询。?1代表参数的占位符，其中1对应方法中的参数索引
    @Query(value="from Customer where custName = ?1")
    public Customer findCustomer(String custName);
    
    @Query(value="update Customer set custName = ?1 where custId = ?2")
    @Modifying
    public void updateCustomer(String custName,Long custId);

}

```

### SQL方式

```java
	/**
    * nativeQuery : 使用本地sql的方式查询
    */
    @Query(value="select * from cst_customer",nativeQuery=true)
    public void findSql();

```

### 方法命名规则查询

|                   |                                          |                                                              |
| ----------------- | :--------------------------------------: | ------------------------------------------------------------ |
| **Keyword**       |                **Sample**                | **JPQL**                                                     |
| And               |        findByLastnameAndFirstname        | …   where x.lastname = ?1 and x.firstname = ?2               |
| Or                |        findByLastnameOrFirstname         | …   where x.lastname = ?1 or x.firstname = ?2                |
| Is,Equals         | findByFirstnameIs, findByFirstnameEquals | …   where x.firstname = ?1                                   |
| Between           |          findByStartDateBetween          | …   where x.startDate between ?1 and ?2                      |
| LessThan          |            findByAgeLessThan             | …   where x.age < ?1                                         |
| LessThanEqual     |          findByAgeLessThanEqual          | …   where x.age ⇐ ?1                                         |
| GreaterThan       |           findByAgeGreaterThan           | …   where x.age > ?1                                         |
| GreaterThanEqual  |        findByAgeGreaterThanEqual         | …   where x.age >= ?1                                        |
| After             |           findByStartDateAfter           | …   where x.startDate > ?1                                   |
| Before            |          findByStartDateBefore           | …   where x.startDate < ?1                                   |
| IsNull            |             findByAgeIsNull              | …   where x.age is null                                      |
| IsNotNull,NotNull |           findByAge(Is)NotNull           | …   where x.age not null                                     |
| Like              |           findByFirstnameLike            | …   where x.firstname like ?1                                |
| NotLike           |          findByFirstnameNotLike          | …   where x.firstname not like ?1                            |
| StartingWith      |       findByFirstnameStartingWith        | …   where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith        |        findByFirstnameEndingWith         | …   where x.firstname like ?1 (parameter bound with prepended %) |
| Containing        |        findByFirstnameContaining         | …   where x.firstname like ?1 (parameter bound wrapped in %) |
| OrderBy           |       findByAgeOrderByLastnameDesc       | …   where x.age = ?1 order by x.lastname desc                |
| Not               |            findByLastnameNot             | …   where x.lastname <> ?1                                   |
| In                |       findByAgeIn(Collection ages)       | …   where x.age in ?1                                        |
| NotIn             |      findByAgeNotIn(Collection age)      | …   where x.age not in ?1                                    |
| TRUE              |            findByActiveTrue()            | …   where x.active = true                                    |
| FALSE             |           findByActiveFalse()            | …   where x.active = false                                   |
| IgnoreCase        |        findByFirstnameIgnoreCase         | …   where UPPER(x.firstame) = UPPER(?1)                      |



## Specifications动态sql

### 模糊查询

```java
//使用匿名内部类的方式,创建一个Specification的实现类,并实现toPredicate方法
@Test
public void testSpecifications() {
    //使用匿名内部类的方式，创建一个Specification的实现类，并实现toPredicate方法
    Specification <Customer> spec = new Specification<Customer>() {
        public Predicate toPredicate(Root<Customer> root,
                                     CriteriaQuery<?> query, CriteriaBuilder cb) {
            //cb:构建查询，添加查询方式   like：模糊匹配
            //root：从实体Customer对象中按照custName属性进行查询
            return cb.like(root.get("custName").as(String.class), "传智播客%");
        }
    };
    Customer customer = customerDao.findOne(spec);
    System.out.println(customer);
}

```

### 分页查询

```java
//关键语句 分页参数Pageable pageable = new PageRequest(0, 5);

//其他语句同上
Pageable page = new PageRequest(0, 10);

Page<TbCustomer> resultPage = customerDao.findAll(specification, page);
System.out.println(resultPage);
```

### cb方法与对应字段

| 方法名称                    | Sql对应关系          |
| --------------------------- | -------------------- |
| equle                       | filed = value        |
| gt（greaterThan ）          | filed > value        |
| lt（lessThan ）             | filed < value        |
| ge（greaterThanOrEqualTo ） | filed >= value       |
| le（ lessThanOrEqualTo）    | filed <= value       |
| notEqule                    | filed != value       |
| like                        | filed like value     |
| notLike                     | filed not like value |



## 多表操作

### 在JPA中表关系的分析步骤

- 首先确定两张表之间的关系(一对一,一对多,多对一,多对多)
- 在数据库中实现两张表的关系
- 在实体类中描述两个实体的关系
- 配置出实体类和数据库表的关系映射(重点)

### JPA中的一对多

#### 示例分析

客户:指一家公司,记为A

联系人:指的是A公司中的员工

公司和员工的关系即为一对多

#### 表关系建立

在一对多中,把一的一方称为主表,把多的一方称为从表,建立一对多的关系,需要使用数据库的外键约束

让联系人表引用客户表的cid作为外键

#### 实体类关系的建立以及映射配置

```java
//一的pojo类
//配置客户和联系人的一对多关系
@OneToMany(targetEntity=LinkMan.class)
@JoinColumn(name="lkm_cust_id",referencedColumnName="cust_id")
private Set<LinkMan> linkmans = new HashSet<LinkMan>(0);

//多的pojo类
//多对一关系映射：多个联系人对应客户
@ManyToOne(targetEntity=Customer.class)
@JoinColumn(name="lkm_cust_id",referencedColumnName="cust_id")
private Customer customer;//用它的主键，对应联系人表中的外键

```

#### 映射的注解说明

```java
@OneToMany:
   	作用：建立一对多的关系映射
    属性：
    	targetEntityClass：指定多的多方的类的字节码
    	mappedBy：指定从表实体类中引用主表对象的名称。
    	cascade：指定要使用的级联操作
    	fetch：指定是否采用延迟加载
    	orphanRemoval：是否使用孤儿删除(不再具有被约束的实体关系)
    	
@ManyToOne
    作用：建立多对一的关系
    属性：
    	targetEntityClass：指定一的一方实体类字节码
    	cascade：指定要使用的级联操作
    	fetch：指定是否采用延迟加载
    	optional：关联是否可选。如果设置为false，则必须始终存在非空关系。

@JoinColumn
     作用：用于定义主键字段和外键字段的对应关系。
     属性：
    	name：指定外键字段的名称
    	referencedColumnName：指定引用主表的主键字段名称
    	unique：是否唯一。默认值不唯一
    	nullable：是否允许为空。默认值允许。
    	insertable：是否允许插入。默认值允许。
    	updatable：是否允许更新。默认值允许。
    	columnDefinition：列的定义信息。

```

#### 一对多的操作

注意:

mappedBy后的值为多的pojo里持有的变量名

多的pojo类里 外键不作为一个单独的成员变量

##### 一对多的添加

```java
/**
     * 需求:
     * 保存一个客户,一个联系人
     * 步骤:
     * 创建一个客户,一个联系人
     * 修改客户里的联系人
     * 会产生两条新建 一条更新
     *
     * 实际上不需要那一条更新
     * 解决办法为
     * 一的一方放弃外键的维护权
     * 具体做法为一的一方修改注解为
     * @OneToMany(mappedBy="customer")
     * mappedBy后的值为多的pojo里持有的变量名(重点)
     */
    
    @Test
    @Transactional
    @Rollback(false)
    public void testAddCustomer(){
        TbCustomer customer = new TbCustomer();
        customer.setCustName("一对多的主表");
        TbLinkMan linkMan = new TbLinkMan();
        linkMan.setLkmName("一对多的从表");
  	    linkMan.setTbCustomer(customer);
        customer.getLinkmans().add(linkMan);

        customerDao.save(customer);
        linkManDao.save(linkMan);
    }

	/**
     * 级联添加 不用保存linkManDao
     * 需要在一的pojo中的
     * @OneToMany配置 cascade = CascadeType.ALL
     */
    @Test
    @Transactional
    @Rollback(false)
    public void testAddCustomer1(){
        TbCustomer customer = new TbCustomer();
        customer.setCustName("一对多的主表1");
        TbLinkMan linkMan = new TbLinkMan();
        linkMan.setLkmName("一对多的从表1");

        linkMan.setTbCustomer(customer);
        customer.getLinkmans().add(linkMan);

        customerDao.save(customer);
    }

```

##### 一对多的删除

```java
删除从表数据:
	随便删
	
删除主表数据:
	没有从表数据:
		随便删

	有从表数据:
		默认情况下,它会把外键字段置为null,然后删除主表数据,如果在数据库的表结构上
		,外键字段有非空约束,默认情况报错
		
		如果配置了放弃维护关联关系的权利,则不能删除(与外键字段是否允许为null没有关系),
		因为在删除时,它根本不会去更新从表的外键字段了
		
		如果还想删除,使用级联删除引用
```

**在实际开发中,级联删除请慎用!!!(在一对多的情况下)**

###### 级联删除

```java
	/**
	 * cascade:配置级联操作
	 * 		CascadeType.MERGE	级联更新
	 * 		CascadeType.PERSIST	级联保存：
	 * 		CascadeType.REFRESH 级联刷新：
	 * 		CascadeType.REMOVE	级联删除：
	 * 		CascadeType.ALL		包含所有
	 */
	@OneToMany(mappedBy="customer",cascade=CascadeType.ALL)

```

### JPA中的多对多

#### 建表

```sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(32) NOT NULL COMMENT '用户名称',
  `birthday` datetime DEFAULT NULL COMMENT '生日',
  `sex` char(1) DEFAULT NULL COMMENT '性别',
  `address` varchar(256) DEFAULT NULL COMMENT '地址',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=51 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC;

CREATE TABLE `role` (
  `ID` int(11) NOT NULL COMMENT '编号',
  `ROLE_NAME` varchar(30) DEFAULT NULL COMMENT '角色名称',
  `ROLE_DESC` varchar(60) DEFAULT NULL COMMENT '角色描述',
  PRIMARY KEY (`ID`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC;

CREATE TABLE `user_role` (
  `UID` int(11) NOT NULL COMMENT '用户编号',
  `RID` int(11) NOT NULL COMMENT '角色编号',
  PRIMARY KEY (`UID`,`RID`) USING BTREE,
  KEY `FK_Reference_10` (`RID`) USING BTREE,
  CONSTRAINT `FK_Reference_10` FOREIGN KEY (`RID`) REFERENCES `role` (`ID`),
  CONSTRAINT `FK_Reference_9` FOREIGN KEY (`UID`) REFERENCES `user` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC;
```

#### 映射关系

```java
Role实体类中

//多对多关系映射
@ManyToMany(mappedBy = "roles",fetch=FetchType.EAGER)
private Set<User> users=new HashSet<>(0);

User实体类中
//多对多关系映射
 /**
     * 多对多的另一个实体,cascade级联操作的取值 CascadeType.ALL所有的级联操作
     * name:中间表的表名
     * joinColumns: 当前对象在中间表的外键
     * inverseJoinColumns:另一个对象在中间表的外键
     * JoinColumn中的name中间表对应的列表
     */
    @ManyToMany(targetEntity = Role.class,cascade = CascadeType.ALL,fetch=FetchType.EAGER)
    @JoinTable(name = "user_role",
            joinColumns ={@JoinColumn(name = "uid",referencedColumnName = "id")},
            inverseJoinColumns ={@JoinColumn(name = "rid",referencedColumnName = "ID")})
```

#### 注解的意义

```java
@ManyToMany
	作用：用于映射多对多关系
	属性：
		cascade：配置级联操作。
		fetch：配置是否采用延迟加载。
    	targetEntity：配置目标的实体类。映射多对多的时候不用写。

@JoinTable
    作用：针对中间表的配置
    属性：
    	nam：配置中间表的名称
    	joinColumns：中间表的外键字段关联当前实体类所对应表的主键字段			  			
    	inverseJoinColumn：中间表的外键字段关联对方表的主键字段
    	
@JoinColumn
    作用：用于定义主键字段和外键字段的对应关系。
    属性：
    	name：指定外键字段的名称
    	referencedColumnName：指定引用主表的主键字段名称
    	unique：是否唯一。默认值不唯一
    	nullable：是否允许为空。默认值允许。
    	insertable：是否允许插入。默认值允许。
    	updatable：是否允许更新。默认值允许。
    	columnDefinition：列的定义信息。

```

#### 添加

```java
 @Test
    public void testAddUser(){
        User user = new User();
        user.setUsername("用户一");

        Role role = new Role();
        role.setRoleName("角色一");
        role.setRoleDesc("角色一的权限");

        user.getRoles().add(role);
        role.getUsers().add(user);

	    //注意 这里执行保存步骤的是 配置中间表关系的实体类
        userDao.save(user);

    }
```

#### 删除

```java
  /**
     * 删除操作
     *  有效可以用
     * 	在多对多的删除时，,双向级联删除根本可以配置
     *  但是在实际开发中禁用
     *	如果配了的话，如果数据之间有相互引用关系，可能会清空所有数据
     *
     * 如下的执行结果是user user_role role 所有关联数据全部删除 
     */

    @Test
    public void testDeleteUser(){
        User user = userDao.findOne(60);
        userDao.delete(user);
    }
```

#### 懒加载的配置

```java
实体类中配置
User中
@ManyToMany(targetEntity = Role.class,cascade = CascadeType.ALL,fetch=FetchType.EAGER)

Role中
@ManyToMany(mappedBy = "roles",fetch=FetchType.EAGER)
```

####  使用Specification查询

```java
例如要通过role查询指定姓名的user
1.创建匿名内部类Specification
2.Join<Role, User> join  join选择连接方式
//JoinType.LEFT : 左外连接,JoinType.INNER：内连接,JoinType.RIGHT：右外连接
3.cb执行模糊查询


 @Test
    @Transactional
    public void testSpecificationByName(){
       Specification<Role> specification = new Specification<Role>() {
           @Override
           public Predicate toPredicate
                   (Root<Role> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder cb) {
               Join<Role, User> join = root.join("users", JoinType.INNER);
               return cb.like(join.get("username"),"%王%");

           }
       };

        List<Role> list = roleDao.findAll(specification);
        System.out.println(list);
    }
```

