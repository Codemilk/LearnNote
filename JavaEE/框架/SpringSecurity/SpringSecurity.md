#  SpringSecurity框架介绍

## 1.概要

**Spring 是非常流行和成功的 Java 应用开发框架，Spring Security正是Spring家族中的成员。Spring Security 基于 Spring 框架，提供了一套 Web 应用安全性的完整解决方案。**

**正如你可能知道的关于安全方面的两个主要区域是“认证”和“授权”（或者访问控制），一般来说，Web 应用的安全性包括用户认证（Authentication）和用户授权（Authorization）两个部分，这两点也是Spring Security重要核心功能。**

**（1）==用户认证==指的是：验证某个用户是否为系统中的合法主体，也就是说用户能否访问该系统。用户认证一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认证过程。通俗点说就是系统认为用户是否能登录**

**（2）==用户授权==指的是验证某个用户是否有权限执行某个操作。在一个系统中，不同用户所具有的权限是不同的。比如对一个文件来说，有的用户只能进行读取，而有的用户可以进行修改。一般来说，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的权限。通俗点讲就是系统判断用户是否有权限去做某些事情。**

## 2.历史

**“Spring Security开始于2003年年底,““spring的acegi安全系统”。 起因是Spring开发者邮件列表中的一个问题,有人提问是否考虑提供一个基于spring的安全实现。**
**Spring Security 以“The Acegi Secutity System for Spring” 的名字始于2013年晚些时候。一个问题提交到Spring 开发者的邮件列表，询问是否已经有考虑一个机遇Spring 的安全性社区实现。那时候Spring 的社区相对较小（相对现在）。实际上Spring自己在2013年只是一个存在于ScourseForge的项目，这个问题的回答是一个值得研究的领域，虽然目前时间的缺乏组织了我们对它的探索。**
**考虑到这一点，一个简单的安全实现建成但是并没有发布。几周后，Spring社区的其他成员询问了安全性，这次这个代码被发送给他们。其他几个请求也跟随而来。到2014年一月大约有20万人使用了这个代码。这些创业者的人提出一个SourceForge项目加入是为了，这是在2004三月正式成立。**
**在早些时候，这个项目没有任何自己的验证模块，身份验证过程依赖于容器管理的安全性和Acegi安全性。而不是专注于授权。开始的时候这很适合，但是越来越多的用户请求额外的容器支持。容器特定的认证领域接口的基本限制变得清晰。还有一个相关的问题增加新的容器的路径，这是最终用户的困惑和错误配置的常见问题。**
**Acegi安全特定的认证服务介绍。大约一年后，Acegi安全正式成为了Spring框架的子项目。1.0.0最终版本是出版于2006 -在超过两年半的大量生产的软件项目和数以百计的改进和积极利用社区的贡献。**
**Acegi安全2007年底正式成为了Spring组合项目，更名为"Spring Security"。**





## 3.同类型产品对比

**Spring Security**
Spring技术栈的组成部分。
通过提供完整可扩展的认证和授权支持保护你的应用程序。
https://spring.io/projects/spring-security
SpringSecurity特点：
			⚫ 和Spring无缝整合。
			⚫ 全面的权限控制。
			⚫ 专门为Web开发而设计。
◼ 旧版本不能脱离Web环境使用。
◼ 新版本对整个框架进行了分层抽取，分成了核心模块和Web模块。单独引入核心模块就可以脱离Web环境。
			⚫ 重量级。
1.3.2 Shiro
Apache旗下的轻量级权限控制框架。
特点：
			⚫ 轻量级。Shiro主张的理念是把复杂的事情变简单。针对对性能有更高要求的互联网应用有更好表现。
			⚫ 通用性。
			◼ 好处：不局限于Web环境，可以脱离Web环境使用。
			◼ 缺陷：在Web环境下一些特定的需求需要手动编写代码定制。 

​		Spring Security 是 Spring 家族中的一个安全管理框架，实际上，在 Spring Boot 出现之前，Spring Security 就已经发展了多年了，但是使用的并不多，安全管理这个领域，一直是 Shiro 的天下。 

​		相对于 Shiro，在 SSM 中整合 Spring Security 都是比较麻烦的操作，所以，Spring Security 虽然功能比 Shiro 强大，但是使用反而没有 Shiro 多（Shiro 虽然功能没有 Spring Security 多，但是对于大部分项目而言，Shiro 也够用了）。

​		 自从有了 Spring Boot 之后，Spring Boot 对于 Spring Security 提供了自动化配置方案，可以使用更少的配置来使用 Spring Security。 因此，一般来说，常见的安全管理技术栈的组合是这样的：

 • SSM + Shiro 

• Spring Boot/Spring Cloud + Spring Security 

以上只是一个推荐的组合而已，如果单纯从技术上来说，无论怎么组合，都是可以运行的。



## 4.模块划分

![image-20210113201800078](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210113203301.png)

# Hello World

## 创建项目并引入对应依赖

![image-20210113202321715](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210113203301.png)

##  进行Controller测试

 ![image-20210115213237741](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210115213245.png)

## 运行结果

当你访问localhost:8081,会出现新的页面

![image-20210115213641512](C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210115213641512.png)

这代表SpringSecurity起作用了，你的默认用户和密码是：

* 用户名：user
* 密码：每次运行会打印出![image-20210115213750753](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210115213750.png)

# SpringSecurity基本原理概要

SpringSecurity 本质是一个过滤器链：
从启动是可以获取到过滤器链：

org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter
org.springframework.security.web.context.SecurityContextPersistenceFilter
org.springframework.security.web.header.HeaderWriterFilter
org.springframework.security.web.csrf.CsrfFilter
org.springframework.security.web.authentication.logout.LogoutFilter
org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter
org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter
org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter
org.springframework.security.web.savedrequest.RequestCacheAwareFilter
org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter
org.springframework.security.web.authentication.AnonymousAuthenticationFilter
org.springframework.security.web.session.SessionManagementFilter
org.springframework.security.web.access.ExceptionTranslationFilter
org.springframework.security.web.access.intercept.FilterSecurityInterceptor

## 三个比较重要的过滤器

代码底层流程：重点看三个过滤器

**FilterSecurityInterceptor：是一个方法级的权限过滤器，基本位于过滤链的最底部‘**

![image-20210117195726128](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210117195727.png)

ExceptionTranslationFilter：是一个异常过滤器，用来处理在认证授权过程中抛出的异常

![image-20210117200712303](C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210117200712303.png)

**UsernamePasswordAuthenticationFilter：对/login的POST请求做拦截，校验表单中用户名，密码**

![image-20210117205109657](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210117205110.png)



## 过滤器加载原理

SpringBoot会帮我们去配置好SpringSecurity，但是如果SpringSecurity自己单独使用我们需要配置过滤器**DelegatingFilterProxy**

**DelegatingFilterProxy** 

![image-20210117205515821](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210117205516.png)

![image-20210117205715075](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210117205715.png)

![](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210117210111.png)

![image-20210117210407884](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210117210408.png)

## 两个重要的接口

****

**UserDetailService：查询数据库用户名和密码的接口**

创建类继承UsernamePasswordAuthenicationFilter，重写三个方法attemptAuthentication（验证）、和他父类的successfulAuthentication（验证成功）和unsuccessfulAuthentication（验证失败）方法

创建类继承UserDetailService，来编写查询数据过程，返回User对象，这个User对象是安全框架提供的对象

 **PasswordEncoder：密码加密**



# Web权限方案

## 用户验证

### 通过配置文件

配置用户信息

![image-20210118212323588](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210118212333.png)

运行结果

![image-20210118212832133](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210118212832.png)

### 通过配置类

 通过继承WebSecurityConfigurerAdapter，重写`configure` 方法

```java
package springsecurity.Config;


import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //使用密码加密类，对密码进行加密
        BCryptPasswordEncoder brBCryptPasswordEncoder =new BCryptPasswordEncoder();
        String encode = brBCryptPasswordEncoder.encode("666");
        //设置用户名和密码以及角色
        auth.inMemoryAuthentication().withUser("tom").password(encode).roles("admin");
    }

    //我们使用密码加密类的时候，需要手动去给他创建一个具体的实现对象
    @Bean
    PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}

```

![image-20210118231351447](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210118231352.png)



### 自定义编写实现类

* 第一步：创建配置类，设置使用哪个userDetailsService实现类
* 第二步：编写实现类，返回User对象，User对象有用户名密码和操作权限

第一步：

```java
package springsecurity.Config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class DiyUserDetialsServiceConfig extends WebSecurityConfigurerAdapter {

    //从IOC容器中取出   UserDetailsService的实现类
    @Autowired
    private UserDetailsService userDetailsService;
    
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
           //设置使用哪个userDetailsService实现类
            auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```



第二步：

```java
package springsecurity.Service;

import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.AuthorityUtils;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.stereotype.Service;

import java.util.List;


@Service("userDetailsService")
public class MyUserService implements UserDetailsService {


    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        List<GrantedAuthority> authority = AuthorityUtils.commaSeparatedStringToAuthorityList("root");

        User user = new User("tom", new BCryptPasswordEncoder().encode("666"), authority);
        return user;
    }
}
```





## 查询数据库进行验证

1. 引入相关依赖

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.4.1</version>
           <relativePath/> <!-- lookup parent from repository -->
       </parent>
       <groupId>SpringSecurity</groupId>
       <artifactId>1-helloworld</artifactId>
       <version>0.0.1-SNAPSHOT</version>
       <name>1-helloworld</name>
       <description>Demo project for Spring Boot</description>
   
       <properties>
           <java.version>1.8</java.version>
       </properties>
   
       <dependencies>
   
           <!--mybatis-plus-->
           <dependency>
               <groupId>com.baomidou</groupId>
               <artifactId>mybatis-plus-boot-starter</artifactId>
               <version>3.0.5</version>
           </dependency> <!--mysql-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
           </dependency> <!--lombok用来简化实体类-->
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-security</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
   
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
           </dependency>
           <dependency>
               <groupId>org.springframework.security</groupId>
               <artifactId>spring-security-test</artifactId>
               <scope>test</scope>
           </dependency>
       </dependencies>
   
       <build>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
               </plugin>
           </plugins>
       </build>
   
   </project>
   ```

2. 创建数据库和数据库表

   ```sql
   create table users(
   id bigint primary key auto_increment,
   username varchar(20) unique not null,
   password varchar(100)
   );
   -- 密码atguigu
   insert into users values(1,'张
   san','$2a$10$2R/M6iU3mCZt3ByG7kwYTeeW0w7/UqdeXrb27zkBIizBvAven0/na');
   -- 密码atguigu
   insert into users values(2,'李
   si','$2a$10$2R/M6iU3mCZt3ByG7kwYTeeW0w7/UqdeXrb27zkBIizBvAven0/na');
   create table role(
   id bigint primary key auto_increment,
   name varchar(20)
   );
   insert into role values(1,'管理员');
   insert into role values(2,'普通用户');
   create table role_user(
   uid bigint,
   rid bigint
   );
   insert into role_user values(1,1);
   insert into role_user values(2,2);
   create table menu(
   id bigint primary key auto_increment,
   name varchar(20),
   url varchar(100),
   parentid bigint,
   permission varchar(20)
   );
   insert into menu values(1,'系统管理','',0,'menu:system');
   insert into menu values(2,'用户管理','',0,'menu:user');
   create table role_menu(
   mid bigint,
   rid bigint
   );
   insert into role_menu values(1,1);
   insert into role_menu values(2,1);
   insert into role_menu values(2,2);
   ```

   

3. 创建User表对应的实体类（使用Lombok）

   ```java
   package springsecurity.Entity;
   
   import lombok.Data;
   
   //Lombok的注解@Data可以通过getter和setter方法
   @Data
   public class User {
       private int id;
       private String password;
   }
   ```

4. 整合MapperPlus，创建接口，继承mp的接口

   ```java
   package springsecurity.Mapper;
   
   import com.baomidou.mybatisplus.core.mapper.BaseMapper;
   import springsecurity.Entity.User;
   
   public interface userMapper extends BaseMapper<User> {
       
   }
   ```

5. 在继承UserDetailsService类的loadUsername方法中使用MapperPlus来查询数据库

   ```java
   package springsecurity.Service;
   
   import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.security.core.GrantedAuthority;
   import org.springframework.security.core.authority.AuthorityUtils;
   import org.springframework.security.core.userdetails.User;
   import org.springframework.security.core.userdetails.UserDetails;
   import org.springframework.security.core.userdetails.UserDetailsService;
   import org.springframework.security.core.userdetails.UsernameNotFoundException;
   import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
   import org.springframework.stereotype.Service;
   import springsecurity.Entity.Users;
   import springsecurity.Mapper.userMapper;
   
   import java.util.List;
   
   
   @Service("userDetailsService")
   public class MyUserService implements UserDetailsService {
   
       @Autowired
       private userMapper userMapper;
   
   //    @Override
       //方法参数username就是从表单过滤器拦截下的用户名
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
           /**这里就是判断从表单获取的数据和数据库比对（数据库查询）*/
            //创建条件构造器
           QueryWrapper queryWrapper=new QueryWrapper();
            //这里就相当于where username(表的列名)=？
           queryWrapper.eq("username",username);
           Users users = userMapper.selectOne(queryWrapper);
           if(users==null){
               throw new UsernameNotFoundException("meiyou");
           }
           //权能
           List<GrantedAuthority> authority = AuthorityUtils.commaSeparatedStringToAuthorityList("root");
           //这里的密码不用加密，数据库已经加密好了
           User user = new User(users.getUsername(),users.getPassword(), authority);
           return user;
       }
   
   
   }
   
   ```

6. 将对应的Mapper扫描到，在启动类配置@MapperScan将你的Mapper文件注册进去

   ```java
   package springsecurity;
   
   import org.mybatis.spring.annotation.MapperScan;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   
   @SpringBootApplication
   //将Mapper注册到对应的容器
   @MapperScan("springsecurity.Mapper")
   public class Application {
   
       public static void main(String[] args) {
           SpringApplication.run(Application.class, args);
       }
   
   }
   ```

7. 通过Druid连接数据库，进行数据操作

   * 配置文件

     ```yaml
     spring:
       datasource:
         username: root
         password: 123456
         driver-class-name: com.mysql.cj.jdbc.Driver
         url: jdbc:mysql://localhost:3306/logincase?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&useSSL=false&verifyServerCertificate=false&autoReconnct=true&autoReconnectForPools=true&allowMultiQueries=true
         type: com.alibaba.druid.pool.DruidDataSource
         #   数据源其他配置
         initialSize: 5
         minIdle: 5
         maxActive: 20
         maxWait: 60000
         timeBetweenEvictionRunsMillis: 60000
         minEvictableIdleTimeMillis: 300000
         validationQuery: SELECT 1 FROM DUAL
         testWhileIdle: true
         testOnBorrow: false
         testOnReturn: false
         poolPreparedStatements: true
         
     ```

   * 通过配合类加入到容器并且和配置文件对应

     ```java
     package springsecurity.Config;
     
     import com.alibaba.druid.pool.DruidDataSource;
     import org.springframework.boot.context.properties.ConfigurationProperties;
     import org.springframework.context.annotation.Bean;
     import org.springframework.context.annotation.Configuration;
     
     @Configuration
     public class DataSourceConfig {
     
         @Bean
         @ConfigurationProperties("spring.datasource")
         public DruidDataSource druidDataSource(){
             return new DruidDataSource();
         }
     
     }
     ```





## 自定义用户登入界面

复写继承自`WebSecurityConfigurerAdapter`的方法configure(),但是参数改版了变成了HttpSercurity

```java
package springsecurity.Config;

import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class DiyUserDetialsServiceConfig extends WebSecurityConfigurerAdapter {

    //从IOC容器中取出   UserDetailsService的实现类
    @Autowired
    private UserDetailsService userDetailsService;
    
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {

           //设置使用哪个userDetailsService实现类
            auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
         http.formLogin()  //自定义自己编写的登入页面
             .loginPage("/login.html")  //登入页面设置
             .loginProcessingUrl("/userlogin") //登入访问路径，说白了封装路径，在表单的action写入这个路径,实际会跳转success
             .defaultSuccessUrl("/success")  //登入成功之后，跳转路径 ,真正意义上的跳转到Controller
             .permitAll()   //表示同意
                 .and()
             .authorizeRequests().antMatchers("/","/hello","/userlogin").permitAll()//表示那些路径不会被拦截 ，可以不用认证直接访问
                 .and()
             .csrf().disable();//关闭csrf防护
         ;

    }

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

![image-20210120191422708](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210120191433.png)

注意：表单的用户名和密码都必须是username和password

![image-20210120191614036](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210120191614.png)



##  基于权限访问控制

## hasAuthority()方法

如果当前的主体具有指定的权限，则返回true，否则返回false

在配置类设置当前访问地址有哪些权限

![image-20210121195247432](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210121195249.png)

如何去给他赋予权力？我们前面已经见过了，在继承了UserDetailSsearch的类的方法中，有一个权力的list

![image-20210121195720253](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210121195721.png)

![image-20210121200129053](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210121200129.png)

## 基于角色访问控制

