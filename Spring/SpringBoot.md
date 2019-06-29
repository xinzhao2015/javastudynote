# SpringBoot

<!-- toc -->

## SpringBoot的配置

### 手动建立一个SpringBoot工程

```
1. 建立一个java工程

2. pom中添加起步依赖,spring-boot-starter-parent

3. 启动springmvc,添加,spring-boot-starter-web

4. 添加启动类

@SpringBootApplication

SpringApplication.class

SpringApplication.run(MySpringBootApplication.class);

5. 添加Controller类并测试

```

### 热部署的设置

```
1.pom中添加spring-boot-devtools

2.在setting-compiler里设置ideal的自动编译

3.Shift+Ctrl+Alt+/ 注册automake

```



 

### Ideal快速创建SpringBoot

```
Spring Initializr 里设置进行SpringBoot的初始化,创建后可以看见pom已经配置了选中的依赖
```

 

### SpringBoot的注解

```

@SpringBootConfiguration 等同于@Configuration,即标注该类是Spring的一个配置类

@EnableAutoConfiguration SpringBoot自动配置功能开启

```

 

### .yml 或者.properties配置文件

```
spring默认加载resources下的application.properties或者application.yml

查询SpringBoot的配置信息可以查阅

https://docs.spring.io/spring-boot/docs/2.0.1.RELEASE/reference/htmlsingle/#common-applicationproperties

 
例如修改SpringBoot的端口和context-path

server:

       prot:8888

       servlet:

              context-path:/demo


```

### 配置文件和配置类的属性映射方式

#### @value注解

```
在成员变量中使用

@Value("${person.name}") 

private String name;

```

#### @ConfigurationProperties注解

使用@ConfigurationProperties(prefix="配置文件中的key的前缀")

```
在类名上使用@ConfigurationProperties(prefix = "person")
```

 

## SpringBoot整合其他技术

### SpringBoot整合mybatis

#### pom中

```
mybatis-spring-boot-starter

mysql-connector-java
```



 

#### application.yml或者application.properties中

```
#DB Configuration:

spring.datasource.driverClassName=com.mysql.jdbc.Driver

spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?

useUnicode=true&characterEncoding=utf8

spring.datasource.username=root

spring.datasource.password=root



#spring集成Mybatis环境

#pojo别名扫描包

mybatis.type-aliases-package=com.itheima.domain

#加载Mybatis映射文件

mybatis.mapper-locations=classpath:mapper/*Mapper.xml

```





#### 测试验证

```
创建xxxMapper.xml,使用@Mapper注解

```

 

 

### SpringBoot整合Jutil

```
pom中添加依赖

spring-boot-starter-test

 

建立的测试类中配置

@RunWith(SpringRunner.class)

@SpringBootTest(classes = MySpringBootApplication.class)

 

SpringBoot整合SpringDataJPA

pom中添加

   spring-boot-starter-data-jpa

 

   如果是jdk9则需要添加依赖

   <groupId>javax.xml.bind</groupId>

<artifactId>jaxb-api</artifactId>

 

在yml中配置

#JPA Configuration:

spring.jpa.database=MySQL

spring.jpa.show-sql=true

spring.jpa.generate-ddl=true

spring.jpa.hibernate.ddl-auto=update

#这一句已经过时

spring.jpa.hibernate.naming_strategy=org.hibernate.cfg.ImprovedNamingStrategy

```



### SpringBoot中配置redis

```
pom中配置

<groupId>org.springframework.boot</groupId>

<artifactId>spring-boot-starter-data-redis</artifactId>

yml中配置

#Redis

spring.redis.host=127.0.0.1

spring.redis.port=6379



测试类中使用RedisTemplate进行测试

```



 

 

 

 