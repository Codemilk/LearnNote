---

typora-root-url: ..\..\..\Pic
---

# 环境搭建

## Maven知识点补充

* Maven聚合工程：父模块的依赖写完子模块要想使用，还得自己去写入依赖
* Scope:provided，因为provided表明该包只在编译和测试的时候用，所以，当启动tomcat的时候，就不会冲突了，
* Scope:test，表示当前依赖不传递，只能在当前工程的test目录下使用



## 1.日志系统:

### 介绍

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200927201051376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)

###不同日志系统的整合

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200927202339861.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)日志的转换：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200927210045512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)
日志优点：
使用日志打印信息和使用sysout 打印信息的区别：sysout 如果不删除，那么执行到这里必然会打印；如果使用日志方式打印，可以通过志级别控制日志输出等级来控制，是否打印。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:SpringPersist.xml")
public class TestConfig {

    @Autowired
    private DataSource dataSource;

    @Autowired
    private AdminMapper adminMapper;
    @Test
    public void test1() throws SQLException {

        System.out.println(dataSource.getConnection());


//        adminMapper.insert(new Admin(null, "tom", "123123","tom", "tom@qq.com", null));
        /**
         * 若在实际开发中，所有想查看数值的地方都使用sysout方式打印，都会给项目上线运行带来问题，因为他本身是一种IO操作通常IO的操作都比较耗费性能
         * 即使你为了节省性能专门花时间删除代码sysout也可能有遗漏，而且麻烦
         * 而如果使用日志系统，那么就可以通过日志级别批量控制信息打印
         * 具体原因：https://ask.csdn.net/questions/1079638
         *
         * */
        System.out.println(adminMapper.selectByPrimaryKey(1));
    }

    @Test
    public void testLog(){
//         1.获取Logger对象,这里传入Class对象就是当前打印日志的类
        Logger logger = LoggerFactory.getLogger(TestConfig.class);

//        2.根据不同日志级别的打印日志，当我们指定打印info，info一下就不会再打印了，以此类推
        logger.debug("Hello DEBUG");

        logger.info("Hello INFO");

        logger.warn("Hello WARN");


    }
```

### logback.xml配置文件（必须是logback.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置-->
    <appender name="STDOUT"
              class="ch.qos.logback.core.ConsoleAppender">
        <!--appender:追加器
                 class：在哪里打印,ConsoleAppender类表示在控制台打印
                 name：追加器name，便于引用
        -->
        <encoder>
            <!-- 日志输出的格式-->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体
            内容、换行-->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger]
                [%msg]%n</pattern>
        </encoder>
    </appender>
    <!-- 设置全局日志级别。日志级别按顺序分别是：DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。-->
<!--     root表示全局日志输出级别-->
    <root level="INFO">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT" />
    </root>
    <!-- 根据特殊需求指定局部日志级别-->
    <logger name="Mapper" level="DEBUG"/>
<!--    -->
</configuration>
```

### 声明式事务

事务概念图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928210733310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)

思路：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928210758352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)
注意：我们的聚合工程，秉承一个原则，聚合且独立。所以我们选择在为事务专门建立一个Spring配置文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928210925682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)
xml配置方式：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

<!--配置自动扫描包-->
    <context:component-scan base-package="Service"></context:component-scan>

    <!-- 配置事务管理器-->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="dataSourceTransactionManager">
        <!--                装配数据源-->
        <property name="dataSource" ref="DruidDataSourceFactory"></property>
    </bean>

    <!--配置事务通知-->
    <tx:advice transaction-manager="dataSourceTransactionManager" id="transactionInterceptor">
        <tx:attributes >
            <!--                指定某个方法上的事务的属性
                            read-only=true:只读属性，让数据库知道这是一个查询操作，能够进行优化
             -->
            <!--                service层,配置只读  -->
            <tx:method name="get*" read-only="true"/>
            <tx:method name="find*" read-only="true"></tx:method>
            <tx:method name="query*" read-only="true"></tx:method>
            <tx:method name="count*" read-only="true"></tx:method>

            <!--            Dao层 增删改，配置事务传播行为，回滚异常
                             REQUIRED:如果当前没有当前没有事务，那么创建新的事务，如果有了事务就沿用现在事务，缺点就是：指定方法没错，但是在同一事务下的其他犯法错误，则会影响到
                             REQUIRES_NEW：必须开启新的事务
                             rollback-for:表示当前那个异常事务回滚
             -->
            <tx:method name="save*" propagation="REQUIRES_NEW" rollback-for="java.lang.RuntimeException"></tx:method>
            <tx:method name="update*" propagation="REQUIRES_NEW" rollback-for="RuntimeException"></tx:method>
            <tx:method name="remove*" propagation="REQUIRES_NEW" rollback-for="RuntimeException"></tx:method>
            <tx:method name="batch*" propagation="REQUIRES_NEW" rollback-for="RuntimeException"></tx:method>

        </tx:attributes>
    </tx:advice>

    <aop:config>
        <!--这里按顺序表示  *：任意返回值和修饰符 *：任意包 ..：任意包下的任意层次 *service：表示任意方法一service后缀结尾的类  *：任意方法  ..：任意参数      -->
        <!--  考虑到后面我们整合SpringSecurity，避免把UserService加入事务控制 让切入点定位到Impl结尾的方法-->
        <aop:pointcut id="TxPoint" expression="execution(* *..*ServiceImpl.*(..))"/>

        <aop:advisor advice-ref="transactionInterceptor" pointcut-ref="TxPoint"></aop:advisor>
    </aop:config>

</beans>
```

## 2.SpringMVC

### 1.各个配置文件的关系

![image-20201007121113838](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007121313.png)

####  1.1web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--    配置字符集拦截器-->
    <!--CharacterEncodingFilter这个filter一定要在所有的filter
             原因：request.setCharacterEncoding(encoding),一定一早在getAttributes（其他filter生效）前面
    -->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <!--        拦截所有路径-->
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--配置监听器-->
    <!--    引入外部spring文件-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:Spring_Tx.xml,classpath*:Spring_Mybatis.xml</param-value>
    </context-param>
    <!--在服务器创建的时候监听servletContext，创建ioc 把servlet传入IOC-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--配置转发器dispatcherServlet
             一般servlet默认生命周期，创建对象在第一次接受请求的时候
             但在dispatcherServlet里面，有很多的框架初始化操作，不适合在请求的时候在来初始化
             设置<load-on-startup>1</load-on-startup>为了让servlet最早去创建
               但是加载顺序不会超过listener filter 加载顺序：listener->Filter>servlet
    -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:Spring_Mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!--        配置url-servlet的方式：/表示拦截所有请求-->
        <!--        <url-pattern>/</url-pattern>-->
        <!--        配置url-servlet的方式：通过扩展名-->
        <!--        优点1：xxx.css，xxx.js，xxx.png等静态资源完全不经过SpringMvc，不需要特殊处理
                    优点2：可以实现伪静态效果，表面看起来是一个静态html文件，实际上是动态java运行的结果
                          给黑客入侵增加难度
                          利于SEO优化，SEO就是让百度，谷歌搜索引擎更容易找到我们的项目
                    缺点：不符合Restful风格，
                    -->
        <url-pattern>*.html</url-pattern>
        <!--   为什么要另外配置一个json扩展名呢

              如果一个Ajax请求扩展名是html，但是实际服务器返回的是*.json，就会报错406，所以我们的需要可以拦截*.json

        -->
        <!--
                                1. 1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
                                2. 2xx：成功。代表：200
                                3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
                                4. 4xx：客户端错误。
                                    * 代表：
                                        * 400：BadRequest 一般是参数问题，例如类型转换，参数为找到
                                        * 404（请求路径没有对应的资源）
                                        * 405：请求方式没有对应的doXxx方法
                                5. 5xx：服务器端错误。代表：500(服务器内部出现异常)
          -->
        <url-pattern>*.json</url-pattern>

    </servlet-mapping>


</web-app>
```

#### 1.2Springmvc配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="MVC"></context:component-scan>

<!--    mvc:annotation-driven:相当于打开了@RequestBody，@RequestBody，@ControllerAdvice-->
    <mvc:annotation-driven></mvc:annotation-driven>

<!--可以-->
<!--    <mvc:default-servlet-handler></mvc:default-servlet-handler>-->

<!--    配置视图解析器-->

    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/"></property>
<!--        保护jsp，将jsp文件放入web-inf下面-->
         <property name="suffix" value=".jsp"></property>
    </bean>
</beans>
```

#### 1.3配置jsp

首先引入相应得api（servlet，jsp-api），注意这里的scope讲解一下：添加<scope>provided</scope> ，因为provided表明该包只在编译和测试的时候用，所以，当启动tomcat的时候，就不会冲突了，完整依赖如下-->

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200929200140496.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)

##### 对应的jsp文件：在我们项目中，jsp文件一般设定到WEB-INF下面，保护文件，不让外界直接能访问到

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200929195913458.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)



```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/9/29
  Time: 18:05
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<%--base标签：
<base> 标签为页面上的所有链接规定默认地址或默认目标。

通常情况下，浏览器会从当前文档的 URL 中提取相应的元素来填写相对 URL 中的空白

使用 <base> 标签可以改变这一点。浏览器随后将不再使用当前文档的 URL，而使用指定的基本 URL 来解析所有的相对 URL。这其中包括 <a>、<img>、<link>、<form> 标签中的 URL。--%>
<%--尽量这样写因为他是动态的，注意${pageContext.request.contextPath}本身带有/所以提前去掉，后面的/要留着--%>
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
<head>
    <title>Title</title>
</head>
<body>
<%--${pageContext.request.contextPath}/--%>
<%--如果不写拓展名，dispatchservlet不回去拦截，直接访问tomcat有没有此文件
    一般来说应该写的,但是我们的tomcat路径设置的就是http://localhost:8080/Crowdfunding/
--%>
<%--记住 请求的uil不可以有/开头，例如：/test/ssm.html, 这样就不参考base标签了--%>
<a href="test/ssm.html">TestSSM</a>
</body>
</html>
```

#### 1.4controller

```java
package MVC.Controller;

import CrowedFunding_Entity.Admin;
import Service.dao.AdminServie;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;
import java.util.Map;

@Controller
//父路径
@RequestMapping("/test")
public class TestHandler {

    @Autowired
    private AdminServie adminServie;

    @RequestMapping("/ssm.html")
    public String testSSM(Map map){
        List<Admin> all = adminServie.getALL();
        map.put("all",all);
       return "Target";
    }
}

```

#### 挂羊头卖狗肉：报错406

* 当你使用@responseBody返回json数据，但是你当前页面是*.html，就会报错406![image-20201007123003028](/20201007123220.png)

**解决办法:**在你的`dispatcherServlet`添加扩展名json

```xml
    <!--配置转发器dispatcherServlet
             一般servlet默认生命周期，创建对象在第一次接受请求的时候
             但在dispatcherServlet里面，有很多的框架初始化操作，不适合在请求的时候在来初始化
             设置<load-on-startup>1</load-on-startup>为了让servlet最早去创建
               但是加载顺序不会超过listener filter 加载顺序：listener->Filter>servlet
    -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:Spring_Mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!--        配置url-servlet的方式：/表示拦截所有请求-->
        <!--        <url-pattern>/</url-pattern>-->
        <!--        配置url-servlet的方式：通过扩展名-->
        <!--        优点1：xxx.css，xxx.js，xxx.png等静态资源完全不经过SpringMvc，不需要特殊处理
                    优点2：可以实现伪静态效果，表面看起来是一个静态html文件，实际上是动态java运行的结果
                          给黑客入侵增加难度
                          利于SEO优化，SEO就是让百度，谷歌搜索引擎更容易找到我们的项目
                    缺点：不符合Restful风格，
                    -->
        <url-pattern>*.html</url-pattern>
        <!--   为什么要另外配置一个json扩展名呢

              如果一个Ajax请求扩展名是html，但是实际服务器返回的是*.json，就会报错406，所以我们的需要可以拦截*.json

        -->
        <!--
                                1. 1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
                                2. 2xx：成功。代表：200
                                3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
                                4. 4xx：客户端错误。
                                    * 代表：
                                        * 400：BadRequest 一般是参数问题，例如类型转换，参数为找到
                                        * 404（请求路径没有对应的资源）
                                        * 405：请求方式没有对应的doXxx方法
                                5. 5xx：服务器端错误。代表：500(服务器内部出现异常)
          -->
<!--        防止出现406-->
        <url-pattern>*.json</url-pattern>

    </servlet-mapping>

```

#### base标签：

```html
base标签：
<base> 标签为页面上的所有链接规定默认地址或默认目标。

通常情况下，浏览器会从当前文档的 URL 中提取相应的元素来填写相对 URL 中的空白

使用 <base> 标签可以改变这一点。浏览器随后将不再使用当前文档的 URL，而使用指定的基本 URL 来解析所有的相对 URL。这其中包括 <a>、<img>、<link>、<form> 标签中的 URL。--%>
```

注意：

* 端口号前必须加：
* contextPath前面不用加上/
* contextPath后面必须加上/
* 页面上所有的参照base标签的标签都必须放在base标签的后面

![在这里插入图片描述](/20201007130118.png)

## 3.ajax

### 3.1. 建立意识

![image-20201007131512948](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007131513.png)

### 3.2. 常用注解：@RequestBody和@ResponseBody

@RequestBody和@ResponseBody要想正常使用，必须加入Jackson依赖，同时在mvc配置文件，配置mvc:annotion-driven 标签

![image-20201007133058619](/20201007133058.png)



#### 知识巩固

1. 请求消息：客户端发送给服务器端的数据

   * 数据格式：
     1. 请求行
        2. 请求头
        3. 请求空行
        4. 请求体

   2. 响应消息：服务器端发送给客户端的数据
      * 数据格式：
        1. 响应行
           1. 组成：协议/版本 响应状态码 状态码描述
           2. 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态。
              1. 状态码都是3位数字 
              2. 分类：
                 1. 1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
                 2. 2xx：成功。代表：200
                 3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
                 4. 4xx：客户端错误。
                    * 代表：
                      * 400：BadRequest 一般是参数问题，例如类型转换，参数为找到
                      * 404（请求路径没有对应的资源） 
                      * 405：请求方式没有对应的doXxx方法
                 5. 5xx：服务器端错误。代表：500(服务器内部出现异常)

        2. 响应头：
           1. 格式：头名称： 值
           2. 常见的响应头：
              1. Content-Type：服务器告诉客户端本次响应体数据格式以及编码格式
              2. Content-disposition：服务器告诉客户端以什么格式打开响应体数据
                 * 值：
                   * in-line:默认值,在当前页面内打开
                   * attachment;filename=xxx：以附件形式打开响应体。文件下载
        3. 响应空行
        4. 响应体:传输的数据

### 3.3@RequestBody的使用

#### 传入数组

##### 第一种方法

Jquery

![image-20201007145055183](/20201007145056.png)

java:

```java
   @RequestMapping("/testJson.html")
    @ResponseBody
    public String testJson(@RequestParam("array[]") int [] arr){

        for(int i:arr){
            System.out.println(i);
        }

        return "Target";

    }
```

问题：传过来的数据json自动转换成特别的形式array [] :1 array[]:2...........array[]:n,需要@requestParam("")，在写入[].

##### 第二种方法（建议使用方法)

将json数据字符串化，提交json字符串对象

```html
<script>
    // 加载
    $(function () {

        $("#btn1").click(function () {
            <%--
                注意当你当然可以使用$.post() $.get()，但是$.post() $.get()这两个都是服务器成功处理之后，响应状态码必须是200才可以执行回调函数
                $.ajax()不管响应状态码是多少，都可处理
             --%>
            // 第二种方法
            // json数组
            var array=[6,9,5,4,1,8,7,4,6]

            // 将json数组转换为json字符串
            //注意无论是json数组还是对象都要像这样转换为字符串
            var requestBody = JSON.stringify(array);
            // json对象
            var obj={"name":"tom","age":12}
                             $.ajax({
                                 url:"test/testJson.html",      //请求地址
                                 type:"POST",                    //请求方式
                                 // 第一种方案：将@requestBody下入value="arrat[]"
                                 // data: {"array":[6,9,5,4,1,8,7,4,6]},      //要发送的请求参数
                                 // 第二种方案
                                 //必须设置contentType，告诉服务器发给我的数据类型化
                                 data: requestBody,      //要发送的请求参数
                                 contentType:"application/json;charset=UTF-8",
                                 datatype:"text",               //如何对待服务器返回的数据
                                 success:function (param) {      //服务器成功处理参数返后调用的回调函数，param是服务器返回参数
                                     alert(param)
                                 },
                                 error:function (param) {        //服务器失败处理参数返后调用的回调函数，param是服务器返回参数
                                     alert(param)
                                 }


                             })
                         })
                     })
                 </script>
```

当你的请求体为 Request Payload时候，你就可以直接使用@RequestBody获得json，但是必须在ajax请求的时候加入**contentType**属性

![image-20201007160737209](/20201007160818.png)

对应的后盾端数据

```java
    @RequestMapping("/testJson.html")
    @ResponseBody
    public String testJson(@RequestBody() List<Integer>arr){

        //创建日志对象，通过日志数据输出方便后期管理
        Logger logger = LoggerFactory.getLogger(TestHandler.class);



        for(Integer i:arr){

            logger.info("number:"+i);
        }

        return "success";

    }

```



#### 传入复杂对象

建立复杂实体bean

![image-20201007162609071](/20201007162609.png)

对应的ajxa方法：

```html
<script>
    // 加载
    $(function () {

        //传入json形式的复杂对象
        $("#btn2").click(function () {
            // 创建Json对象
            var student={
            // private Integer stuId;
            // private String strName;
            // private Address address;
            // private List<Subject> SubjectList;
            // private Map<String,String> map;
                "stuId":1,
                "strName":"LiSi",
                "address":{
                    // private String province;
                    // private String city;
                    // private String street;
                       "province":"Guangdong",
                       "city":"Wuhu",
                       "street":"facai"},
                "SubjectList":[{
                    // private String subjectName;
                    // private Integer subjectNumber;
                    "subjectName":"Math",
                    'subjectNumber':1
                },{
                    // private String subjectName;
                    // private Integer subjectNumber;
                    "subjectName":"Chinese",
                    'subjectNumber':0
                }],
                "map":{
                    "k1":"1k",
                    "k2":"2k"
                }

        }
        var student = JSON.stringify(student);
        $.ajax({
            url:"test/testConfuseJson.html",
            type:"POST",
            data:student,
            contentType:"application/json;charset=UTF-8",
            success:function (param) {
                alert(param)
            },
            error:function (param) {
                alert(param)
            }
        })
        })
   
                 </script>
```

对应的Handler：

```java
    @RequestMapping("/testConfuseJson.html")
    @ResponseBody
    public String testConfuseJson(@RequestBody Student student){

        logger.info("student:"+student);

        return "success";
    }

```

#### Ajax返回结果规范

* 统一整个项目Ajax的返回结果集，未来也可以用于分布式架构各个模块见得调用

代码

```java
package ResultEntity;

public class ResultEntity<T>{

    public static final String SUCCESS="SUCCESS";
    public static final String FAILED="FAILED";

/**用来封装当前结果成功还是失败*/
    private String result;
/**    请求处理失败，返回的错误消息*/
    private String message;
/**要返回的数据*/
    private T data;

    public ResultEntity() {
    }

    public ResultEntity(String result, String message, T data) {
        this.result = result;
        this.message = message;
        this.data = data;
    }
/**泛型方法:泛型在类上面就是泛型类，在方法上面就是泛型方法*/
/**请求成功但不需要返回类型*/
    public static <Type>ResultEntity<Type> successWithoutData(){
        return new ResultEntity<Type>(SUCCESS,null,null);
    }
/**请求成功但是需要返回数据
 * 注意:这里的泛型T就是泛型Type，准确的说是 先有Type再有T
 * */
    public static <Type>ResultEntity<Type> successWithData(Type data){
       return new ResultEntity<Type>(SUCCESS,null,data);
    }
/**处理消息失败*/
    public static <Type>ResultEntity<Type> Failed(String message){
        return new ResultEntity<Type>(FAILED,message,null);
    }

    public String getResult() {
        return result;
    }

    public void setResult(String result) {
        this.result = result;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    @Override
    public String toString() {
        return "ResultEntity{" +
                "result='" + result + '\'' +
                ", message='" + message + '\'' +
                ", data=" + data +
                '}';
    }
}

```

注意：这里使用的两个泛型，其实是一个泛型

![image-20201007201728113](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007201834.png)

测试：

一定要修改你的ajax的url和handler@RequestMapping("/testConfuseJson.json")，之前讲过，返回json的时候你的url是*.html，会报错406   挂羊头卖狗肉

![image-20201007205415342](/20201008145126.png)

解决办法：

Handler

```java
    @RequestMapping("/testConfuseJson.json")
    @ResponseBody
    public ResultEntity<Student> testConfuseJson(@RequestBody Student student){

        logger.info("student:"+student);
//        ResultEntity<Student> studentResultEntity=ResultEntity.successWithData(student);
//        直接这样返回就相当于上面一样
        return ResultEntity.successWithData(student);
    }
```

ajax

![image-20201007205638062](/20201008145127.png)

## 4.异常映射

### 4.1.目标

* 使用异常映射机制讲整个项目的异常和错误提示进行统一管理

### 4.2.异常的两种配置

Springmvc提供了xml异常映射和基于注解的异常映射，不是二选一，有区别，原因在下面

#### 1.思路

![image-20201008135836032](/20201008135839.png)

![image-20201008140756724](/20201008145236.png)

#### 2.基于xml的异常映射(SpringMvc.xml)

**注意：当你没有写注解映射，这个简单异常处理器会帮助你处理异常**

```xml
<!--配置基于Xml的异常映射-->
    <bean id="XmlExceptionResolver" class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
<!--        配置异常类型-->
        <property name="exceptionMappings" >
            <props>
<!--                key属性指定异常 标签体写入对应的视图，还去视图解析器那里拼接,不配置注解的异常处理器，这个简单处理器也会出来@requestMaping-->
                <prop key="java.lang.Exception">system-error</prop>
            </props>
        </property>
    </bean

```

#### 3.基于注解的异常映射

注意：一般情况下，在你的后端，你会接收到两种需求

![image-20201008135836032](/20201008135839.png)

你需要判断需求类型，所以你需要写一个**工具类**

##### 判断请求类型的工具方法

思路：通过请求头的一个属性值`X-Requested-With` 来判断请求是否是普通请求还是ajax请求，当你的`X-Requested-With` 通过request.getHeader("`X-Requested-With`")取不到值，说明你的请求普通请求，如果可以去到 **XMLHttpRequest** 说明这个请求时ajax请求

![image-20201008171524777](/20201009162657.png)

判断请求的工具



```java
package Util;

import sun.misc.Request;

import javax.servlet.http.HttpServletRequest;

public class CrowdUtil {

//    通过request判断
    public static boolean  judgeRequestType(HttpServletRequest request){

        /**通过请求头的X-Requested-With属性值判断，null为普通请求 XMLHttpRequest为ajax请求 */
        String X_Requested_With = request.getHeader("X-Requested-With");

        System.out.println(X_Requested_With);
//        返回为true表示ajax，false表示普通请求
        return "XMLHttpRequest".equals(X_Requested_With);
    }
}

```



##### CrowedExceptionHandler

```java
package MVC.ExceptionHandler;

import Util.CrowdUtil;
import Util.ResultEntity;
import com.google.gson.Gson;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**@ControllerAdvice是一个基于注解的异常处理类*/
@ControllerAdvice
public class CrowedExceptionHandler {

/*  @ExceptionHandler可以将一个具体的异常类型和一个方法关联起来*/
    @ExceptionHandler(value = {NullPointerException.class})
//  注解可以从参数中获取，异常

    public ModelAndView ResolveNullPointerException(
            //实际捕获的异常
            NullPointerException nullPointerException ,
            //当前请求对象
            HttpServletRequest request,
            HttpServletResponse response) throws IOException {

         //首先获得当前请求时什么请求


        boolean b = CrowdUtil.judgeRequestType(request);

        if(b){
            //说明是ajax
            //返回ResultEntity对象,传入异常信息
            ResultEntity<Object> failed = ResultEntity.Failed(nullPointerException.getMessage());

//            将ResultEntity转换为json字符串
//            1.创建Gson对象
            Gson gson=new Gson();
//           2.进行转换
            String s = gson.toJson(failed);

//            3.将json数据传入到网页
            response.getWriter().write(s);

        }

        ModelAndView mv=new ModelAndView("system-error");
        mv.addObject("exception", nullPointerException);

        return mv;
    }
}

```



虽然异常处理器完成了，但是我们遇到了一个问题，一个异常就要一个方法？，可大部分代码是一样的，所以，我们要复用

船新版本：

```java
//更新版
    private ModelAndView CommonExceptionResolved(
            //不同的错误可能有不同的视图
            String view,
            //对应的异常,这里时向上转型
            Exception exception,
            HttpServletRequest request,
            HttpServletResponse response) throws IOException {
        //首先获得当前请求判定什么请求
        boolean b = CrowdUtil.judgeRequestType(request);

        if(b){
            //说明是ajax

            //LoggerFactory.getLogger(TestHandler.class).info("异常处理器");

            //返回ResultEntity对象
            ResultEntity<Object> failed = ResultEntity.Failed(exception.getMessage());

            // 将ResultEntity转换为json字符串
            //1.创建Gson对象
            Gson gson=new Gson();
            //2.进行转换
            String s = gson.toJson(failed);

            //3.将json数据传入到网页
            response.getWriter().write(s);

            return null;
        }

        ModelAndView mv=new ModelAndView(view);
        mv.addObject("exception", exception);

        return mv;

    }

    @ExceptionHandler(value = {NullPointerException.class})
    public ModelAndView NullPointerExceptionResolver(Exception e,
                                             HttpServletRequest request,
                                             HttpServletResponse response) throws IOException {

      return  CommonExceptionResolved("system-error.jsp", e,request,response);
    }
```

### 补充：

不要去在拦截器中想向上抛出异常，否则会直接交给虚拟机还是什么东西处理，你的异常拦截器不会捕捉到

![image-20210104203259187](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210104203300.png)

否则拦截器里面抛出的异常是不可以被注解的异常处理器访问到的，只可以被xml的访问到，个人理解：在你的拦截器加载没完成直接抛出异常是不可以的，因为这个时候你的自定义异常处理器还没有创建吧 大概~

换句话说也就是，你在拦截器的prehandle的方法抛出异常，只会被xml异常处理拦截到

你在拦截器的prehandle的方法不抛出异常，被异常处理器拦截到

原因：

* https://www.jianshu.com/p/02d334c3a8e8?utm_source=oschina-app

* https://www.cnblogs.com/cndota/p/5879762.html

## 5.创建常量类

* 方便后期更改，统一规范

  ![image-20201008210302748](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008210415.png)

代码：

```java
package Util;

public class CrowedConstant {

    public static final String MESSAGE_ACCESS_FORBIDEN="Sorry,Please log in";

    public static final String MESSAGE_LOAD_FAILED="Sorry,Account or Password is wrong";


    public static final String MESSAGE_LOAD_ALREADY_IN_USE="Sorry,Account is USED";


    public static final String ATTR_Name_exception="exception";

}

```

## 6.登录页面搭建

![image-20201008224507314](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162658.png)

### 登录跳转

* 因为跳转到登录界面只是一个浏览器的转发，没有其他的操作，所以直接视图转发`mvc:view-controller`就可以

![image-20201008224619575](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008224619.png)

这样只需要在浏览器输入对应的url就可以了

![image-20201008224658749](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008224658.png)



##   7.layer

### 1.引入运行环境

![image-20201009113420516](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162659.png)

### 2.插入script并测试

![image-20201009113842121](/20201009162700.png)

### 修改system-error.jsp页面

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="keys" content="">
    <meta name="author" content="">
    <link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">
    <link rel="stylesheet" href="static/css/font-awesome.min.css">
    <link rel="stylesheet" href="static/css/login.css">
    <script src="static/Jquery/jquery-3.3.1.min.js"></script>
    <script src="static/bootstrap/js/bootstrap.min.js"></script>
    <style>

    </style>
</head>
<script>

     $(function () {
         $("#btn").click(function () {
             //相当于浏览器的后退按钮
             window.history.back();
         })
     })
</script>
<body>
<nav class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container">
        <div class="navbar-header">
            <div><a class="navbar-brand" href="index.html" style="font-size:32px;">尚筹网-创意产品众筹平台</a></div>
        </div>
    </div>
</nav>

<div class="container">

        <h2 class="form-signin-heading"><i class="glyphicon glyphicon-log-in" style="text-align: center"></i> 尚筹网系统消息</h2>
<%--        表示req.getattribute("ex").getmessage--%>
        <h3>${requestScope.exception.message}</h3>

        <button id="btn" class="btn btn-l btn-success btn-block">点我回到上一步 </button>
</div>

</body>
</html>
```



# 管理员登录

## 1.流程图

![image-20201009162620943](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162701.png)



## 2.MD5加密工具方法

在你的工具模块写

![image-20201009163001334](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203541.png)



```java

    /**
     * 明文字符串转进行Md5加密
     * @param source 传入明文字符串
     * @return  加密结果
     */
    public static String MD5(String source){

        // 1.判断字符穿是否合法
        if(source==null||source.length() == 0){
          // 2.不合法抛出运行异常
          throw new RuntimeException(CrowedConstant.MESSAGE_ILLEAGE);
        }


        try {
            // 3.创建MessageDigest，MessageDigest.getInstance(level)，level表示什么加密方式
            String level="md5";
            MessageDigest messageDigest=MessageDigest.getInstance(level);

            // 4.获取明文数组串对应的字节数组
            byte[] bytes = source.getBytes();
            // 5.执行加密
            byte[] digest = messageDigest.digest(bytes);

            // 6.方便传入数据库，创建BigInteger
            // singNum表示控制是正数还是负数 ，1表示整数
            int singNum=1;
            BigInteger big=new BigInteger(singNum,digest);
            //按照16进制将BigInteger转变为字符串
            int radix=16;

          String encode=big.toString(radix);

          return encode;
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }

        return null;
    }
```

## 3.创建自定义登陆失败异常

![image-20201009172134326](/20201012203542.png)

```java
package Exception;

/**
 * @author lenovo
 */
public class LoginFailedException extends RuntimeException{

    static final long serialVersionUID = -7034897190745766939L;
    
    public LoginFailedException() {
    }

    public LoginFailedException(String message) {
        super(message);
    }

    public LoginFailedException(String message, Throwable cause) {
        super(message, cause);
    }

    public LoginFailedException(Throwable cause) {
        super(cause);
    }

    public LoginFailedException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }
}

```

## 4.登录代码

jsp

```html
<%@ taglib prefix="mvc" uri="http://www.springframework.org/tags/form" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="keys" content="">
    <meta name="author" content="">
    <link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">
    <link rel="stylesheet" href="static/css/font-awesome.min.css">
    <link rel="stylesheet" href="static/css/login.css">
    <script src="static/Jquery/jquery-3.3.1.min.js"></script>
    <script src="static/bootstrap/js/bootstrap.min.js"></script>
<%--    <link rel="stylesheet" href=""> --%>
    <style>

    </style>
</head>
<body>
<nav class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container">
        <div class="navbar-header">
            <div><a class="navbar-brand" href="index.html" style="font-size:32px;">尚筹网-创意产品众筹平台</a></div>
        </div>
    </div>
</nav>

<div class="container">

    <form action="Admin/login.html" method="post" class="form-signin" role="form">
        <h2 class="form-signin-heading"><i class="glyphicon glyphicon-log-in"></i> 管理员登录</h2>
        <div class="form-group has-success has-feedback">
            <input type="text" name="loginAcct" class="form-control" id="userName" placeholder="请输入登录账号" autofocus>
            ${requestScope.exception.message}
            <span class="glyphicon glyphicon-user form-control-feedback"></span>
        </div>
        <div class="form-group has-success has-feedback">
            <input type="password" name="userPswd" class="form-control" id="userPass" placeholder="请输入登录密码" style="margin-top:10px;">
            <span class="glyphicon glyphicon-lock form-control-feedback"></span>
        </div>
        <button type="submit" class="btn btn-lg btn-success btn-block" > 登录</button>
    </form>
</div>

</body>
</html>
```

handler

```java
package MVC.Controller;

import CrowedFunding_Entity.Admin;
import Service.dao.AdminServie;
import Util.CrowedConstant;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.SessionAttribute;
import org.springframework.web.bind.annotation.SessionAttributes;

import javax.servlet.http.HttpSession;

/**
 * @author lenovo
 */
@Controller
@RequestMapping("/Admin")
public class AdminHandler {

    @Autowired
    private AdminServie adminServie;

    @RequestMapping("/login.html")
    public String doLogin(@RequestParam("loginAcct") String userName, @RequestParam("userPswd") String password, HttpSession session){

        //若返回admin对象说明登录成功，若果账号密码不正确则会抛出异常
        Admin adminByLogAct = adminServie.getAdminByLogAct(userName, password);


        session.setAttribute(CrowedConstant.ATTR_LOGIN_ADMIN, adminByLogAct);


        return "success";
    }

}

```

service

```java
package Service.Impl;

import CrowedFunding_Entity.Admin;
import CrowedFunding_Entity.AdminExample;
import Mapper.AdminMapper;
import Service.dao.AdminServie;
import Util.CrowdUtil;
import Util.CrowedConstant;
import exception.LoginFailedException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import sun.security.jgss.LoginConfigImpl;

import java.util.List;
import java.util.Objects;

@Service
public class AdminServiceImpl implements AdminServie {

    @Autowired
    private AdminMapper adminMapper;

    //存入admin
    @Override
    public void saveAdmin(Admin admin) {

          adminMapper.insert(admin);


    }

    // 获得全体成员
    @Override
    public List<Admin> getALL() {
        return adminMapper.selectByExample(new AdminExample());
    }
    // 登入
    @Override
    public Admin getAdminByLogAct(String userName, String password) {

        // 1.根据账号查询Admin对象
        //创建adminExample对象，进行条件查询
        AdminExample adminExample=new AdminExample();
        AdminExample.Criteria criteria = adminExample.createCriteria();

        AdminExample.Criteria s1 = criteria.andLoginAcctEqualTo(userName);

        List<Admin> admins = adminMapper.selectByExample(adminExample);




        // 2.查询是否为空，空则抛出异常，反之从数据库取出作比较
        if(admins == null || admins.size() == 0){
            throw new LoginFailedException(CrowedConstant.MESSAGE_LOAD_FAILED);
        } else if(admins.size()>1){
            throw new  LoginFailedException(CrowedConstant.LOGIN_NOR_NNIQUE);
        }
//        list问题！！！！！！！！！！！
        Admin admin=admins.get(0);
        // 3.将表单密码进行加密
        String userPswdFromForm = CrowdUtil.MD5(password);
        // 4.和数据库取出密码进行比较
        String userPswdFromDB =  admin.getUserPswd();
        // 5.一致返回对象反之抛出异常，

        // 使用Objects.equals(userPswdFromForm,userPswdFromDB)的好处是：
        // public static boolean equals(Object a, Object b) {
        // 先判断是不是同一个对象或者判断是不是同一个属性值
        // return (a == b) || (a != null && a.equals(b));

        if(!Objects.equals(userPswdFromDB,userPswdFromForm)){
            throw new LoginFailedException(CrowedConstant.MESSAGE_LOAD_FAILED);
        }
        return admin;

    }
}

```

## 5.重定向到主页面

* 转发就是服务器再给浏览器一个路径让他去访问，但是我们的主界面在WEB-INF下不能直接访问，所以我们需要通过mvc的view-conrtoller来重定向



![image-20201009230530187](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009230652.png)

修改为重定向

```java
package MVC.Controller;

import CrowedFunding_Entity.Admin;
import Service.dao.AdminServie;
import Util.CrowedConstant;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.SessionAttribute;
import org.springframework.web.bind.annotation.SessionAttributes;

import javax.servlet.http.HttpSession;

/**
 * @author lenovo
        */
@Controller
@RequestMapping("/Admin")
public class AdminHandler {

    @Autowired
    private AdminServie adminServie;

    @RequestMapping("/login.html")
    public String doLogin(@RequestParam("loginAcct") String userName, @RequestParam("userPswd") String password, HttpSession session){

        //若返回admin对象说明登录成功，若果账号密码不正确则会抛出异常
        Admin adminByLogAct = adminServie.getAdminByLogAct(userName, password);


        session.setAttribute(CrowedConstant.ATTR_LOGIN_ADMIN, adminByLogAct);


        /*要重定向，否则刷新就相当于重新提交表单，也即是在访问下handler方法，就再一次查找了数据库
        但是我们不能直接写访问这个jsp，因为是交给jsp访问的，所以要写对应的url,因为重定向是再给浏览器一个url
        这里的不可以直接转发web-inf下面的jsp因为被保护，所以得写入访问路径，这里我们直接写入新的url，让视图转换器跳转
        转发这里要加/
        */
        return "redirect:/Page.html";
    }

}

```

## 6.用户退出登录

```java
    @RequestMapping("/logout.html")
    public String doLogout(HttpSession session){

        //强制刷新session
        session.invalidate();

//        这里也要重定向，防止重复提交表单，没意义

        return "redirect:/logout.html";

    }
```

![image-20201010183849094](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201010183900.png)

## 7.抽取后台页面

在我们实际开发的时候，有许多页面是所有页面公有的，方便我们开发，所以我们选择抽取后台页面

![image-20201010184341189](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203543.png)

方法

添加抽取的页面：

![image-20201010185214220](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203544.png)

include-head.jsp

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/10
  Time: 18:44
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">
    <link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">
    <link rel="stylesheet" href="static/css/font-awesome.min.css">
    <link rel="stylesheet" href="static/css/main.css">
    <script src="static/Jquery/jquery-3.3.1.min.js"></script>
    <script src="static/bootstrap/js/bootstrap.min.js"></script>
    <script src="static/script/docs.min.js"></script>
    <script src="static/layer/layer.js"></script>
    <script type="text/javascript">
        $(function () {
            $(".list-group-item").click(function(){
                if ( $(this).find("ul") ) {
                    $(this).toggleClass("tree-closed");
                    if ( $(this).hasClass("tree-closed") ) {
                        $("ul", this).hide("fast");
                    } else {
                        $("ul", this).show("fast");
                    }
                }
            });
        });
    </script>
    <style>
        .tree li {
            list-style-type: none;
            cursor:pointer;
        }
        .tree-closed {
            height : 40px;
        }
        .tree-expanded {
            height : auto;
        }
    </style>
</head>

```

include-nav.jsp

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/10
  Time: 18:47
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<nav class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container-fluid">
        <div class="navbar-header">
            <div><a class="navbar-brand" style="font-size:32px;" href="#">众筹平台 - 控制面板</a></div>
        </div>
        <div id="navbar" class="navbar-collapse collapse">
            <ul class="nav navbar-nav navbar-right">
                <li style="padding-top:8px;">
                    <div class="btn-group">
                        <button type="button" class="btn btn-default btn-success dropdown-toggle" data-toggle="dropdown">
                            <i class="glyphicon glyphicon-user"></i> ${sessionScope.loginAdmin.userName} <span class="caret"></span>
                        </button>
                        <ul class="dropdown-menu" role="menu">
                            <li><a href="#"><i class="glyphicon glyphicon-cog"></i> 个人设置</a></li>
                            <li><a href="#"><i class="glyphicon glyphicon-comment"></i> 消息</a></li>
                            <li class="divider"></li>
                            <li><a href="Admin/logout.html"><i class="glyphicon glyphicon-off"></i> 退出系统</a></li>
                        </ul>
                    </div>
                </li>
                <li style="margin-left:10px;padding-top:8px;">
                    <button type="button" class="btn btn-default btn-danger">
                        <span class="glyphicon glyphicon-question-sign"></span> 帮助
                    </button>
                </li>
            </ul>
            <form class="navbar-form navbar-right">
                <input type="text" class="form-control" placeholder="查询">
            </form>
        </div>
    </div>
</nav>

```

include-setbar.jsp

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/10
  Time: 18:50
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<div class="col-sm-3 col-md-2 sidebar">
    <div class="tree">
        <ul style="padding-left:0px;" class="list-group">
            <li class="list-group-item tree-closed" >
                <a href="main.html"><i class="glyphicon glyphicon-dashboard"></i> 控制面板</a>
            </li>
            <li class="list-group-item tree-closed">
                <span><i class="glyphicon glyphicon glyphicon-tasks"></i> 权限管理 <span class="badge" style="float:right">3</span></span>
                <ul style="margin-top:10px;display:none;">
                    <li style="height:30px;">
                        <a href="user.html"><i class="glyphicon glyphicon-user"></i> 用户维护</a>
                    </li>
                    <li style="height:30px;">
                        <a href="role.html"><i class="glyphicon glyphicon-king"></i> 角色维护</a>
                    </li>
                    <li style="height:30px;">
                        <a href="permission.html"><i class="glyphicon glyphicon-lock"></i> 菜单维护</a>
                    </li>
                </ul>
            </li>
            <li class="list-group-item tree-closed">
                <span><i class="glyphicon glyphicon-ok"></i> 业务审核 <span class="badge" style="float:right">3</span></span>
                <ul style="margin-top:10px;display:none;">
                    <li style="height:30px;">
                        <a href="auth_cert.html"><i class="glyphicon glyphicon-check"></i> 实名认证审核</a>
                    </li>
                    <li style="height:30px;">
                        <a href="auth_adv.html"><i class="glyphicon glyphicon-check"></i> 广告审核</a>
                    </li>
                    <li style="height:30px;">
                        <a href="auth_project.html"><i class="glyphicon glyphicon-check"></i> 项目审核</a>
                    </li>
                </ul>
            </li>
            <li class="list-group-item tree-closed">
                <span><i class="glyphicon glyphicon-th-large"></i> 业务管理 <span class="badge" style="float:right">7</span></span>
                <ul style="margin-top:10px;display:none;">
                    <li style="height:30px;">
                        <a href="cert.html"><i class="glyphicon glyphicon-picture"></i> 资质维护</a>
                    </li>
                    <li style="height:30px;">
                        <a href="type.html"><i class="glyphicon glyphicon-equalizer"></i> 分类管理</a>
                    </li>
                    <li style="height:30px;">
                        <a href="process.html"><i class="glyphicon glyphicon-random"></i> 流程管理</a>
                    </li>
                    <li style="height:30px;">
                        <a href="advertisement.html"><i class="glyphicon glyphicon-hdd"></i> 广告管理</a>
                    </li>
                    <li style="height:30px;">
                        <a href="message.html"><i class="glyphicon glyphicon-comment"></i> 消息模板</a>
                    </li>
                    <li style="height:30px;">
                        <a href="project_type.html"><i class="glyphicon glyphicon-list"></i> 项目分类</a>
                    </li>
                    <li style="height:30px;">
                        <a href="tag.html"><i class="glyphicon glyphicon-tags"></i> 项目标签</a>
                    </li>
                </ul>
            </li>
            <li class="list-group-item tree-closed" >
                <a href="param.html"><i class="glyphicon glyphicon-list-alt"></i> 参数管理</a>
            </li>
        </ul>
    </div>
</div>

```

# 登陆检查

## 1.目标

将部分资源保护起来，让没有登录的请求不能访问

## 2.思路

![image-20201011144318894](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201011144440.png)

## 3.代码

1. 创建自定义异常

   ![image-20201011153715656](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203545.png)

   ```java
   package exception;
   
   public class AccessForbiddenException extends RuntimeException {
   
       public AccessForbiddenException() {
           super();
       }
   
       public AccessForbiddenException(String message) {
           super(message);
       }
   
       public AccessForbiddenException(String message, Throwable cause) {
           super(message, cause);
       }
   
       public AccessForbiddenException(Throwable cause) {
           super(cause);
       }
   }
   
   ```

   

2. 创建拦截器类

​       ![image-20201011153755146](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203546.png)

```java
package MVC.Interceptor;

import CrowedFunding_Entity.Admin;
import Util.CrowedConstant;
import exception.AccessForbiddenException;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.security.auth.login.LoginException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.nio.file.AccessDeniedException;

public class LoginInterceptor implements HandlerInterceptor {


    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        //1.获取Session对象
        HttpSession session = request.getSession();

        //2.尝试从session中取出数据Admin

        Admin attribute = (Admin) session.getAttribute(CrowedConstant.ATTR_LOGIN_ADMIN);

        //3.进入管理主界面之前，判断是否有用户信息

        if (attribute==null){

            throw new AccessForbiddenException();
        }
        return true;
    }
}

```



3. 注册拦截器类

   ![image-20201011190629938](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203547.png)
   
   

# 管理员维护

## 1. 任务清单

### 分页显示Admin数据

1. 分页显示

2. 带关键词分布

### 新增Admin

### 更新Admin

### 单挑删除Admin



## 4.代码

### 1.分页显示主题数据

#### 1.目标

将数据库中的Admin数据在页面以分页形式显示。在后端将“带关键词”和“不太关键词”的分页合并为同一套代码

#### 2 .思路

![image-20201011194541979](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203548.png)

  1.引入PageHelper

1. 加入对应的jar包

2. 在Spring和Mybatis整合的配置文件配置plugins

   ![image-20201011201120923](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203549.png)

3.  编写Mapper文件，因为我们会使用Mysql的Concat函数

​          concat函数：返回结果为连接参数产生的字符串。如有任何一个参数为NULL ，则返回值为 NULL。或许有一个或多个参数。 如果所有参数均为非二进制字符串，则结果为非二进制字符串。 如果自变量中含有任一二进制字符串，则结果为一个二进制字符串。

  

![image-20201012140802756](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203550.png)



4. 编写Service

```java
    @Override
    public PageInfo<Admin> getAdmins(String create_time,Integer pageNum,Integer pageSize) {

//        这里不可以直接PageHelper，el表达式只可识别这是一个list不是一个对象
        Page<Admin> page = PageHelper.startPage(1, 5);

        List<Admin> admins = adminMapper.selectByContact(create_time);

        PageInfo<Admin> pageInfo=new PageInfo<Admin>(admins);

        return pageInfo;
    }

```

5. 编写Handler

```java
    @RequestMapping("/getPageInfo")
//    @ResponseBody
    public String getPageInfo(@RequestParam(value = "create_time",defaultValue = "") String create_time, @RequestParam(value = "PageNum",defaultValue = "1")Integer PageNum, @RequestParam(value = "PageSize",defaultValue = "5") Integer PageSize, ModelMap modelMap){

//       获取info对象存入模型
        PageInfo<Admin> admins = adminServie.getAdmins(create_time, PageNum, PageSize);


        modelMap.addAttribute(CrowedConstant.ATTR_PAGE_INFO,admins);


        return  "admin-main";
    }
```



### 2. 分页栏

1. 通过`pagination_zh`来进行前端的分页插件

   1. 加入依赖环境，在指定页面加入依赖

      ![image-20201012142731758](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203551.png)

   2. 替换原有的分页栏源码

   ![image-20201012144802793](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203552.png)

   

   3. 写jQuery语句

      ![image-20201012162703583](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203553.png)

4. 依赖文件有bug，注销回调函数代码

   ![image-20201012171356757](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203554.png)

   ![image-20201012171252823](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203555.png)



### 3.关键词查询

3.1. 调整表单

![image-20201012172959883](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203556.png)

3.2.翻页的时候保持查询条件

防止点击分页栏的时候，提交的url没有参数，修改分页的超链接的url

![image-20201012180939454](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203557.png)

3.3 代码直接调用分页查询数据的代码的接口就行

### 4.单例删除

1. 目标

![image-20201012184820460](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203558.png)

2. 思路

3. 代码 

​     前端：

![image-20201012191826320](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203559.png)

​    后端：

​       Service

       ```java
 @Override
    public void deleteUser(Integer id) {

        adminMapper.deleteByPrimaryKey(id);
    
    }
    
       ```

​          注意：我们要是实现的目标是转发到原有的路径并带有`pageNum`和`ketWord`,直接返回会没有数据，转发到原来的页面，当你刷新还会执行删除操作，所以我们选择重定向

修改前端的删除超链接的href，这里直接进入后端Handler的查数据方法

![image-20201012193928846](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203600.png)

​       Handler

​         ![image-20201012200028808](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203601.png)



### 5.新增

#### 1.目标

* 将表单提交的Admin对象保存到数据库中

   1.loginAcct不能重复

   2.密码加密

#### 2.思路

![image-20201012203525133](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203602.png)

#### 3.实现

1. 要查重，将表中login_acct赋予 唯一约束，借此判断是否重复

```
ALTER TABLE `t_admin` ADD UNIQUE INDEX (`login_acct`)
```

2. 调整修改按钮，并准备表单页面admin-login

   ![image-20201012205500200](/image-20201012205500200.png)

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/9
  Time: 18:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
<body>
<%@include file="include-nav.jsp" %>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
            <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
                <ol class="breadcrumb">
                    <li><a href="Admin/getPageInfo.html">首页</a></li>
                    <li><a href="Admin/login.html">数据列表</a></li>
                    <li class="active">新增</li>
                </ol>
                <div class="panel panel-default">
                    <div class="panel-heading">表单数据<div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i class="glyphicon glyphicon-question-sign"></i></div></div>
                    <div class="panel-body">
                        <form href="Admin/Register.html" role="form">
                            <div class="form-group">
                                <label for="userName">登陆账号</label>
                                <input type="text" class="form-control" name="userName" id="userName" placeholder="请输入登陆账号">
                            </div>
                            <div class="form-group">
                                <label for="password">用户名称</label>
                                <input type="text" class="form-control" name="password" id="password" placeholder="请输入用户名称">
                            </div>
                            <div class="form-group">
                                <label for="email">邮箱地址</label>
                                <input type="email" class="form-control" name="email" id="email" placeholder="请输入邮箱地址">
                                <p class="help-block label label-warning">请输入合法的邮箱地址, 格式为： xxxx@xxxx.com</p>
                            </div>
                            <button type="button" name="submit" class="btn btn-success"><i class="glyphicon glyphicon-plus"></i> 新增</button>
                            <button type="button" name="reset" class="btn btn-danger"><i class="glyphicon glyphicon-refresh"></i> 重置</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>

```

3. 配置view-controller

   ![image-20201013175924411](/image-20201013175924411.png)

4. 配置Service

   ```java
   
       //存入admin
       @Override
       public void saveAdmin(Admin admin) {
           //密码加密
           String password=admin.getUserPswd();
           String MD5_password = CrowdUtil.MD5(password);
           admin.setUserPswd(MD5_password);
   
           //生成创建时间
           Date date=new Date();
           SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-mm-dd HH:mm:ss");
           String format = simpleDateFormat.format(date);
   
           adminMapper.insert(admin);
   
   
       }
   ```

   

5. 配置Handler

  ```java

    @RequestMapping(value = "/Register.html",method = RequestMethod.POST)
    public String  Register(Admin admin){

        System.out.println("来了");
        adminServie.saveAdmin(admin);

        return   "redirect:/Admin/getPageInfo.html?pageNum="+ Integer.MAX_VALUE;

    }
  ```

异常处理器知识补充：![image-20201013203108695](/image-20201013203108695.png)

![image-20201013203116429](/image-20201013203116429.png)

结论就是：这两个异常处理器选择一个就可了，且当你开启了mvc：annotion-drven 的时候默认开始异常处理器，DispatcherServlet也会默认转配HandlerExceptionResolver

![image-20201013204026444](/image-20201013204026444.png)

![image-20201013204500256](/image-20201013204500256.png)

#### 修改

由于mybais源码对于唯一主键异常在内部就应经抛出了，所以

![image-20201013215411972](/image-20201013215411972.png)



### 6. 修改

# RBAC模型

## 为什么要有权限控制

如果没有权限控制，系统的功能完全不设防，全部暴露在所有的用户面前，当用户登录以后就可以适用所有的功能，这是不可以的

## 控制权限是什么

权限=“权利”+“限制”

## 如何进行权限控制

### 1. 定义资源

资源就是要被保护起来的功能，具体形式有url，handler方法，service方法，页面元素等等都可以定义为资源被保护起来

### 2.创建权限

一个功能复杂的项目包含很多资源文件，成千上万的资源都要一一进行权限操作过于麻烦，所以我们可以将相关资源打包成一个“权限”同时分配给不同的人

![image-20201105203520130](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201105203534.png)

### 3.创建角色

​		对于一个庞大的系统来说，资源与用户是不可预估的，将资源打包成“权限”是操作的简化，将用户分划分为不用的角色也是对操作的简化，否则针对一个一个的用户太麻烦了

​		所以角色就是用户的分组，分类。先给角色对应的权限，再然后把角色分配给用户，用户已角色的身份就享有了对应的权限

### 4.管理用户

系统的用户其实是人操作系统的登入的账号和密码

### 5.建立关联关系

权限→资源：单向多对多

​		从java类之间单向：从权限实体类可以获取资源对象的集合，但是资源不可以获取到权限

​        数据库表之间的多对多：

​                   一个权限可以包括多个资源

​                   一个资源可以分配给多个权限

角色→权限：单向多对多

​        从java类之间单向：从角色实体类可以获取权限对象的集合，但是反过来不可以

​        数据库表之间的多对多：

​                   一个角色可以拥有多个权限

​                   一个权限可以分配给多个角色

用户→角色：双向多对多

​	    从java类之间单向：可以通过用户获取它具备的角色，也可以看一个角色下包含那些用户

​        数据库表之间的多对多：

​                   一个角色可以包含多个用户

​                   一个用户可以充当多个角色



## 多对多在数据库呢之间的表示

### 1.没有中间表

![image-20201105212355130](/image-20201105212355130.png)

如果在建立外键fk上加入，表的关联关系数据，那么现在这个情况无法使用sql语句进行关联查询，mysql不会吧fk分为'1',' 2',' 3' 他只会把你当成一个字符串“123”

### 2..有中间表

![image-20201106210827551](/image-20201106210827551.png)

```sql
select t_studet.id,t_student.name from t_student 
left join t_inner on t_studen.id=t_inner.stuent_id 
left join t_subject on t_inner.subject_id=t_subject.id 
where
t_subjct.id=1
```

#### 2.1中间表的主键生成方式

* 另外设置主键

![image-20201106211048325](/image-20201106211048325.png)

* 联合主键

  ![image-20201106211200736](/image-20201106211200736.png)

注意：联合主键不可以重复

##  RBAC模型

### 1.概念

模型得核心就是`用户`、角色、权限互相关联，出来的系统就叫RBAC(基于角色的访问控制)，在一个RBAC中，一个用户可以充当多个角色，而一个角色又可以拥有多个权限

### 2. 模型

#### 1.RBAC0:

* RBAC模型的基础核心

#### 2.RBAC1: 继承

* 在RBAC0的基础上加入了，角色继承的关系，例如：在你办法得到会员的时候，你的白银会员可以去广告，黄金会员继承了白银会员的权限

#### 3.RBAC2：分离

![image-20201107133828384](/image-20201107133828384.png)

#### 4.RBAC3：结合

![image-20201107133843363](/image-20201107133843363.png)

## 扩展的RABC模型

![image-20201107133944251](/image-20201107133944251.png)



---

# Ajax工作模式

## 异步

### 1.图示

![image-20201107140527957](/image-20201107140527957.png)

### 2.代码

```html
<script>
    $(function (){
        $("#async").click(function (){

            //函数进入，执行
            console.log("ajax之前")
            // ajax请求
            $.ajax({
                url: "test/ajax/async.html",
                type: "post",
                dataType:"text",
                //success只会在服务器响应后执行
                success:function (resp){
                    console.log("ajxa内部")
                }
            })
            //普通请求，在你的ajax执行之后，执行
            console.log("ajax之后")

        })
    })
```

输出效果

![image-20201107140631366](/image-20201107140631366.png)





可以通过jQuery的函数，延后方法，**也证明确实success()函数和ajax外部函数是分开的*,所以谁先执行不一定*

```html
 //    可以通过jQuery的方法，执行延后,这也证明了，ajax的success和普通的函数方法确实不在同一个线程上
            setTimeout(function () {
                console.log("ajax之后")
            },6000)
```



## 同步

### 1.图示

![image-20201107150610168](/image-20201107150610168.png)

## 2.代码

![image-20201107150917710](/image-20201107150917710.png)

## 总结

* 同步：一个线程从头到尾按顺序执行
* 异步：多个线程同时执行，谁也不等谁

小知识点：

* $.post() $.get() $.ajax 区别，但是$.post() $.get()这两个都是服务器成功处理之后，响应状态码必须是200才可以执行回调函数
  $.ajax()不管响应状态码是多少，都可处理

---

# 角色维护

## 角色分页操作

### 思路

![image-20201107154844581](/image-20201107154844581.png)

### 后端

#### 1.创建数据库

```mysql
create table t_role (
    id   int PRIMARY KEY,
    name CHAR(20)
                    );
```

#### 2.逆向工程生成对应的资源，并修改配置文件，进行concat查询

修改逆向工程配置文件，指定对应的表

![image-20201107160216477](/image-20201107160216477.png)

修改mapper.xml

新的语句既可以使用条件查询，也可以非条件查询![ ](/image-20201107172428344.png)

#### 插入数据

##### 写入service和handler的代码

Service

```java
package Service.Impl;


import CrowedFunding_Entity.Role;
import Mapper.RoleMapper;
import Service.service.RoleService;
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @author lenovo
 */
@Service
public class RoleServiceImpl implements RoleService {

    @Autowired
    private RoleMapper roleMapper;


    @Override
    public PageInfo getPage(String keyword, int pageNum, int pageSize) {
        //1.开启分页功能
        PageHelper.startPage(pageNum, pageSize);
        //2.获取数据
        List<Role> roles = roleMapper.selectRoleByKeyword(keyword);
        //3.创建分页类
        PageInfo pageInfo=new PageInfo(roles);
        
        return pageInfo;
    }
}

```



handler	

```java
package MVC.Controller;

import CrowedFunding_Entity.Role;
import Service.service.RoleService;
import Util.ResultEntity;
import com.github.pagehelper.PageInfo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

/**
 * @author lenovo
 */
@Controller
@RequestMapping("/Role")
public class RoleHandler {

     @Autowired
    private RoleService roleService;

     @RequestMapping("getRoles.json")
     @ResponseBody
    public ResultEntity<PageInfo<Role>> getRoles(@RequestParam("keyword") String keyword, @RequestParam("pageNum") int pageNum,@RequestParam("pageSize") int pageSize){

         //获取数据
         PageInfo<Role> page = roleService.getPage(keyword, pageNum, pageSize);

         //封装到结果集，如果成功返回数据，异常则交给异常处理器

         return  ResultEntity.successWithData(page);
     }

}

```

#### 小知识点-UUID

UUID简介

* UUID的含义是通用的唯一识别码，这是一个软件构建的标准
* UUID的目的是让分布式系统的所有元素，都具有唯一的辨识资讯，而不需要透过中央的控制端来做辨识资讯的指定.
* 如此一来每个人都可以建立不与其它人冲突的 UUID。在这样的情况下，就不需考虑数据库建立时的名称重复问题。

UUID组成

* UUID保证对在同一时空中的所有机器都是唯一的。通常平台会提供生成的API。

* 按照开放软件基金会(OSF)制定的标准计算，用到了以太网卡地址、纳秒级时间、芯片ID码和许多可能的数字。

* UUID由以下几部分的组合：

​          （1）当前日期和时间，UUID的第一个部分与时间有关，如果你在生成一个UUID之后，过几秒又生成一个UUID，则第一个部分不                          

​                   同，其余相同。

​          （2）时钟序列。

​          （3）全局唯一的IEEE机器识别号，如果有网卡，从网卡MAC地址获得，没有网卡以其他方式获得。

*  UUID的唯一缺陷在于生成的结果串会比较长。关于UUID这个标准使用最普遍的是微软的GUID(Globals Unique Identifiers)。

   标准的UUID格式为：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (8-4-4-4-12)

代码：生成多个UUID

```java
   @Test
    public void testInsetRole(){

        int a=0;
        while(a<10){
            String s= UUID.randomUUID().toString();
            System.out.println(s);
            a++;
        }
```

运行结果：

![image-20201110152529280](/image-20201110152529280.png)

转自：https://www.cnblogs.com/java-class/p/4727698.html



---



### 前端

#### 修改视图控制器

![image-20201110163414697](/image-20201110163415366.png)

#### 修改前端超链接以及创建role-page页面

![image-20201110163816938](/image-20201110163816938.png)

role-page

建立window全局变量

```jsp
    <%--
      Created by IntelliJ IDEA.
      User: lenovo
      Date: 2020/10/9
      Time: 18:14
      To change this template use File | Settings | File Templates.
    --%>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>

    <!DOCTYPE html>
    <html lang="UTF-8">
    <%@include file="include-head.jsp" %>
<%--    引入自定义script文件--%>
    <script type="text/javascript" src="static/Myjs/my-role.js"></script>

    <script>
        $(function (){
            //建立window全局变量
            window.pageNum=2
            window.keyWord=""
            window.pageSize=5

           //    获取后端数据
          var pageInfo= getPageInfoRemote()
            // layer.msg(pageInfo)
          //填充数据
            FillBody(pageInfo)
        })
    </script>
    <body>
    <%@include file="include-nav.jsp" %>
    <div class="container-fluid">
        <div class="row">
            <%@include file="include-setbar.jsp" %>
            <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
                <div class="panel panel-default">
                    <div class="panel-heading">
                        <h3 class="panel-title"><i class="glyphicon glyphicon-th"></i> 数据列表</h3>
                    </div>
                    <div class="panel-body">
                        <form class="form-inline" role="form" style="float:left;">
                            <div class="form-group has-feedback">
                                <div class="input-group">
                                    <div class="input-group-addon">查询条件</div>
                                    <input class="form-control has-success" type="text" placeholder="请输入查询条件">
                                </div>
                            </div>
                            <button type="button" class="btn btn-warning"><i class="glyphicon glyphicon-search"></i> 查询</button>
                        </form>
                        <button type="button" class="btn btn-danger" style="float:right;margin-left:10px;"><i class=" glyphicon glyphicon-remove"></i> 删除</button>
                        <button type="button" class="btn btn-primary" style="float:right;" onclick="window.location.href='form.html'"><i class="glyphicon glyphicon-plus"></i> 新增</button>
                        <br>
                        <hr style="clear:both;">
                        <div class="table-responsive">
                            <table class="table  table-bordered">
                                <thead>
                                <tr >
                                    <th width="30">#</th>
                                    <th width="30"><input type="checkbox"></th>
                                    <th>名称</th>
                                    <th width="100">操作</th>
                                </tr>
                                </thead>
                                <tbody id="RoleBody">

                                </tbody>
                                <tfoot>
                                <tr >
                                    <td colspan="6" align="center">
                                        <div id="Pagination" class="pagination"><!-- 这里显示分页 --></div>

                                    </td>
                                </tr>
                                </tfoot>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    </body>
    </html>

```



#### 编写js文件

```js
//生成函数
function generate(){

    //1.获取数据
    var pageInfo=getPageInfoRemote();
    //2.填充表格
    FillBody(pageInfo)

}
//访问远程端程序获取pageInfo
function getPageInfoRemote(){

    var resp=$.ajax({
        url: "Role/getRoles.json",
        data: {
            "pageNum" : window.pageNum,
            "keyWord" : window.keyWord,
            "pageSize" : window.pageSize
        },
        type : "POST",
        dataType : "JSON",
        async : false,
        success:function(response){
            return response
        },
        error:function(response){
            return response
        }
    })
   //获取状态码
   var status =  resp.status

    console.log(resp)
    //当前响应码不为200表示响应失败，判断服务器是否请求成功
    if(status!=200){
        layer.msg("响应失败")
        return null
    }

    //获取json数据
    var ResultEntity=resp.responseJSON

    //判断是否获取数据成功
    if(ResultEntity.result!="SUCCESS"){
          layer.msg("服务器访问成功，但是数据获取失败，错误："+ResultEntity.message)
        //return null相当于停止执行下面的方法
        return null;
    }

    //这里表示运行成功，获取页面元素
    var pageInfo=ResultEntity.data
    return pageInfo

}
//填充表格
function FillBody(pageInfo){
    // 每次append之前要清除否则会追加
    $("#RoleBody").empty()

    //检查pageInfo，true:表示运行成功，pageInfo带有数据,false:表示获取失败，在tbody填入失败信息
    if(pageInfo==null||pageInfo==undefined||pageInfo.list==null||pageInfo.list.length == 0){
        $("#RoleBody").append("<tr><td colspan='4'>抱歉！没有查询到您要的信息</td></tr>")
        return null
    }

    for(var i = 0; i < pageInfo.list.length; i++){

        //数据成员
        var role=pageInfo.list[i]
        var roleId=role.id
        var roleName=role.name
        //真实表格数据
        var IdTd="<td>"+roleId+"</td>"
        var checkBoxTd="<td><input type='checkbox'></td>"
        var formNameTd="<td>"+roleName+"</td>"

        var checkBtn="<button type='button' class='btn btn-success btn-xs'><i class='glyphicon glyphicon-check'></i></button>"
        var pencilBtn="<button type='button' class='btn btn-primary btn-xs'><i class='glyphicon glyphicon-pencil'></i></button>"
        var removeBtn="<button type='button' class='btn btn btn-danger btn-xs'><i class='glyphicon glyphicon-remove'></i></button>"

        var buttonTd="<td>"+checkBtn+" "+pencilBtn+" "+removeBtn+"</td>"
        var tr="<tr>"+IdTd+checkBoxTd+formNameTd+buttonTd+"</tr>"

        $("#RoleBody").append(tr)


    }

      generateNavigator(pageInfo)

}
//翻页时的回调函数
function paginationCallBack(pageIndex,JQuery){
    //根据pageIndex计算得到的PageNum
    window.pageNum=pageIndex+1;

    //调用分页函数
    generate()

    //    由于每个页码按钮都是超链接，所以在这个函数最后取消超链接的默认行为
    return false
}
//生成页码导航栏
function generateNavigator(pageInfo){


    //总记录数
    var totalRecord=pageInfo.total
    //声明一个JSON对象存储Pagination要设置的属性
    var properties={
        num_edge_entries: 3,                            //边缘页数
        num_display_entries: 5,                         //主体页数
        items_per_page:pageInfo.pageSize,               // 每页显示1项
        callback:paginationCallBack,                   //回调函数
        current_page:pageInfo.pageNum-1,                //因为Pagination这个组件索引是从0开始的，所以我们要减一
        prev_text: "上一页",                             //上一页文本显示的文本
        next_text: "下一页",
    };

    $("#Pagination").pagination(totalRecord,properties);

}

```

​	





## 关键词查询

### 思路

![image-20201113191010496](/image-20201113191010496.png)



### 代码：

```js
 //关键字查询
            $("#searchBykeyWord").click(function (){
                //获取keyWord的内容，并赋给window.keyWord
                window.keyWord=$("#keyWord").val()
                alert(window.keyWord)
                //刷新页面
                generate()
            })
```



## 角色新增

### 思路

![image-20201114154411998](/image-20201114154411998.png)



### 代码

#### 前端

```js

            //保存
            $("#MyModalSave").click(function (){

                //获取表单数据，
                var RoleName=$("#RoleName").val()

                alert(RoleName)
                $.ajax({
                    url : "Role/addRole.json",
                    data : {
                        "RoleName" : RoleName
                    },
                    type : "POST",
                    datatype: "JSON",
                    async : false,
                    success : function (response){
                       var result=response.result

                        if(result=="SUCCESS"){
                            layer.msg("操作成功")
                        }
                        //重新加载分页数据
                        //设置最大页数
                        window.pageNum = 695418
                        generate()
                    },
                    error : function (response){
                        var result=response.result

                        if(result=="FAILSE"){
                            layer.msg("操作失败")
                        }
                    }
                 })
                //关闭模态框
                $('#MyModal').modal('hide')
                //刷新输入栏，方便下次操作
                $("#keyWord").val(" ")


            })
```

#### 后端

hadnler

```java

     @PostMapping("/addRole.json")
     @ResponseBody
     public ResultEntity<String> addRole(@RequestParam("RoleName") String RoleName) {


         System.out.println("Role:"+RoleName);

//         判断书是否为空
         if(!StringUtils.isEmpty(RoleName)){
         roleService.saveRole(new Role(null, RoleName));

         return  ResultEntity.successWithoutData();
         }

         return ResultEntity.Failed("失败了");

     }
```

​	

## 用户更新

### 思路

![image-20201115152230869](/image-20201115152230869.png)



### 前端

```js
     //在页面上的铅笔绑定单机响应函数，目的是打开模态框
            //传统的事件绑定方式只能在第一个页面有效，翻页后失效
            // $(".pencil-btn").click(function (){
            //
            //     alert($("#editBtn").attr(id))
            //
            //     $('#MyModalUpdate').modal('show')
            //
            // })

            // 使用Jquery的on函数解决这个问题
            // 首先找到所有动态生成的元素所依附的静态元素，例如role内容依附的静态元素时tbody
               /*
               *  on函数参数：
               *           1.事件类型
               *           2.真正要绑定的事件的元素的选择器
               *           3.响应函数
               * */
            $("#RoleBody").on("click",".pencil-btn",function (){
                // this表示当前按钮，按钮的父组件是<td>
                // prev():用来上一个同级元素
                // 获取当前属性的name
                window.roleName= $(this).parent().prev().text()

                // 获取id
                window.roleId= $(".pencil-btn").attr("id")
                // 值的回显
                $('#MyModalUpdate #RoleName').val(window.roleName)
                // 打开模态框
                $('#MyModalUpdate').modal('show')

            });

            $("#MyModalupdateBtn").click(function (){

              var rolename=  $('#MyModalUpdate #RoleName').val()


                $.ajax({
                    url : "Role/updateRole.json",
                    type: "POST",
                    data : {
                            "name" : rolename,
                            "id" :  window.roleId
                           },
                    datatype: "JSON",
                    success:function (resp){
                        //加载数据
                        console.log(resp)
                        //关闭模态框
                        $('#MyModalUpdate').modal('hide')
                        //清空数据
                        $('#MyModalUpdate #RoleName').val("")
                        //更新页面
                        generate()

                    }
                })           })
```



### 后端

省略

##  用户删除

### 目标

前端的“单挑删除”和“批量三处”在后端合并为同一个操作，合并依据：不论是单条删除的id还是多个删除的id都是放在数组中，后端完全根据id的数组进行删除

### 思路

![image-20201116180454501](/image-20201116180454501.png)

### 代码

####  前端

```js
  /**删除操作*/
            $("#deleteModalBtn").click(function (){

                  //将JSON数组中转换为json数据

                var requestBody=JSON.stringify(window.roleIdArray)

                layer.msg(requestBody)

                  $.ajax({
                      url:"Role/deleteRole.json",
                      data:requestBody,
                      datatype:"json",
                      contentType: "application/json;charset=UTF-8",
                      type: "POST",
                      success:function (resp){
                          //加载数据
                          console.log(resp)
                          //关闭模态框
                          $('#MyModalDelete').modal('hide')
                          //更新页面
                          generate()
                      },
                      error : function (response) {

                              layer.msg("操作失败")

                      }

                  })
            })
                //单例删除
                $("#RoleBody").on("click",".removeBtn",function (){

                    // this表示当前按钮，按钮的父组件是<td>
                    // prev():用来上一个同级元素
                    // 获取当前属性的name
                    window.roleName= $(this).parent().prev().text()

                    // 获取id
                    window.roleId= $(".removeBtn").attr("id")

                    var roleArray=[{
                        roleId:window.roleId,
                        roleName:window.roleName
                    }]

                    layer.msg(roleArray.length)
                    confirmModalAlert(roleArray)

                });
                //全选或者全部选删除
                $("#summaryBox").click(function (){

                     //获取单个选择
                    var checkBoxStatus=this.checked;



                    $(".itemCheckBox").prop("checked",checkBoxStatus)
                })



            //全选或全不选的优化
            $("#RoleBody").on("click",".itemCheckBox",function (){

              // 选择的checkBox
              var itemCheckBoxNum=$(".itemCheckBox:checked").length
              //所有的checkBox
              var totalCheckBox=$(".itemCheckBox").length

                //进行判定，倘若达到相等，那么所有的都会被选中
                $("#summaryBox").prop("checked",itemCheckBoxNum==totalCheckBox)

            });

            $("#deleteBtn").click(function (){

                var  roleArray=[]
                //遍历素有的被选中的CheckBox
                $(".itemCheckBox:checked").each(function (){

                    var roleName=$(this).parent().next().text()
                    var roleId = this.id

                    roleArray.push({
                        roleId:roleId,
                        roleName:roleName

                    })
                })


                confirmModalAlert(roleArray)

                // generate()

            })


```

####  后端

省略

---

# 树形结构图基础

## 节点类型

![image-20201122220002047](/image-20201122220002047.png)

约定：整个树形结构层数不可超过三层，否则太复杂。

## 在数据库中表示树形结构

### 1.创建数据库

```sql
create table t_menu
(
    id int(11) not null auto_increment, 
    pid int(11), name varchar(200), 
    url varchar(200),
    icon varchar(200), primary key (id)
);
```



```sql
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('1',NULL,'系统权限菜单','glyphicon
glyphicon-th-list',NULL);
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('2','1',' 控 制 面 板 ','glyphicon
glyphicon-dashboard','main.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('3','1','权限管理','glyphicon glyphicon
glyphicon-tasks',NULL);
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('4','3',' 用 户 维 护 ','glyphicon
glyphicon-user','user/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('5','3',' 角 色 维 护 ','glyphicon
glyphicon-king','role/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('6','3',' 菜 单 维 护 ','glyphicon
glyphicon-lock','permission/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('7','1',' 业 务 审 核 ','glyphicon
glyphicon-ok',NULL);
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('8','7',' 实 名 认 证 审 核 ','glyphicon
glyphicon-check','auth_cert/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('9','7',' 广 告 审 核 ','glyphicon
glyphicon-check','auth_adv/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('10','7',' 项 目 审 核 ','glyphicon
glyphicon-check','auth_project/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('11','1',' 业 务 管 理 ','glyphicon
glyphicon-th-large',NULL);
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('12','11',' 资 质 维 护 ','glyphicon
glyphicon-picture','cert/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('13','11',' 分 类 管 理 ','glyphicon
glyphicon-equalizer','certtype/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('14','11',' 流 程 管 理 ','glyphicon
glyphicon-random','process/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('15','11',' 广 告 管 理 ','glyphicon
glyphicon-hdd','advert/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('16','11',' 消 息 模 板 ','glyphicon
glyphicon-comment','message/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('17','11',' 项 目 分 类 ','glyphicon
glyphicon-list','projectType/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('18','11',' 项 目 标 签 ','glyphicon
glyphicon-tags','tag/index.htm');
insert into `t_menu` (`id`, `pid`, `name`, `icon`, `url`) values('19','1',' 参 数 管 理 ','glyphicon
glyphicon-list-alt','param/index.htm');
```



![image-20201123173105740](/image-20201123173105740.png)

### 关联方式

在表内通过Pid字段关联到父节点Id字段，建立父子关系

![image-20201123171705993](/image-20201123171705993.png)

根节点的Pid为null，因为是**老父亲**

## 在java类中表示树形结构

### 基本方式

在Menu类中使用List<Menu>children属性存储当前节点的子节点

* Menu表示任意节点类
* 所有Menu的集合是一个整体树形结构

### 为了配合zTree所需要添加的属性

Pid属性：找到父节点

name属性：作为节点名称

icon属性：当前节点使用的图标

open属性：控制节点是否默认打开

url属性：点击节点跳转的位置



# 菜单维护

## 页面显示树形结构以及组件

### 目标

将数据库中查询的数据在页面以树状结构展示

### 思路

数据库查询全部→Java对象组装→页面使用zTree显示

### 代码

#### 1.逆向工程

```java
package CrowedFunding_Entity;

import java.util.ArrayList;
import java.util.List;

public class Menu {

    /**主键*/
    private Integer id;
    /**父节点ID*/
    private Integer pid;

    /**节点名称*/
    private String name;

    /**点击对应的跳转的页面*/
    private String url;
    /**节点图标*/
    private String icon;


    private Boolean open = true;

    /**防止children.方法 空指针异常*/
    private List<Menu> children=new ArrayList<Menu>();

    public boolean isOpen() {
        return open;
    }

    public void setOpen(boolean open) {
        this.open = open;
    }

    public List<Menu> getChildren() {
        return children;
    }

    public void setChildren(List<Menu> children) {
        this.children = children;
    }

    public Menu() {
    }

    public Menu(Integer id, Integer pid, String name, String url, String icon, Boolean open, List<Menu> children) {
        this.id = id;
        this.pid = pid;
        this.name = name;
        this.url = url;
        this.icon = icon;
        this.open = open;
        this.children = children;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Integer getPid() {
        return pid;
    }

    public void setPid(Integer pid) {
        this.pid = pid;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name == null ? null : name.trim();
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url == null ? null : url.trim();
    }

    public String getIcon() {
        return icon;
    }

    public void setIcon(String icon) {
        this.icon = icon == null ? null : icon.trim();
    }

    @Override
    public String toString() {
        return "Menu{" +
                "id=" + id +
                ", pid=" + pid +
                ", name='" + name + '\'' +
                ", url='" + url + '\'' +
                ", icon='" + icon + '\'' +
                ", open=" + open +
                ", children=" + children +
                '}';
    }
}
```



#### 2.service代码

```java
package MVC.Controller;


import CrowedFunding_Entity.Menu;
import Service.service.MenuService;
import Util.ResultEntity;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

@Controller
@RequestMapping("/Menu")
public class MenuHandler {

    @Autowired
    private MenuService menuService;


    @ResponseBody
    @RequestMapping("/getWhole.json")
    public ResultEntity<Menu> getWholeNew(){


        List<Menu> all = menuService.getAll();

        Menu root =null;

        Map<Integer, Menu> menuList = new HashMap<>();


        for (Menu menu: all) {

//           将数据存入Map中通过Pid和map中的键id对应 来和value取值
            menuList.put(menu.getId(), menu);

        }

        for(Menu menu:all){

            if(menu.getPid()==null){
                root =menu;

                continue;
            }

            //表示当前的map里面的menu是你的当foreach里面的父节点
            if(menuList.containsKey(menu.getPid())){

                //获取父节点
                Menu menuFather = menuList.get(menu.getPid());

                List<Menu> children = menuFather.getChildren();

                children.add(menu);

                continue;

            }
        }
        return ResultEntity.successWithData(root);
    }


//   //时间复杂度太大，所以不适用
//    public ResultEntity<Menu> getWholeOld(){
//
//        /**1.查询全部的Menu对象*/
//        List<Menu> all = menuService.getAll();
//
//        /**声明一个变量用来存储找到的根节点*/
//
//        Menu root=null;
//
//        for (Menu menu:all){
//
//            //获取menu的pid
//            Integer pid = menu.getPid();
//
//            //判断当前的Pid是否为空,因为只有根节点的PId为空
//            if(pid==null){
//                root=menu;
//
//                //停止本次循环，继续下次循环
//                continue;
//            }
//            //如果不为空，说明当前的节点menu有父节点，要建立父子关系
//            for (Menu menuFather:all) {
//                // 表示当前的id等于Pid，也就是当前的menuFather是menu的父类
//                if(pid.equals(menu.getId())){
//
//                    //将子节点存入父节点的children集合
//                    List<Menu> children = menuFather.getChildren();
//
//                    children.add(menu);
//
//                    break;
//                }
//            }
//
//        }


//     return ResultEntity.successWithData(root);
//    }


}

```

运行结果

![image-20201124122639898](/image-20201124122639898.png)



#### 引入主页面和zTree

##### zTree

![image-20201124125409433](/image-20201124125409433.png)





##### 主页面

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/9
  Time: 18:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
    <%--引入Ztree--%>  
<link rel="stylesheet" href="ztree/zTreeStyle.css">
<script src="ztree/jquery.ztree.all-3.5.min.js"></script>
<body>
<%@include file="include-nav.jsp" %>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">

            <div class="panel panel-default">
                <div class="panel-heading"><i class="glyphicon glyphicon-th-list"></i> 权限菜单列表
                    <div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i
                            class="glyphicon glyphicon-question-sign"></i></div>
                </div>
                <div class="panel-body">
                    <ul id="treeDemo" class="ztree">
                    </ul>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>

```



##### 使用假数据生成树形结构

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/9
  Time: 18:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
<%--引入Ztree--%>
<link rel="stylesheet" href="static/ztree/zTreeStyle.css">
<script src="static/ztree/jquery.ztree.all-3.5.min.js"></script>
<script type="text/javascript" >
    $(function (){

    //    1.创建JSON对象用于存储对xTree所做的设置
        var settings = {}

    //    2.准备生成属性结构的JSON数据
        var zNodes =[
            { name:"父节点1 - 展开", open:true,
                children: [
                    { name:"父节点11 - 折叠",
                        children: [
                            { name:"叶子节点111"},
                            { name:"叶子节点112"},
                            { name:"叶子节点113"},
                            { name:"叶子节点114"}
                        ]},
                    { name:"父节点12 - 折叠",
                        children: [
                            { name:"叶子节点121"},
                            { name:"叶子节点122"},
                            { name:"叶子节点123"},
                            { name:"叶子节点124"}
                        ]},
                    { name:"父节点13 - 没有子节点", isParent:true}
                ]},
            { name:"父节点2 - 折叠",
                children: [
                    { name:"父节点21 - 展开", open:true,
                        children: [
                            { name:"叶子节点211"},
                            { name:"叶子节点212"},
                            { name:"叶子节点213"},
                            { name:"叶子节点214"}
                        ]},
                    { name:"父节点22 - 折叠",
                        children: [
                            { name:"叶子节点221"},
                            { name:"叶子节点222"},
                            { name:"叶子节点223"},
                            { name:"叶子节点224"}
                        ]},
                    { name:"父节点23 - 折叠",
                        children: [
                            { name:"叶子节点231"},
                            { name:"叶子节点232"},
                            { name:"叶子节点233"},
                            { name:"叶子节点234"}
                        ]}
                ]},
            { name:"父节点3 - 没有子节点", isParent:true}

        ];

            //表示zTree的初始化开始,初始化树形结构
            $.fn.zTree.init($("#treeDemo"), settings, zNodes);



    })
</script>
<body>
<%@include file="include-nav.jsp" %>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">

            <div class="panel panel-default">
                <div class="panel-heading"><i class="glyphicon glyphicon-th-list"></i> 权限菜单列表
                    <div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i
                            class="glyphicon glyphicon-question-sign"></i></div>
                </div>
                <div class="panel-body">
                    <ul id="treeDemo" class="ztree">
                    </ul>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>

```



##### 实际代码

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/9
  Time: 18:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
<%--引入Ztree--%>
<link rel="stylesheet" href="static/ztree/zTreeStyle.css">
<script src="static/ztree/jquery.ztree.all-3.5.min.js"></script>
<script type="text/javascript" >
    $(function (){

    //    1.创建JSON对象用于存储对xTree所做的设置
        var settings = {}

    //    准备Json数据，数据来源是Ajax请求得到
        $.ajax({
            url:"Menu/getWhole.json",
            datatype:"JSON",
            type: "POST",
            success:function (response){

                   var result= response.result
                if(result=="SUCCESS"){
                       var zNodes = response.data
                    //表示zTree的初始化开始,初始化树形结构
                    $.fn.zTree.init($("#treeDemo"), settings, zNodes);
                }
                if(result=="FAILED"){
                    layer.msg(response.message)
                }
            }
        })

    //    2.准备生成属性结构的JSON数据
    //     var zNodes =[
    //         { name:"父节点1 - 展开", open:true,
    //             children: [
    //                 { name:"父节点11 - 折叠",
    //                     children: [
    //                         { name:"叶子节点111"},
    //                         { name:"叶子节点112"},
    //                         { name:"叶子节点113"},
    //                         { name:"叶子节点114"}
    //                     ]},
    //                 { name:"父节点12 - 折叠",
    //                     children: [
    //                         { name:"叶子节点121"},
    //                         { name:"叶子节点122"},
    //                         { name:"叶子节点123"},
    //                         { name:"叶子节点124"}
    //                     ]},
    //                 { name:"父节点13 - 没有子节点", isParent:true}
    //             ]},
    //         { name:"父节点2 - 折叠",
    //             children: [
    //                 { name:"父节点21 - 展开", open:true,
    //                     children: [
    //                         { name:"叶子节点211"},
    //                         { name:"叶子节点212"},
    //                         { name:"叶子节点213"},
    //                         { name:"叶子节点214"}
    //                     ]},
    //                 { name:"父节点22 - 折叠",
    //                     children: [
    //                         { name:"叶子节点221"},
    //                         { name:"叶子节点222"},
    //                         { name:"叶子节点223"},
    //                         { name:"叶子节点224"}
    //                     ]},
    //                 { name:"父节点23 - 折叠",
    //                     children: [
    //                         { name:"叶子节点231"},
    //                         { name:"叶子节点232"},
    //                         { name:"叶子节点233"},
    //                         { name:"叶子节点234"}
    //                     ]}
    //             ]},
    //         { name:"父节点3 - 没有子节点", isParent:true}
    //
    //     ];





    })
</script>
<body>
<%@include file="include-nav.jsp" %>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">

            <div class="panel panel-default">
                <div class="panel-heading"><i class="glyphicon glyphicon-th-list"></i> 权限菜单列表
                    <div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i
                            class="glyphicon glyphicon-question-sign"></i></div>
                </div>
                <div class="panel-body">
                    <ul id="treeDemo" class="ztree">
                    </ul>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>

```



##### 防止访问页面点了去404

![image-20201125110002780](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201125110010.png)

```js
    1.创建JSON对象用于存储对xTree所做的设置
        var settings = {
            view:{
                "addDiyDom":myAddDiyDom
            },
            //设置url为找不到的字符串，防止其乱跳转
            data:{
                key:{
                    url:"giao"
                }
            }
        }

```



原理

![image-20201124163104964](/image-20201124163104964.png)



##### 添加按钮组件

###### 按钮的规则

* level0：根节点
  * 添加子节点
* level1：分支节点
  * 修改
  * 添加子节点
  * 没有子节点：可以删除
  * 有子节点：不可以删除
* level3：叶子节点
  * 修改
  * 删除

###### 代码

```js
//鼠标移出节点范围移除按钮组
function myRemoveHoverDom(treeId, treeNode) {

    var btnGroupId = treeNode.tId + "_btnGrp"

    //移除自己本身
    $("#" + btnGroupId).remove()
}

//鼠标移入节点范围添加按钮组
function myAddHoverDom(treeId, treeNode) {

//    按钮组的标签结构：<span><a><i></i></a></span>
//    按钮出现的位置：节点中treeDemo_n_a超链接的后面

//    为了在需要移除按钮能够净赚定位到按钮组所在<span>。需要Span设置有规律的id
    var btnGroupId = treeNode.tId + "_btnGrp"
//    找到节点按钮
    var tID = treeNode.tId + "_a"


    //按钮的准备
    var addBtn = "<a class=\"btn btn-info dropdown-toggle btn-xs\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-plus rbg \"></i></a>"
    var deleteBtn = "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-times rbg \"></i></a>"
    var editBtn = "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\" title=\"修改权限信息\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-edit rbg \"></i></a>"


    //将组件加入到超连接节点按钮的后面

    //判断目前节点的级别
    var level = treeNode.level

    // alert(level)
    var htmlBtn = ""
    switch (level) {
        case 0:
            break;
            htmlBtn = addBtn + ""
        case 1:
            if (treeNode.children.length == 0) {
                htmlBtn = addBtn + "" + deleteBtn + "" + editBtn

                break;
            } else {

                htmlBtn = addBtn + "" + editBtn
                break;
            }

        case 2:
            htmlBtn = deleteBtn + "" + editBtn
            break;
    }


    //判断以前是否已经添加了按钮组,id为btnGroupId的组件表示已经加入组件了
    if ($("#" + btnGroupId).length > 0) {
        return;
    }
    // alert(htmlBtn)
    $("#" + tID).after("<span id='" + btnGroupId + "'>" + htmlBtn + "</span>")


}

//加载node 并且修改图标
function myAddDiyDom(treeId, treeNode) {

    //treeId就是treeDemo 也就是整个树形结构附着的id模拟
    console.log("treeId" + treeId)

    //每一个treeNode代表一个节点  ，也就是后端查询得到的Menu对象的全部属性
    console.log(treeNode)

    //根据控制图标的Span标签的id找到这个Span标签

    //例子id="treeDemo_4_a"
    //解析：ul的id_当前节点的序号_功能
    // ul的id_当前节点的序号也就是Tid
    var id = treeNode.tId + "_ico"

    //删除旧的class
    $("#" + id).removeClass()

    //添加新的class

    $("#" + id).prop("class", treeNode.icon)
}


```





##### 树形结构生成函封装到函数

* 每当我们每一次请求的时候，我们都会重新获取一次数据，所以我们需要获取属性结构封装到Mymenu.js函数里面去

  ![image-20201125104946921](/image-20201125104946921.png)

```js
js:
//获取树形结构
function generateTree(settings,zNodes){
    $.ajax({
        url:"Menu/getWhole.json",
        datatype:"JSON",
        type: "POST",
        success:function (response){

            var result= response.result
            if(result=="SUCCESS"){
                var zNodes = response.data
                //表示zTree的初始化开始,初始化树形结构
                $.fn.zTree.init($("#treeDemo"), settings, zNodes);
            }
            if(result=="FAILED"){
                layer.msg(response.message)
            }
        }
    })

}

html:
        generateTree(settings)
```



## 给节点的组件绑定单击函数

### 测试

```js

    //    给节点按钮 添加按钮绑定事件
        $("#treeDemo").on("click",".addBtn",function (){
            alert("addNtn")

            //return false 为了防止他乱跑，测试方便
            return false;
        })
```



### 添加模态框

#### 目标

给当前节点添加子节点，保存到数据库并刷新树形结构的显示。

#### 思路

![image-20201126110254881](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201126110302.png)

#### 代码

##### 前端

###### 添加增删改模态框

![image-20201126165944930](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201126165952.png)

![image-20201126170000406](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201126170001.png)



##### 绑定添加函数

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
<%--引入Ztree--%>
<link rel="stylesheet" href="static/ztree/zTreeStyle.css">
<script src="static/ztree/jquery.ztree.all-3.5.min.js"></script>
<script src="static/Myjs/my-menu.js"></script>
<script type="text/javascript" >
    $(function (){

    //    1.创建JSON对象用于存储对xTree所做的设置
        var settings = {
            view:{
                "addDiyDom":myAddDiyDom,
                "removeHoverDom":myRemoveHoverDom,
                "addHoverDom":myAddHoverDom
            },
            data:{
                key:{
                    url:"giao"
                }
            }
        }

    //    准备Json数据，数据来源是Ajax请求得到
        generateTree(settings)

    //    给节点按钮 添加按钮绑定事件
        $("#treeDemo").on("click",".addBtn",function (){

            // 加入节点后要分配节点,所以要加入对应的父id，也就当前节点的id
               window.menuPid=this.id

            // alert(window.menuPid)
              $("#menuAddModal").modal('show')
            //return false 为了防止他乱跑，测试方便



            return false;
        })



        $("#menuSaveBtn").click(function (){

          //  选定图标
          var MenuFunction=$(".modal-body :checked").val()
          //  选定名称
          var MenuName=$("#menuAddModal [name=name]").val()
          //  选定url
          var MenuUrl=$("#menuAddModal [name=url]").val()




            $.ajax({
               url:"Menu/saveMenu.json",
               data: {
                  "name":MenuName,
                  "pid":window.menuPid,
                  "url":MenuUrl,
                  "icon":MenuFunction
               },
                type:"POST",
                datatype:"json",
                success:function (response) {
                   console.log(response)
                   layer.msg(response.staus)
                },
                error:function (response){
                   layer.msg(response.staus)
                }

            })

            $("#menuAddModal").modal('hide')

            //重新生成树形结构
            generateTree(settings)

            //清空表单
            $("#menuResetBtn").click()

        })

    /**   2.准备生成属性结构的JSON数据
        var zNodes =[
            { name:"父节点1 - 展开", open:true,
                children: [
                    { name:"父节点11 - 折叠",
                        children: [
                            { name:"叶子节点111"},
                            { name:"叶子节点112"},
                            { name:"叶子节点113"},
                            { name:"叶子节点114"}
                        ]},
                    { name:"父节点12 - 折叠",
                        children: [
                            { name:"叶子节点121"},
                            { name:"叶子节点122"},
                            { name:"叶子节点123"},
                            { name:"叶子节点124"}
                        ]},
                    { name:"父节点13 - 没有子节点", isParent:true}
                ]},
            { name:"父节点2 - 折叠",
                children: [
                    { name:"父节点21 - 展开", open:true,
                        children: [
                            { name:"叶子节点211"},
                            { name:"叶子节点212"},
                            { name:"叶子节点213"},
                            { name:"叶子节点214"}
                        ]},
                    { name:"父节点22 - 折叠",
                        children: [
                            { name:"叶子节点221"},
                            { name:"叶子节点222"},
                            { name:"叶子节点223"},
                            { name:"叶子节点224"}
                        ]},
                    { name:"父节点23 - 折叠",
                        children: [
                            { name:"叶子节点231"},
                            { name:"叶子节点232"},
                            { name:"叶子节点233"},
                            { name:"叶子节点234"}
                        ]}
                ]},
            { name:"父节点3 - 没有子节点", isParent:true}

        ];
     */

    })
</script>
<body>
<%@include file="include-nav.jsp" %>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">

            <div class="panel panel-default">
                <div class="panel-heading"><i class="glyphicon glyphicon-th-list"></i> 权限菜单列表
                    <div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i
                            class="glyphicon glyphicon-question-sign"></i></div>
                </div>
                <div class="panel-body">
                    <ul id="treeDemo" class="ztree">
                    </ul>
                </div>
            </div>
        </div>
    </div>
</div>
<%@include file="modal-menu-add.jsp"%>
<%@include file="modal-menu-edit.jsp"%>
<%@include file="modal-menu-confirm.jsp"%>
</body>
</html>

```

my-menu.js

```js
//获取树形结构
function generateTree(settings,zNodes){
    $.ajax({
        url:"Menu/getWhole.json",
        datatype:"JSON",
        type: "POST",
        success:function (response){

            var result= response.result

            if(result=="SUCCESS"){
                var zNodes = response.data
                //表示zTree的初始化开始,初始化树形结构
                $.fn.zTree.init($("#treeDemo"), settings, zNodes);
            }
            if(result=="FAILED"){
                layer.msg(response.message)
            }
        }
    })

}

//鼠标移出节点范围移除按钮组
function myRemoveHoverDom(treeId, treeNode) {

    var btnGroupId = treeNode.tId + "_btnGrp"

    //移除自己本身
    $("#" + btnGroupId).remove()
}

//鼠标移入节点范围添加按钮组
function myAddHoverDom(treeId, treeNode) {

//    按钮组的标签结构：<span><a><i></i></a></span>
//    按钮出现的位置：节点中treeDemo_n_a超链接的后面

//    为了在需要移除按钮能够净赚定位到按钮组所在<span>。需要Span设置有规律的id
    var btnGroupId = treeNode.tId + "_btnGrp"
//    找到节点按钮
    var tID = treeNode.tId + "_a"

    // alert("laiel")

    //按钮的准备
    // <a class="btn btn-info dropdown-toggle btn-xs" style="margin-left:10px;padding-top:0px;" href="#">&nbsp;&nbsp;<i class="fa fa-fw fa-plus rbg "></i></a>
    var deleteBtn = "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs \" style=\"margin-left:10px;padding-top:0px;\" href=\"#\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-times rbg \"></i></a>"
    var editBtn =   "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\" title=\"修改权限信息\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-edit rbg \"></i></a>"
    var addBtn =    "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs addBtn\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-plus rbg \"></i></a>"

    //将组件加入到超连接节点按钮的后面

    //判断目前节点的级别
    var level = treeNode.level

    // alert(level)
    var htmlBtn;
    switch (level) {
        case 0:
            htmlBtn =""+ addBtn
            break;
        case 1:
            if (treeNode.children.length == 0) {
                htmlBtn = addBtn + "" + deleteBtn + "" + editBtn
                break;
            } else {
                htmlBtn = addBtn + "" + editBtn
                break;
            }
        case 2:
            htmlBtn = deleteBtn + "" + editBtn
            break;
    }

    //判断以前是否已经添加了按钮组,id为btnGroupId的组件表示已经加入组件了
    if ($("#" + btnGroupId).length > 0) {
        return;
    }
    // alert(htmlBtn)
    $("#" + tID).after("<span id='" + btnGroupId + "'>" + htmlBtn + "</span>")


}

//加载node 并且修改图标
function myAddDiyDom(treeId, treeNode) {

    //treeId就是treeDemo 也就是整个树形结构附着的id模拟
    console.log("treeId" + treeId)

    //每一个treeNode代表一个节点  ，也就是后端查询得到的Menu对象的全部属性
    console.log(treeNode)

    //根据控制图标的Span标签的id找到这个Span标签

    //例子id="treeDemo_4_a"
    //解析：ul的id_当前节点的序号_功能
    // ul的id_当前节点的序号也就是Tid
    var id = treeNode.tId + "_ico"

    //删除旧的class
    $("#" + id).removeClass()

    //添加新的class

    $("#" + id).prop("class", treeNode.icon)
}


```

##### 后端

```java
package MVC.Controller;


import CrowedFunding_Entity.Menu;
import Service.service.MenuService;
import Util.ResultEntity;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

@Controller
@RequestMapping("/Menu")
public class MenuHandler {

    @Autowired
    private MenuService menuService;

    @RequestMapping("/saveMenu.json")
    @ResponseBody
    public ResultEntity saveMenu(Menu menu){

        menuService.saveMenu(menu);

        return ResultEntity.successWithoutData();
    }

    @ResponseBody
    @RequestMapping("/getWhole.json")
    public ResultEntity<Menu> getWholeNew(){


        List<Menu> all = menuService.getAll();

        Menu root =null;

        Map<Integer, Menu> menuList = new HashMap<>();


        for (Menu menu: all) {

//           将数据存入Map中通过Pid和map中的键id对应 来和value取值
            menuList.put(menu.getId(), menu);

        }

        for(Menu menu:all){

            if(menu.getPid()==null){
                root =menu;

                continue;
            }

            //表示当前的map里面的menu是你的当foreach里面的父节点
            if(menuList.containsKey(menu.getPid())){

                //获取父节点
                Menu menuFather = menuList.get(menu.getPid());

                List<Menu> children = menuFather.getChildren();

                children.add(menu);

                continue;

            }
        }
        return ResultEntity.successWithData(root);
    }


//   //时间复杂度太大，所以不适用
//    public ResultEntity<Menu> getWholeOld(){
//
//        /**1.查询全部的Menu对象*/
//        List<Menu> all = menuService.getAll();
//
//        /**声明一个变量用来存储找到的根节点*/
//
//        Menu root=null;
//
//        for (Menu menu:all){
//
//            //获取menu的pid
//            Integer pid = menu.getPid();
//
//            //判断当前的Pid是否为空,因为只有根节点的PId为空
//            if(pid==null){
//                root=menu;
//
//                //停止本次循环，继续下次循环
//                continue;
//            }
//            //如果不为空，说明当前的节点menu有父节点，要建立父子关系
//            for (Menu menuFather:all) {
//                // 表示当前的id等于Pid，也就是当前的menuFather是menu的父类
//                if(pid.equals(menu.getId())){
//
//                    //将子节点存入父节点的children集合
//                    List<Menu> children = menuFather.getChildren();
//
//                    children.add(menu);
//
//                    break;
//                }
//            }
//
//        }


//     return ResultEntity.successWithData(root);
//    }


}

```



###  更新模态框

#### 目标

修改当前节点的基本属性，不更换父节点

#### 思路

![image-20201126172112275](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201126172112.png)

#### 代码

##### 前端

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
<%--引入Ztree--%>
<link rel="stylesheet" href="static/ztree/zTreeStyle.css">
<script src="static/ztree/jquery.ztree.all-3.5.min.js"></script>
<script src="static/Myjs/my-menu.js"></script>
<script type="text/javascript" >
    $(function (){

    //    1.创建JSON对象用于存储对xTree所做的设置
        var settings = {
            view:{
                "addDiyDom":myAddDiyDom,
                "removeHoverDom":myRemoveHoverDom,
                "addHoverDom":myAddHoverDom
            },
            data:{
                key:{
                    url:"giao"
                }
            }
        }

    //    准备Json数据，数据来源是Ajax请求得到
        generateTree(settings)

        /**更新*/
        $("#treeDemo").on("click",".editBtn",function (){
          //zTree可以直接通过节点属性来获取其他属性，所以知道当前节点的id就可以了
            window.id= this.id

            $("#menuEditModal").modal('show')

            //回显数据

            //通过查看文档可知，$.fn.zTree.getZTreeObj(”html中zTree的最终父标签“)获取zTreeObj对象
            var zTreeObj=$.fn.zTree.getZTreeObj("treeDemo")

            var key="id"

            var value=window.id

            //根据id通过zTree当前节点查询属性
            var currentNode=zTreeObj.getNodeByParam(key,value)

            //回显
            $("#menuEditModal [name=name]").val(currentNode.name)
            $("#menuEditModal [name=url]").val(currentNode.url)
            //radio回显的本质是value属性与回显的节点对应的value( 不一定是value例如我这里就是icon)一样
            //具体做法就是把icon放入数组中

            $("#menuEditModal  [name=icon]").val([currentNode.icon])


            return false;
        })

        $("#menuEditBtn").click(function () {

            //  选定图标
            var MenuFunction=$(".modal-body :checked").val()
            //  选定名称
            var MenuName=$("#menuEditModal [name=name]").val()
            //  选定url
            var MenuUrl=$("#menuEditModal [name=url]").val()


            $.ajax({
                url:"Menu/updateMenu.json",
                data: {
                    //后端的数据更新使用updateSelective
                    "id": window.id,
                    "name":MenuName,
                    "url":MenuUrl,
                    "icon":MenuFunction,
                },
                type:"POST",
                datatype:"JSON",
                success:function (response) {
                    console.log(response)
                    // layer.msg(response.staus)
                    //重新生成树形结构
                    generateTree(settings)

                },
                error:function (response){
                    // layer.msg(response.staus)
                }

            })

            $("#menuEditModal").modal('hide')

        })


        /**保存*/
    //    给节点按钮 添加按钮绑定事件
        $("#treeDemo").on("click",".addBtn",function (){

            // 加入节点后要分配节点,所以要加入对应的父id，也就当前节点的id
               window.menuPid=this.id

            // alert(window.menuPid)
              $("#menuAddModal").modal('show')
            //return false 为了防止他乱跑，测试方便



            return false;
        })



        //保存按钮添加单击事件
        $("#menuSaveBtn").click(function (){

          //  选定图标
          var MenuFunction=$(".modal-body :checked").val()
          //  选定名称
          var MenuName=$("#menuAddModal [name=name]").val()
          //  选定url
          var MenuUrl=$("#menuAddModal [name=url]").val()


            $.ajax({
               url:"Menu/saveMenu.json",
               data: {
                  "name":MenuName,
                  "pid":window.menuPid,
                  "url":MenuUrl,
                  "icon":MenuFunction
               },
                type:"POST",
                datatype:"json",
                success:function (response) {

                   console.log(response)
                    //重新生成树形结构
                    generateTree(settings)
                   // layer.msg(response.staus)
                },
                error:function (response){
                   layer.msg(response.staus)
                }

            })

            $("#menuAddModal").modal('hide')



            //清空表单
            $("#menuResetBtn").click()

        })

    /**   2.准备生成属性结构的JSON数据
        var zNodes =[
            { name:"父节点1 - 展开", open:true,
                children: [
                    { name:"父节点11 - 折叠",
                        children: [
                            { name:"叶子节点111"},
                            { name:"叶子节点112"},
                            { name:"叶子节点113"},
                            { name:"叶子节点114"}
                        ]},
                    { name:"父节点12 - 折叠",
                        children: [
                            { name:"叶子节点121"},
                            { name:"叶子节点122"},
                            { name:"叶子节点123"},
                            { name:"叶子节点124"}
                        ]},
                    { name:"父节点13 - 没有子节点", isParent:true}
                ]},
            { name:"父节点2 - 折叠",
                children: [
                    { name:"父节点21 - 展开", open:true,
                        children: [
                            { name:"叶子节点211"},
                            { name:"叶子节点212"},
                            { name:"叶子节点213"},
                            { name:"叶子节点214"}
                        ]},
                    { name:"父节点22 - 折叠",
                        children: [
                            { name:"叶子节点221"},
                            { name:"叶子节点222"},
                            { name:"叶子节点223"},
                            { name:"叶子节点224"}
                        ]},
                    { name:"父节点23 - 折叠",
                        children: [
                            { name:"叶子节点231"},
                            { name:"叶子节点232"},
                            { name:"叶子节点233"},
                            { name:"叶子节点234"}
                        ]}
                ]},
            { name:"父节点3 - 没有子节点", isParent:true}

        ];
     */

    })
</script>
<body>
<%@include file="include-nav.jsp" %>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">

            <div class="panel panel-default">
                <div class="panel-heading"><i class="glyphicon glyphicon-th-list"></i> 权限菜单列表
                    <div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i
                            class="glyphicon glyphicon-question-sign"></i></div>
                </div>
                <div class="panel-body">
                    <ul id="treeDemo" class="ztree">
                    </ul>
                </div>
            </div>
        </div>
    </div>
</div>
<%@include file="modal-menu-add.jsp"%>
<%@include file="modal-menu-edit.jsp"%>
<%@include file="modal-menu-confirm.jsp"%>
</body>
</html>

```

My-menu.js

```js
//获取树形结构
function generateTree(settings,zNodes){
    $.ajax({
        url:"Menu/getWhole.json",
        datatype:"JSON",
        type: "POST",
        success:function (response){

            var result= response.result

            if(result=="SUCCESS"){
                var zNodes = response.data
                //表示zTree的初始化开始,初始化树形结构
                $.fn.zTree.init($("#treeDemo"), settings, zNodes);
            }
            if(result=="FAILED"){
                layer.msg(response.message)
            }
        }
    })

}

//鼠标移出节点范围移除按钮组
function myRemoveHoverDom(treeId, treeNode) {

    var btnGroupId = treeNode.tId + "_btnGrp"

    //移除自己本身
    $("#" + btnGroupId).remove()
}

//鼠标移入节点范围添加按钮组
function myAddHoverDom(treeId, treeNode) {

//    按钮组的标签结构：<span><a><i></i></a></span>
//    按钮出现的位置：节点中treeDemo_n_a超链接的后面

//    为了在需要移除按钮能够净赚定位到按钮组所在<span>。需要Span设置有规律的id
    var btnGroupId = treeNode.tId + "_btnGrp"
//    找到节点按钮
    var tID = treeNode.tId + "_a"

    // alert("laiel")

    //按钮的准备
    // <a class="btn btn-info dropdown-toggle btn-xs" style="margin-left:10px;padding-top:0px;" href="#">&nbsp;&nbsp;<i class="fa fa-fw fa-plus rbg "></i></a>
    var deleteBtn = "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs deleBtn\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-times rbg \"></i></a>"
    var editBtn =   "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs editBtn\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\" title=\"修改权限信息\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-edit rbg \"></i></a>"
    var addBtn =    "<a id='" + treeNode.id + "' class=\"btn btn-info dropdown-toggle btn-xs addBtn\" style=\"margin-left:10px;padding-top:0px;\" href=\"#\">&nbsp;&nbsp;<i class=\"fa fa-fw fa-plus rbg \"></i></a>"


    //判断目前节点的级别
    var level = treeNode.level

    // alert(level)
    var htmlBtn;
    switch (level) {
        case 0:
            htmlBtn =""+ addBtn
            break;
        case 1:
            if (treeNode.children.length == 0) {
                htmlBtn = addBtn + "" + deleteBtn + "" + editBtn
                break;
            } else {
                htmlBtn = addBtn + "" + editBtn
                break;
            }
        case 2:
            htmlBtn = deleteBtn + "" + editBtn
            break;
    }

    //判断以前是否已经添加了按钮组,id为btnGroupId的组件表示已经加入组件了
    if ($("#" + btnGroupId).length > 0) {
        return;
    }
    //将组件加入到超连接节点按钮的后面
    $("#" + tID).after("<span id='" + btnGroupId + "'>" + htmlBtn + "</span>")


}

//加载node 并且修改图标
function myAddDiyDom(treeId, treeNode) {

    //treeId就是treeDemo 也就是整个树形结构附着的id模拟
    // console.log("treeId" + treeId)
    //
    // //每一个treeNode代表一个节点  ，也就是后端查询得到的Menu对象的全部属性
    // console.log(treeNode)

    //根据控制图标的Span标签的id找到这个Span标签

    //例子id="treeDemo_4_a"
    //解析：ul的id_当前节点的序号_功能
    // ul的id_当前节点的序号也就是Tid
    var id = treeNode.tId + "_ico"

    //删除旧的class
    $("#" + id).removeClass()

    //添加新的class

    $("#" + id).prop("class", treeNode.icon)
}


```



### 删除模态框

* 和更新差不多




---

# 角色和权限 分配

* 权限控制机制的本质就是“用钥匙开锁”

![image-20201201210037597](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201201210045.png)



## 给Admin分配role

###  目标

通过页面操作吧Admin和Role之间的关联关系保存到数据库

###  思路

![image-20201201214312152](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201201214312.png)

###  代码

#### 前往分配页面

##### 建表语句

```sql
CREATE TABLE `project_crowd`.`inner_admin_role` ( `id` INT NOT NULL AUTO_INCREMENT,
`admin_id` INT, `role_id` INT, PRIMARY KEY (`id`) );
```

当前的表不对应的实体类，所以不通过逆向过程，直接在admin或role操作即可

##### 前端页面

![image-20201201215753905](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201201215754.png)



##### 后端

###### Handler

```java
package MVC.Controller;

import CrowedFunding_Entity.Role;
import Service.service.AdminServie;
import Service.service.RoleService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import javax.xml.ws.Action;
import java.util.List;

/**
 * @author lenovo
 */
@Controller
@RequestMapping("/Assign")
public class AssignHandler {


    @Autowired
    private AdminServie adminServie;

    @Autowired
    private RoleService roleService;

    @RequestMapping("/Page.html")
    public String toPage(@RequestParam(value = "id",defaultValue = "") Integer id, @RequestParam(value = "pageNum",defaultValue = "1") Integer pageNum, @RequestParam(value = "keyWord",defaultValue = "") String  keyWord, ModelMap modelMap){

       //查询已分配角色
        List<Role> assignRole = roleService.getAssignRole(id);

        //查询未分配角色
        List<Role> unAssignRole = roleService.getUnAssignRole(id);

        //存入模型
        modelMap.addAttribute("assignRole",assignRole);
        modelMap.addAttribute("unAssignRole",unAssignRole);


        return "assignRole";
    }


}

```



###### Service

```java
package Service.Impl;


import CrowedFunding_Entity.Role;
import CrowedFunding_Entity.RoleExample;
import Mapper.RoleMapper;
import Service.service.RoleService;
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @author lenovo
 */
@Service
public class RoleServiceImpl implements RoleService {


    @Autowired
    private RoleMapper roleMapper;

    //查询已经分配的角色
    @Override
    public List<Role> getAssignRole(Integer id) {
        return roleMapper.selectAssignRole(id);
    }
    //查询没有分配的角色
    @Override
    public List<Role> getUnAssignRole(Integer id) {
        return roleMapper.selectUnAssignRole(id);
    }


    @Override
    public PageInfo getPage(String keyword, int pageNum, int pageSize) {
        //1.开启分页功能
        PageHelper.startPage(pageNum, pageSize);
        //2.获取数据
        List<Role> roles = roleMapper.selectRoleByKeyword(keyword);
        //3.创建分页类
        PageInfo pageInfo=new PageInfo(roles);

        return pageInfo;
    }

    @Override
    public void saveRole(Role role) {
        roleMapper.insert(role);
    }

    @Override
    public void updateRole(Role role) {

        roleMapper.updateByPrimaryKey(role);

    }

    @Override
    public void deleteRole(List<Integer> list) {
        RoleExample roleExample = new RoleExample();
        RoleExample.Criteria criteria = roleExample.createCriteria();
        criteria.andIdIn(list);
        roleMapper.deleteByExample(roleExample);
    }

}

```



###### Mapper

```xml
    <select id="selectAssignRole" parameterType="integer" resultType="CrowedFunding_Entity.Role">
          select t_role.id,t_role.name 
          from t_role join inner_admin_role  
          on t_role.id=inner_admin_role.role_id  
          where  inner_admin_role.admin_id=#{id} </select>
    <select id="selectUnAssignRole" parameterType="integer" resultType="CrowedFunding_Entity.Role">
       select t_role.id,t_role.name from t_role join inner_admin_role  
      on t_role.id!=inner_admin_role.role_id 

      where  inner_admin_role.admin_id=1
  </select>

```



#### 在分配页面显示角色数据

##### 前端

######  调整表单

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/9
  Time: 18:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
<body>
<%@include file="include-nav.jsp" %>
<script>
    $(function () {


        //加载导航条
        initPagination();
    })
    //生成页码导航条的函数
    function initPagination() {
        //总记录数
        var totalRecord=${requestScope.PageInfo.total}
        //声明一个JSON对象存储Pagination要设置的属性
        var properties={
            num_edge_entries: 3,                            //边缘页数
            num_display_entries: 5,                         //主体页数
            items_per_page:${requestScope.PageInfo.pageSize},// 每页显示1项
            callback: pageselectCallback,                   //回调函数
            current_page:${requestScope.PageInfo.pageNum-1},//因为Pagination这个组件索引是从0开始的，所以我们要减一
            prev_text: "上一页",                             //上一页文本显示的文本
            next_text: "下一页",
        };



        $("#Pagination").pagination(totalRecord,properties);
    }
    //回调函数的含义：声明以后不是自己调用，而是交个系统或者框架调用
    //用户点击“上一页，下一页，1,2,3”这样的页码是调用这个函数实现页面跳转
    //PageIndex时候Pageination传给我们那个“从零开始”的页码

    function pageselectCallback(pageIndex,jquery) {

        //根据pageIndex计算得到的PageNum
        var pageNum=pageIndex+1;

        //跳转页面
        window.location.href="Admin/getPageInfo.html?pageNum="+pageNum+"&keyWord=${param.keyWord}";

        //    由于每个页码按钮都是超链接，所以在这个函数最后取消超链接的默认行为
        return false
    }
</script>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
            <div class="panel panel-default">
                <div class="panel-heading">
                    <h3 class="panel-title"><i class="glyphicon glyphicon-th"></i> 数据列表</h3>
                </div>
                <div class="panel-body">
                    <form class="form-inline" action="Admin/getPageInfo.html" method="post" role="form" style="float:left;">
                        <div class="form-group has-feedback">
                            <div class="input-group">
                                <div class="input-group-addon">查询条件</div>
                                <input class="form-control has-success" name="keyWord" type="text" placeholder="请输入查询条件">
                            </div>
                        </div>
                        <button type="submit" class="btn btn-warning"><i class="glyphicon glyphicon-search"></i> 查询</button>
                    </form>
                    <button type="button"  class="btn btn-danger" style="float:right;margin-left:10px;"><i class=" glyphicon glyphicon-remove"></i> 删除</button>
<%--                旧代码
                    button type="button" class="btn btn-primary" style="float:right;" onclick="window.location.href='add.html'"><i class="glyphicon glyphicon-plus"></i> 新增</button>
                    新代码
--%>
                    <a href="Register.html" class="btn btn-primary" style="float:right;"><i class="glyphicon glyphicon-plus"></i> 新增</a>
                    <br>
                    <hr style="clear:both;">
                    <div class="table-responsive">
                        <table class="table  table-bordered">
                            <thead>
                            <tr >
                                <th width="30">#</th>
                                <th width="30"><input type="checkbox"></th>
                                <th>账号</th>
                                <th>名称</th>
                                <th>邮箱地址</th>
                                <th width="100">操作</th>
                            </tr>
                            </thead>
                            <tbody>

                            <c:if test="${empty requestScope.PageInfo.list}">
                                <tr>
                                    <th>
                                    <td colspan="6" align="center">抱歉！没有查到您要的信息</td>
                                    </th>
                                </tr>
                            </c:if>

                            <c:if test="${!empty requestScope.PageInfo.list}">
                                <c:forEach var="item" items="${requestScope.PageInfo.list}">
                            <tr>
                                <td>${item.id}</td>
                                <td><input type="checkbox"></td>
                                <td>${item.loginAcct}</td>
                                <td>${item.userName}</td>
                                <td>${item.email}</td>
                                <td>
                                    <a type="button" href="Assign/Page.html?id=${item.id}&pageNum=${PageInfo.pageNum}&keyWord=${param.keyWord}" class="btn btn-success btn-xs"><i class=" glyphicon glyphicon-check"></i></a>
                                    <a type="button" class="btn btn-primary btn-xs" href="Admin/editAdminById.html?id=${item.id}&pageNum=${PageInfo.pageNum}&keyWord=${param.keyWord}"><i class=" glyphicon glyphicon-pencil"></i></a>
<%--                                    <button  type="button" class="btn btn-danger btn-xs" id="deleteButton" ><i   class=" glyphicon glyphicon-remove"></i></button>--%>
<%--                                    --%>
                                    <a href="Admin/deleteUser/${item.id}/${requestScope.PageInfo.pageNum}/${param.keyWord}.html" id="delete"><i   class=" glyphicon glyphicon-remove"></i></a>
                                </td>
                            </tr>
                                </c:forEach>
                            </c:if>
                            </tbody>
<%--                            原始代码--%>
<%--                            <tfoot>--%>
<%--                            <tr >--%>
<%--                                <td colspan="6" align="center">--%>
<%--                                    <ul class="pagination">--%>
<%--                                        <li class="disabled"><a href="#">上一页</a></li>--%>
<%--                                        <li class="active"><a href="#">1 <span class="sr-only">(current)</span></a></li>--%>
<%--                                        <li><a href="#">2</a></li>--%>
<%--                                        <li><a href="#">3</a></li>--%>
<%--                                        <li><a href="#">4</a></li>--%>
<%--                                        <li><a href="#">5</a></li>--%>
<%--                                        <li><a href="#">下一页</a></li>--%>
<%--                                    </ul>--%>
<%--                                </td>--%>
<%--                            </tr>--%>

<%--                            </tfoot>--%>
<%--                            新代码--%>
                            <tfoot>
                            <tr >
                                <td colspan="6" align="center">
                                    <div id="Pagination" class="pagination"><!-- 这里显示分页 --></div>

                                </td>
                            </tr>

                            </tfoot>

                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
    </div>
    </div>
</div>

</body>
</html>

```



###### 	Jquery代码

![image-20201202185737962](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201202185739.png)



```js
<script type="text/javascript">
    $(function (){
         $("#toRightButton").click(function () {
             // select:eq(0)>表示第一个select标签，>option:selected表示子元素(标签)选中的
             $("select:eq(0)>option:selected").appendTo("select:eq(1)")

         })

        $("#toLeftButton").click(function () {
            // select:eq(0)>表示第一个select标签，>option:selected表示子元素(标签)选中的
            $("select:eq(1)>option:selected").appendTo("select:eq(0)")

        })

    })
</script>

```



##### 后端

###### Handler

```java
  @RequestMapping("/assign.html")
    public String assign(@RequestParam("adminId") Integer adminId,
                         @RequestParam("pageNum")Integer pageNum,
                         @RequestParam("keyWord")String keyWord,
                         //required = false 表示当前的对象可以不为空，我们允许用户在页面取消所有的已分配角色在提交表单，所以可以不提供roleIdList请求参数
                         //
                         @RequestParam(value = "roleIdList",required = false)List<Integer> roleIdList){

        System.out.println(roleIdList);
        adminServie.saveRoleWithAdmin(adminId,roleIdList);


        return "redirect:/Admin/getPageInfo.html?pageNum="+pageNum+"&keyWord="+keyWord;
    }
```



###### Service

```java
@Override
    public void saveRoleWithAdmin(Integer id,List<Integer> roleIds) {

        //旧的数据如下：
        /** adminID  roleId
         *     1       1(要删除)
         *     1       2(要删除)
         *     1       3
         *
         * 新的数据
         * adminID  roleId
         *    1       1(本来就有)
         *    1       2(本来就有)
         *    1       3(本来就有)
         *    1       4(新的)
         *    为了简化数据，现根据adminID删除旧的数据，再根据roleIds保存新的数据
         * */
        adminMapper.deleteByRoleWithAdminRelationShip(id);
        if(roleIds!=null&&roleIds.size() > 0){
        adminMapper.insertByRoleWithAdminRelationShip(id,roleIds);
        }
    }
```

  

###### Mapper

```xml
<insert id="insertByRoleWithAdminRelationShip" >
    <foreach collection="roleList" item="role" separator=";">
    insert into  inner_admin_role values(null,#{adminId},#{role})
    </foreach>
  </insert>
  <delete id="deleteByRoleWithAdminRelationShip">
      delete from inner_admin_role where admin_id =#{id}
  </delete>
```



######  小bug：复选框里面的option只可以选中页面黑灰色的选择的option

![image-20201202213915106](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201202213915.png)



jquery

```js
 //表单提交按钮设定单击函数,使选择结果全部选中
        $("#submitButton").click(function (){
            //表示当前的select:eq(1)的所有option全都选中
            $("select:eq(1)>option").prop("selected","selected")
            //测试结果，暂时不让表单提交返回false
            // return false;
        })
```



## 给Role分配Auth

###  目标

###  思路

![image-20201203143809236](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201203143810.png)

### 代码

#### t_auth建表和数据填充

```sql
-- 建表
CREATE TABLE `t_auth` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`name` varchar(200) DEFAULT NULL,
`title` varchar(200) DEFAULT NULL,
`category_id` int(11) DEFAULT NULL,
 PRIMARY KEY (`id`)
);

-- 数据填充
INSERT INTO t_auth(id,`name`,title,category_id) VALUES(1,'','用户模块',NULL);
INSERT INTO t_auth(id,`name`,title,category_id) VALUES(2,'user:delete','删除',1);
INSERT INTO t_auth(id,`name`,title,category_id) VALUES(3,'user:get','查询',1);
INSERT INTO t_auth(id,`name`,title,category_id) VALUES(4,'','角色模块',NULL);
INSERT INTO t_auth(id,`name`,title,category_id) VALUES(5,'role:delete','删除',4);
INSERT INTO t_auth(id,`name`,title,category_id) VALUES(6,'role:get','查询',4);
INSERT INTO t_auth(id,`name`,title,category_id) VALUES(7,'role:add','新增',4);
```



#####  表中列属性的意义

* name：给资源分配权限或给角色分配权限是使用的具体值，将来做权限验证也是使用name字段的值来进行比对,建议使用英文
* time：在页面上显示，让用户便于查看的值，建议使用中文
* category_id：关联当前权限所属的分类。这个关联不是到其他表关联，而是就在当前表内部进行关联

![image-20201203152814595](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201203152814.png)

name字段的格式：   

​            模块:操作名

​       例如：user;delete

​             这里的`:`不会有任何意义，不论是我们自己写的代码还是将来使用的框架都不会解析`:`如果不用：也可以用%、@、#、随意什么的





----



####  打开模态框

##### 绑定单机事件

###### 寻找按钮并添加标记

因为在之前的角色维护里，我们生成role的方式通过js动态生成，所以我们需要通过jQuery的on函数来进行绑定

![image-20201203162220031](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201203162220.png)

![image-20201203162319333](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20201203162319333.png)

###### 准备模态框



![image-20201203165814773](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201203165815.png)



```jsp
<%@ page language="java" contentType="text/html;charset=UTF-8" pageEncoding="UTF-8"%>
<div id="assignModal" class="modal fade" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal"
                        aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>
                <h4 class="modal-title">尚筹网系统弹窗</h4>
            </div>
            <div class="panel panel-default">
                <div class="panel-heading"><i class="glyphicon glyphicon-th-list"></i> 权限分配列表<div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i class="glyphicon glyphicon-question-sign"></i></div></div>
                <div class="panel-body">
                    <button class="btn btn-success">分配许可</button>
                    <br><br>
                    <ul id="treeDemo" class="ztree"></ul>
                </div>
            </div>

        </div>
    </div>
</div>

```



######  绑定单级响应函数

![image-20201203162511706](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201203162511.png)



![image-20201203171446954](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201203171447.png)

######  小bug：js代码自动保存，导致修改js，前端页面收不到

解决办法

![image-20201203165741796](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201203165742.png)



###### 树形结构的显示

![image-20201204210934089](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201204211141.png)

```js
//声明专门的函数来分配Auth的模态框显示Auth的属性结构数据
function fillAuthTree() {

    //1.发送ajax
    var ajaxAuth = $.ajax({
        url: "Assign/getAuth.json",
        dataType: "JSON",
        type: "POST",
        async: false
    })

    if (ajaxAuth.status != 200) {
        layer.msg("请求出错，响应状态码是:" + ajaxAuth.status + "说明是" + ajaxAuth.statusText)
        return false;
    }
    //获取Auth数组
    var  authList= ajaxAuth.responseJSON.data;

    //zTree配置文件
    var settings = {
        data: {
            //通过设定参数simpleData的子属性enable来使得有Pid和id关系的list交给zTree
            simpleData: {
                enable:true
            }

        },
        //通过设定参数check的子属性enable来使得zTree展开单选框/多选框
        check: {
            enable: true
        },

        
    }

    // 生成树形结构
    $.fn.zTree.init($("#authTreeDemo"), settings, authList);

    // 查询已分配的Auth的id组成的数组

    // 根据authidArray把树形结构对应的节点选上
}
```



######  生成树的显示调整

**小问题**：

* 1. 告诉zTree使用title字段显示节点名称
* 2. 使用categoryID属性代替Pid属性关联父节点

![image-20201204212244237](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201204212244.png)

解决问题1：

![image-20201204212425621](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201204212425.png)

![image-20201204212927522](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20201204212927522.png)

效果：

![image-20201204213000713](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20201204213000713.png)

解决问题2：

![image-20201204213141177](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201204213141.png)

![image-20201204213540906](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201204213541.png)

运行结果

![image-20201204213438045](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201204213438.png)



######  树的展开

![image-20201204213757730](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201204213816.png)



---



###### 树的回显

**创建角色和权限之间关联关系的中间表**

```sql
CREATE TABLE `project_crowd`.`inner_role_auth` ( `id` INT NOT NULL AUTO_INCREMENT,
`role_id` INT, `auth_id` INT, PRIMARY KEY (`id`) );
```

根据role_id查询auth_id

前端

![image-20201206154008909](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201206154009.png)

```js
//声明专门的函数来分配Auth的模态框显示Auth的属性结构数据
function fillAuthTree() {

    //1.发送ajax
    var ajaxAuth = $.ajax({
        url: "Assign/getAuth.json",
        dataType: "JSON",
        type: "POST",
        async: false
    })

    if (ajaxAuth.status != 200) {
        layer.msg("请求出错，响应状态码是:" + ajaxAuth.status + "说明是" + ajaxAuth.statusText)
        return false;
    }
    //获取Auth数组
    var  authList= ajaxAuth.responseJSON.data;

    //zTree配置文件
    var settings = {
        data: {
            //通过设定参数simpleData的子属性enable来使得有Pid和id关系的list交给zTree
            simpleData: {
                enable:true,
                pIdKey:"categoryId"
            },
            //通过指定key子属性name指定zTree使用子节点那个属性作为name
            key:{
                name:"title"
            }
        },
        //通过设定参数check的子属性enable来使得zTree展开单选框/多选框
        check: {
            enable: true
        },


    }

    // 生成树形结构
    $.fn.zTree.init($("#authTreeDemo"), settings, authList);

    //获取zTreeNode对象
    var zTreeObj=$.fn.zTree.getZTreeObj("authTreeDemo")
    //通过zTreeObj来使树形结构展开
    zTreeObj.expandAll(true)
    // 查询已分配的Auth的id组成的数组

      //因为roleId在前面获取role的时候已经加id赋给全局变量window.roleID了所以这里直接取
     var authIds=$.ajax({
        url:"Assign/getAuthsId.json",
        contentType:"application/json;charset=UTF-8",
        data:window.roleID,
        dataType:"json",
        type:"POST",
        async : false
    })

    // console.log(authIds)
    if(authIds.status!=200){
        layer.msg("错误状态码:"+authIds.status+"\n"+"错误信息"+authIds.statusText)
    }
    //获取成功
    var authIdList=authIds.responseJSON.data
    console.log(authIdList)
    // 根据authidArray把树形结构对应的节点选上
        //表示
        var checked=true
    for (var i=0;i<authIdList.length; i++){

        //节点id
        var authId=authIdList[i]
        //通过zTreeObj的getNodeByParam(key（key的意思就是对应的键）,value),方法获取对应authId的对象
        var authNode=zTreeObj.getNodeByParam("id",authId)
        //表示选中的意思
        var checked=true
        //表示是否子父节点选中联动，我们不使用联动，不联动是为了避免把不该勾选的勾选上
        var checkTypeFlag=false
    zTreeObj.checkNode(authNode,checked,checkTypeFlag)
    }

}
```



zTree关于回显的文档

![image-20201206152403606](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201206152406.png)

后端

Handler

```java

    @ResponseBody
    @RequestMapping("/getAuthsId.json")
    public ResultEntity<List<Integer>>  getAuthsId(@RequestBody Integer roleIds){
        List<Integer> authsId = authService.getAuthsId(roleIds);
        return  ResultEntity.successWithData(authsId);

    }
```



Service

```java
package Service.Impl;

import CrowedFunding_Entity.Auth;
import CrowedFunding_Entity.AuthExample;
import Mapper.AuthMapper;
import Service.service.AuthService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class AuthServiceImpl implements AuthService {

    @Autowired
    private AuthMapper authMapper;

    @Override
    public List<Integer> getAuthsId(Integer id) {

        List<Integer> integers = authMapper.selectAuthsId(id);
        return integers;
    }
    @Override
    public List<Auth> getAuth() {
        List<Auth> auths = authMapper.selectByExample(new AuthExample());
        return auths;
    }

}

```

Mapper

```xml
<select id="selectAuthsId" parameterType="java.lang.Integer" resultType="java.lang.Integer">
    select auth_id from inner_role_auth where role_id=#{id}
  </select>
```



###### 注意:传输过程小贴士

当我们传输json数组的时候，优先选择使用JSON.StringFly的转换之后再传输

  前端数据：

![image-20201207150556996](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201207150559.png)

后端接收数据：

​	![image-20201207150648888](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201207150649.png)

## 給Menu分配Auth



###  思路



### 代码



