# springMVC

## 1.servlet环境搭建

**目录结构**

![image-20200102140532074](.\images\image-20200102140532074.png)

**添加web的支持**

![image-20200102150228219](.\images\image-20200102150228219.png)

**maven工程依赖**

1. servlet-api
2. jsp-api

```.xml
<dependencies>
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
    </dependencies>
```

helloServlet.java

```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取前端参数
        String method = req.getParameter("method");
        if ("add".equals(method)){
            req.getSession().setAttribute("msg","执行了add方法");
        }
        if ("delete".equals(method)){
            req.getSession().setAttribute("msg","执行了delete方法");
        }

        //业务逻辑
        //todo
        //视图跳转 转发 或者重定向
        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<!--    servlet配置-->
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.cevlink.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>

<!--    session配置-->
    <session-config>
        <!--15分钟-->
        <session-timeout>15</session-timeout>
    </session-config>

<!--    欢迎页配置-->
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
```

test.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>hello</title>
</head>
<body>
${msg}
</body>
</html>
```

index.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
  $END$
  </body>
</html>
```

**Tomcat配置**

1. 本地解压tomcat.zip，添加到环境变量：D:\apache-tomcat-9.0.1\bin
2. ieda 添加tomcat配置

![image-20200102141020327](.\images\image-20200102141020327.png)

![image-20200102141401715](.\images\image-20200102141401715.png)

![image-20200102141520044](.\images\image-20200102141520044.png)

**开启项目**

![image-20200102141717123](.\images\image-20200102141717123.png)

**http请求测试**

![image-20200102141834972](.\images\image-20200102141834972.png)

添加请求参数

![image-20200102141938216](.\images\image-20200102141938216.png)

![image-20200102142015705](.\images\image-20200102142015705.png)

**MVC框架需要做那些事情**

1. 将URL映射到java类或java类的方法
2. 封装用户提交的数据
3. 处理请求=>调用相关的业务处理=>封装响应的数据
4. 将响应的数据进行渲染 jsp/html等表示数据

常见服务端MVC框架：struts  、spring MVC、ASP.NET MVC、Zend Framework JSF

常见前端MVC框架：Vue、angularjs、react

MVC演化进阶模式：MVP、MVVM等



## 2.springMVC

在线官方文档：https://docs.spring.io/spring/docs/4.3.25.RELEASE/spring-framework-reference/htmlsingle/#mvc

基于java的web轻量级MVC框架，约定优于配置。

功能强大：RESTful风格、数据验证、格式化、本地化、主题等

### 1.DispatcherServlet（调度servlet设计）

DispatcherServlet还是一个httpServlet

![image-20200102145129547](.\images\image-20200102145129547.png)

### 2.springMVC请求处理工作流

![mvc](.\images\mvc.png)

1.前端发起请求=>前端控制器 (DispatcherServlet)

2.委托请求给页面控制/处理器

3.调用业务对象=>模型

4.模型=>返回模型数据

5.返回ModelAndView

6.渲染视图

7.返回控制

8.输出响应



### 3.Spring Web MVC中的典型上下文层次结构

![mvc context hierarchy](.\images\mvc-context-hierarchy.png)

### 4.Spring Web MVC环境搭建

目录结构

![image-20200102153617960](.\images\image-20200102153617960.png)

maven依赖

```xml
<dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>

        <!-- jsp标签-->
        <!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>
```

web环境配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<!--    注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<!--        关联springmvc的配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

resources目录下配置springMVC的配置

springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

<!--    <context:component-scan base-package="org.springframework.samples.petclinic.web"/>-->

    <!-- 配置springmvc -->
    <!--    处理映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <!--处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    
    <!--    视图解析器 DispatcherServlet给他的ModelAndView
        1.获取ModelAndView的数据
        2.解析view的名字
        3.拼接视图名字，找到对应的视图
        4.将视图渲染
    -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--        前缀和后缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--  BeanNameUrlHandlerMapping 需要配置控制器的bean  自定义的controller  id及访问的url-->
    <bean id="/hello" class="com.cevlink.controller.HelloController"/>
</beans>
```

HelloController.java

```java
public class HelloController implements Controller {

    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {

        ModelAndView mv = new ModelAndView();

        mv.addObject("msg","hellospringmvc");

        mv.setViewName("hello");  //自动拼接 /WEB-INF/jsp/hello.jsp

        return mv;
    }
}
```

404问题：

打包时添加相关的jar包

![image-20200102154145231](.\images\image-20200102154145231.png)

![image-20200102154220083](.\images\image-20200102154220083.png)

### 5.深入springMVC

1. 配置DispatcherServlet 

   ​	springMVC的核心：请求分发器，前端控制器

2. contextConfigLocation 本地上下文配置：springmvc-servlet.xml

3. springmvc-servlet.xml

   ​	处理器映射

   ​	处理器适配

   ​	视图解析器

4. 控制器 （业务处理）

   ​    implements Controller

   ```java
   @FunctionalInterface
   public interface Controller {
       @Nullable
       ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
   }
   ```

   ModelAndView

   ![image-20200102161258698](.\images\image-20200102161258698.png)

5. 视图

实际开发不用这么写，配置麻烦，使用注解版是springMVC的精髓。



### 6.注解版本springMVC 

1. 配置DispatcherServlet 

   ​	springMVC的核心：请求分发器，前端控制器

2. contextConfigLocation 本地上下文配置：springmvc-servlet.xml

   - 扫包支持

   ```xml
    <!-- 自动扫描包 让指定下的注解生效，由ioc容器统一管理-->
       <context:component-scan base-package="com.cevlink.controller"/>
   ```

   - mvc默认处理器

   ```xml
   <!--  让spring MVC不处理静态资源 .css等  -->
       <mvc:default-servlet-handler />
   ```

   - mvc注解驱动支持

   ```xml
   <!--  开启mvc注解支持 -->
   <mvc:annotation-driven/>
   ```

   - 内部资源视图解析器

   ```xml
   <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   ```

3. 编写控制器

4. 编写jsp页面

   

   web.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
       <servlet>
           <servlet-name>dispatcherServlet</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
       </servlet>
       <servlet-mapping>
           <servlet-name>dispatcherServlet</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   </web-app>
   ```

   springmvc-servlet.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:p="http://www.springframework.org/schema/p"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
       <!-- 自动扫描包 让指定下的注解生效，由ioc容器统一管理-->
       <context:component-scan base-package="com.cevlink.controller"/>
   
       <!--  让spring MVC不处理静态资源 .css等  -->
       <mvc:default-servlet-handler />
   
       <!-- Spring3.1之前的注解 HandlerMapping -->
   <!--    <bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping"/>-->
   
       <!-- Spring3.1之前的注解 HandlerAdapter -->
   <!--    <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter"/>-->
   
   
       <!--  开启mvc注解支持
              @RequestMapping完成映射
              要使@RequestMapping生效，
              必须向上下文中注册DefaultAnnotationHandlerMapping和一个annotationMethodHandlerAdopter实例，
              这2个实例分别在类级别和方法级别处理。
              default-servlet-handler配置自动完成上述2个实例的注入
         -->
       <mvc:annotation-driven/>
   
       <!--  内部资源视图解析器   -->
       <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   
       <!-- ... -->
   
   </beans>
   ```

   编写控制器注解版本

   HelloController.java

   ```java
   package com.cevlink.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   /**
    * Created on 2020/1/2.
    *
    * @author yc
    * @desc
    */
   @Controller
   @RequestMapping("/hello")
   public class HelloController {
   
       @RequestMapping("/h1")
       public String hello(Model model){
   
           model.addAttribute("msg","hello mvc annotation!");
   
           return "hello";
       }
   
   }
   ```

   

   hello.jsp

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
       ${msg}
   </body>
   </html>
   
   ```

   项目目录

   ![image-20200102172600827](.\images\image-20200102172600827.png)

   测试结果：

   ![image-20200102172705973](.\images\image-20200102172705973.png)

注解：

```java
@RequestMapping(value = "/h1",method = RequestMethod.GET)
//简写
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
```

### 7.重定向和转发

HttpServletRequest，HttpServletResponse可以直接使用。

![image-20200103094740423](.\images\image-20200103094740423.png)

springmvc-servlet.xml 注掉视图解析器的配置 InternalResourceViewResolver，控制器可以使用原生的拼接。

1. 返回全路径 转发

   ```java
       public String hello(Model model){
           model.addAttribute("msg","hello mvc annotation!");
   //        return "hello";
           return "/WEB-INF/jsp/hello.jsp";
       }
   ```

   

2. 转发（显示说明 forward）

   ```java
       public String hello(Model model){
           model.addAttribute("msg","hello mvc annotation!");
   //        return "hello";
           return "forward:/WEB-INF/jsp/hello.jsp";
       }
   ```

   

3. 重定向

   注解掉视图解析器和不注掉视图解析器一样。

   ```java
       public String hello(Model model){
           model.addAttribute("msg","hello mvc annotation!");
   //        return "hello";
   //        return "/WEB-INF/jsp/hello.jsp";
   //        return "forward:/WEB-INF/jsp/hello.jsp";
           return "redirect:/index.jsp    }
   ```

### 8.数据处理

1. 接收请求参数

   - 直接接收参数

     ```java
         @RequestMapping(value = "/h2",method = RequestMethod.GET)
         public String h2(Model model,String name){
     
             model.addAttribute("msg","接收的参数："+name);
             return "hello";
         }
     ```

     ![image-20200103101504476](.\images\image-20200103101504476.png)

   - 指定参数名@RequestParam

     ```java
         @RequestMapping(value = "/h3",method = RequestMethod.GET)
         public String h3(Model model,@RequestParam("userName") String name){
             model.addAttribute("msg","接收的参数："+name);
             return "hello";
         }
     ```

     ![image-20200103104451316](.\images\image-20200103104451316.png)

   - 包装参数实体

     

2. 数据回显