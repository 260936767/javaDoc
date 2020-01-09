## ssm整合

### 1.工程搭建

#### 1.1创建普通maven工程

![image-20200108170705698](.\images\image-20200108170705698.png)

#### 1.2 导入依赖

```xml
<!--
    依赖问题：
        junit,数据库驱动，连接池，servlet,jsp,mybatis,mybatis-spring,spring
    静态资源过滤问题 -->
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <!-- dbcp  durid  hribx -->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>

        <!--Servlet - JSP -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.2</version>
        </dependency>

        <!--Spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
    </dependencies>

    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
```

#### 1.3 配置为web工程

![image-20200108170932287](.\images\image-20200108170932287.png)

#### 1.4 创建响应的包，具备基本骨架

![image-20200108171100597](.\images\image-20200108171100597.png)

### 2.mybatis配置(dao层)

#### 2.1 resource下创建mybatis的配置文件

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>
```

需要配置内容

- 别名包

- 注册mapper.xml

  ```xml
  <!--    别名-->
      <typeAliases>
          <package name="com.cevlink.pojo"/>
      </typeAliases>
  
  <!--    注册mapper.xml文件-->
      <mappers>
          <mapper class="com.cevlink.dao.BookMapper"/>
      </mappers>
  ```

#### 2.2 dao层完善

- pojo

  ```java
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  public class Books {
  
      private int bookID;
      private String bookName;
      private int bookCounts;
      private String detail;
  }
  ```

  

- dao接口

  ```java
  public interface BookMapper {
  
      List<Books> queryAllBook();
  
      int addBook(Books books);
  
      int deleteBookById(@Param("bookID") int id);
  
      Books queryBookById(@Param("bookID") int id);
  
      int updateBook(Books books);
  }
  ```

  

- mapper.xml

  将configuration/config更改为mapper即可

  ![image-20200108175353742](.\images\image-20200108175353742.png)

  注意namespace 为指定的mapper.xml文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.cevlink.dao.BookMapper">
      <insert id="addBook" parameterType="Books">
          insert into ssmbuild.books (bookName, bookCounts, detail)
          values (#{bookName},#{bookCounts},#{detail});
      </insert>
  
      <select id="queryAllBook" resultType="Books">
          select * from ssmbuild.books;
      </select>
  
      <delete id="deleteBookById" parameterType="int">
          delete from ssmbuild.books where bookID =#{bookID}
      </delete>
  
      <update id="updateBook" parameterType="Books">
          update ssmbuild.books set bookName=#{bookName},bookCounts=#{bookCounts}, detail = ${detail} where bookID =#{bookID} ;
      </update>
  
      <select id="queryBookById" parameterType="int" resultType="Books">
          select * from ssmbuild.books where bookID=#{bookID};
      </select>
  </mapper>
  ```

  至此，dao层处理完毕。

  后面将mybatis和spring整合。

### 3.spring-dao整合

#### 3.1 ApplicationContext管理配置文件

spring-dao.xml，需要spring来管理 ApplicationContext。spring bean的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

![image-20200108180237537](.\images\image-20200108180237537.png)

![image-20200108180327383](.\images\image-20200108180327383.png)

#### 3.2 spring-dao.xml配置

- 关联数据库配置

database.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
# 8.0以上 要设置时区 serverTimezone=Asia/Shanghai
jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8&serv
jdbc.username=root
jdbc.password=123456
```

```xml
<!--关联数据库配置文件-->
    <context:property-placeholder location="classpath:database.properties"/>
```

- 数据连接池

![image-20200108181951192](.\images\image-20200108181951192.png)

![image-20200108182050773](.\images\image-20200108182050773.png)

```xml
<!--    连接池 c3p0 \ hikari-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        
        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false"/>
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000"/>
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2"/>
        
    </bean>
```

- sqlsessionFactory

  ```xml
  <!-- 2.sqlsessionFactory mybatis核心bean  -->
      <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
          <!--数据源-->
          <property name="dataSource" ref="dataSource"/>
          <!-- 绑定mybatis的配置 -->
          <property name="configLocation" value="classpath:mybatis-config.xml"/>
          <!-- 这句配置mapper配置文件的位置 如果采用注解的方式这句可以省去 -->
          <!-- <property name="mapperLocations" value="classpath:XXX/mapper/*.xml"/>-->
      </bean>
  ```

  

- dao接口注入spring容器

  ```xml
  <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
          <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
          <!-- <property name="sqlSessionTemplate" value="sqlSessionFactory"/>-->
          <property name="basePackage" value="com.cevlink.dao"/>
      </bean>
  ```

整体：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 0.关联数据库配置文件-->
    <context:property-placeholder location="classpath:database.properties"/>
    <!-- 1.连接池 c3p0 \ hikari-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>

        <!-- c3p0连接池的私有属性 -->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!-- 关闭连接后不自动commit -->
        <property name="autoCommitOnClose" value="false"/>
        <!-- 获取连接超时时间 -->
        <property name="checkoutTimeout" value="10000"/>
        <!-- 当获取连接失败重试次数 -->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>

    <!-- 2.sqlsessionFactory mybatis核心bean  -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--数据源-->
        <property name="dataSource" ref="dataSource"/>
        <!-- 绑定mybatis的配置 -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!-- 这句配置mapper配置文件的位置 如果采用注解的方式这句可以省去 -->
        <!-- <property name="mapperLocations" value="classpath:XXX/mapper/*.xml"/>-->
    </bean>

    <!-- 3.配置dao接口的扫描包，动态实现dao接口可以注入spring容器中 -->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- <property name="sqlSessionTemplate" value="sqlSessionFactory"/>-->
        <property name="basePackage" value="com.cevlink.dao"/>
    </bean>

</beans>
```



### 4.spring-service.xml配置

service有事务处理，整合事务。

#### 4.1 扫包，注入spring容器（也可以使用注解 @Service）

上下文 组件扫描  context：component-scan

#### 4.2 业务类注入spring容器

<bean></bean>

#### 4.3 声明事务

DataSourceTransactionManager  spring框架的

事务拦截传播配置

#### 4.4 AOP支持

切入点和顾问

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 1.扫描service下的包 -->
    <context:component-scan base-package="com.cevlink.service"/>

    <!-- 2.将业务类注入spring容器 -->
    <bean id="bookServiceImpl" class="com.cevlink.service.impl.BookServiceImpl">
        <!-- 配置需要的属性 bookMapper在spring-dao.xml中自动扫描包中已经注入spring容器，
        spring-service.xml也在同一个ApplicationContext中 可以直接引用  -->
        <property name="bookMapper" ref="bookMapper"/>
    </bean>

    <!-- 3.申明事务 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 事务拦截 传播配置 -->
    <tx:advice id="transactionInterceptor" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="insert" propagation="SUPPORTS"/>
            <tx:method name="delete" propagation="SUPPORTS"/>
            <tx:method name="update" propagation="SUPPORTS"/>
            <tx:method name="select" propagation="SUPPORTS"/>
        </tx:attributes>
    </tx:advice>

    <!-- 4.aop事务支持 -->
    <aop:config>
        <!-- 切入点 -->
        <aop:pointcut id="serviceCut" expression="execution(public * com.cevlink.service.*.*(..))"/>
        <aop:advisor pointcut-ref="serviceCut" advice-ref="transactionInterceptor"/>
    </aop:config>
</beans>
```

手动将xml文件配置在一个applicationContext中的方式

1.工程模块引入spring的支持

2.spring支持需要定义ApplicationContext,将工程下applicationContext.xml的加入ApplicationContext

3.手动将其他xml文件导入applicationContext.xml文件中，保证了相关的xml在一个ApplicationContext中。

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--手动 将spring-dao.xml  spring-service.xml 导入 ApplicationContext中-->
    <import resource="spring-dao.xml"/>
    <import resource="spring-service.xml"/>
    <import resource="spring-mvc.xml"/>
</beans>
```

### 5. spring-mvc.xml配置

- 增加web支持

- web.xml配置servlet，编码过滤器

- spring-mvc.xml 开启映射处理器  适配器 ， 视图解析器

  spring-mvc使用注解，映射处理器  适配器不需要配置

  使用开启注解和默认的servlet来处理即可

  - schema要选择mvc的包

  ![image-20200109161517860](.\images\image-20200109161517860.png)

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- 1.dispatcherServlet -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!-- 2.characterEncodingFilter -->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!-- 3.session -->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```

spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 1. 开启注解驱动 -->
    <mvc:annotation-driven/>

    <!-- 2. 静态资源过滤 -->
    <mvc:default-servlet-handler/>

    <!-- 3. 扫描controller包 -->
    <context:component-scan base-package="com.cevlink.controller"/>

    <!-- 4. 视图解析 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

至此 ssm框架整合完毕。

### 6.排错

controller

```java
@Controller
@RequestMapping("/book")
public class BookController {

    @Autowired
    private BookServiceImpl bookServiceImpl;

    @GetMapping("/list")
    public String list(Model model){

        List<Books> books = bookServiceImpl.queryAllBook();
        model.addAttribute("list",books);

        return "all";
    }

}
```

jsp

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>all</title>
</head>
<body>
<c:forEach var="book" items="${requestScope.get('list')}">
    <a>book.bookName</a>
</c:forEach>
</body>
</html>
```



#### 6.1 找不到文件

```java
Caused by: java.io.FileNotFoundException: class path resource [ spring-dao.xml] cannot be opened because it does not exist
	at org.springframework.core.io.ClassPathResource.getInputStream(ClassPathResource.java:180)
	at org.springframework.beans.factory.xml.XmlBeanDefinitionReader.loadBeanDefinitions(XmlBeanDefinitionReader.java:330)
	... 47 more
```

applicationContext中有导入资源文件

```xml
<import resource="classpath: spring-dao.xml"/>
```

classpath：这个是class下的路径不对

更改：

```xml
    <import resource="spring-dao.xml"/>
    <import resource="spring-service.xml"/>
    <import resource="spring-mvc.xml"/>
```

#### 6.2 bean 失败

```java
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'bookController': Unsatisfied dependency expressed through field 'bookServiceImpl'; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'com.cevlink.service.impl.BookServiceImpl' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
```

思路：bookController ，BookServiceImpl 业务层，和dao层没有关系。

controller中的BookServiceImpl初始化失败，说明spring容器中找不到BookServiceImpl。

controller使用的是spring-mvc.xml容器，这个容器肯定是没有BookServiceImpl的，有BookServiceImpl的是applicationContext和spring-service容器有。

解决：在web.xml配置dispatcherServlet 初始化context指定为applicationContext.xml即可。

```xml
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
<!--            <param-value>classpath:spring-mvc.xml</param-value>-->
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
```















