---








typora-root-url: ..\..\..\Pic
typora-copy-images-to: upload
---

# 入门

## 简介

1. 简化Spring应用开发的一个框架

2. 整个Spring技术栈的一个大整合

3. J2EE开发的一站式解决方案

![image-20201208161114792](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208161122.png)

### 约定大约配置

* 约定优于配置（convention over configuration），也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性
* 在SpringBoot中，约定大于配置可以从以下两个方面来理解：
  1. 开发人员仅需规定应用中不符合约定的部分
  2. 在没有规定配置的地方，采用默认配置，以力求最简配置为核心思想

### 优点

* 快速创建独立运行的SPring项目以及与主流框架集成
* 使用嵌入式的Servlet容器，无需使用打成war包加入到服务器
* starters自动依赖与版本控制
* 大量的自动配置，简化开发，也可修改默认值
* 无需配置XML，无代码生成，开箱即用
* 准生产环境的运行时应用监控
* 与云计算的天然继承 

### 缺点：

* 上手简单，学会难

## 微服务

微服务：架构风格

一个应用应该是一组小型服务；可以通过HTTP的方式来进行互通

每一个功能元素最终都是一个可独立替换和独立升级的软件

更新是一种服务，那么改 删 查就是他的模块？

![image-20201208165624464](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208165624.png)

详细参照微服务文档：https://www.jianshu.com/p/4821a29fa998

**单体应用**

![image-20201208165610683](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208165611.png)



## HelloWorld

小问题：浏览器请求hello请求，服务器接受请求并处理，响应Hello World字符串

### 创建Maven工程

 导入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>org.example</groupId>
    <artifactId>1-HelloWorld</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>



    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

### 编写主程序

```java
package com.HelloWorld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;


/**告诉SpringBoot来标注一个主程序*/
@SpringBootApplication
@RestController
public class helloWorld {
      
    public static void main(String[] args) {
        SpringApplication.run(helloWorld.class, args);
    }

    @GetMapping("/hello")
    public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
        return String.format("Hello %s!", name);
    }

}

```

### 测试结果

![image-20201208175622633](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208175622.png)

![image-20201208175615926](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208175616.png) 

###  简化部署

以前的话，我们需要将项目打成war，但是SpringBoot只需要导入Maven插件

```xml
<plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludeDevtools>false</excludeDevtools>
            </configuration>
    </plugin>
</plugins>
```

**打包**

![image-20201208202208816](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208202209.png)

![image-20201208202143156](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208202144.png)

* 通过 java -jar jar包名.jar

## 分析

### 探究Hello World

#### 1.POM文件

#####  1.父项目

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.5.RELEASE</version>
</parent>

他的父项目是
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.5.RELEASE</version>
</parent>
他来真正管理Spring Boot 应用里面的所有依赖版本
```

Spring Boot的版本仲裁中心

以后我们导入依赖默认是不需要写版本（这是规定）；(没有在dependencies里面的依赖自然需要声明版本号)

![image-20201208203827972](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208203828.png)



####  2.启动器

在我们的项目中我们引入了`spring-boot-starter-web`依赖

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

首先我们要明白什么是**spring-boot-starter**

spring-boot-starter:

* spring-boot场景启动器，帮我们导入web模块正常运行所依赖的组件
* 说白了就是spring-boot-starte-xxx就是SpringBoot规定好的的依赖的集合，导入这一个`spring-boot-starter-xxxx`依赖，直接导入官方规定的所有依赖

例如：**spring-boot-starter-web**

![image-20201208210955946](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208210956.png)

**spring-boot-starter-web.pom**

![image-20201208211128525](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208211129.png)

不同的功能有不同的Starter

![image-20201208211410001](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201208211410.png)

**总结：Spring Boot将所有的功能场景都抽取出来，做成一个一个的starter(启动器)，只需要在项目里面引入这些starter(启动器)相关场景，就好像上文提供的`spring-boot-starter-web`，这样就不用一个个去自己费尽心思去引入和配置了，这也就是约定大于配置SpringBoot已经把这些规定好了，只需要你自己配置其他不在规定里面的东西，也就是后面的自定义操作**



####  3. 主程序入口，主入口类

```java
package com;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RestController;


/**告诉SpringBoot来标注一个主程序,告诉这是一个SpringBoot应用  */
@SpringBootApplication
public class Helloworld {

    public static void main(String[] args) {

        SpringApplication.run(Helloworld.class, args);
        
    }

}
```

##### **@SpringBootApplication**

* Spring Boot 应用标注这个某个类上说明这个类就是SpringBoot的主配置类，SpringBoot就应该运行是这个类的main方法来启动SpringBoot

![image-20201209133120424](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209133128.png)



######  1. @SpringBootConfiguration

* SpringBoot配置类，标注某个类上，表示这是一个Spring Boot的配置类
  * @Configuration(Spring中的注解):
    * 配置类上标注这个注解，配置类----配置文件，他也是一个容器的组件：@Component





###### 2. @EnableAutoConfiguration

* 开启自动配置功能，以前我们需要配置的东西，SpringBoot帮我们自动配置,@EnableAutoConfiguration告诉SpringBoot开启自动配置功能；这样自动配置才能生效

* ![image-20201209133528640](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209133528.png)

  

  2.1.**@AutoConfigurationPackage:自动配置包**、

  * **@Import(AutoConfigurationPackages.Registrar.class)：**
    * ![image-20201209150953529](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209201625.png)
      * 2.11 **@import：**Spring底层注解@import，给容器导入一个组件，**@AutoConfigurationPackage**中导入的是**AutoConfigurationPackages.Registrar.class**
      * 2.12  **AutoConfigurationPackages.Registrar**中registerBeanDefinitions()方法可以自动注册bean，将当前主配置类所在的包下的所有组件扫描进来
      * 2.12的步骤：
      * ![image-20201209145056708](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209145057.png)
      * ![image-20201209145345894](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209145346.png)
      * 将上图的metadata传入给PackageImports类的构造器获取数据
      * ![image-20201209145631013](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209145631.png)
      * 可以取得数据result,result也就是当前@SpringBootApplication标注的类(主配置类)的所在的包名
      * ![image-20201209145801925](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209145801.png)
      * **个人小结：最后交给register()方法进行处理，开始注册主配置类所在包下的所有组件,包括自己，我猜测的是先去获取包名然后再去，扫描其他注解**

  2.2 @Import(AutoConfigurationImportSelector.class)

  * 2.21 AutoConfigurationImportSelector:自动配置导入选择器

    * **getCandidateConfigurations()方法**，返回一个**AutoConfigurationEntry**类型的对象，当前对象包括**configurations**，**configurations**就是将所有导入的组件以全类名的方式返回的字符串结合；这些组件会被添加到容器中，会给容器中导入非常多的自动配置(xxxAutoConfiguration);就是给容器中导入这个场景（starter）所有的组件，并配置好这些组件

      * ![](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201209231034703.png)

      * **getCandidateConfigurations()**方法内部**SpringFactoriesLoader.loadFactoryNames(**getSpringFactoriesLoaderFactoryClass(),
              getBeanClassLoader()**)**可以获取到==configurations==

        * ![image-20201213205915719](/image-20201213205915719.png)

          进入**loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),getBeanClassLoader())**方法

          通过调试就可调试出其中getSpringFactoriesLoaderFactoryClass()和getBeanClassLoader()和分别是**EnableAutoConfiguration**和当前类	AutoConfigurationImportSelector的类路径

          ![image-20201213232256350](/image-20201213232256350.png)

          ![image-20201213232959696](/image-20201213232959696.png)

      有了这些自动配置类，免去我们手动编写配置注入功能组件的工作自动配置类

      

##### 测试

* 如果是上文所讲的那样的话，他不会扫描到其他除主配置类所在包下得组件

![image-20201209152113732](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209152115.png)

访问失败

![image-20201209152229466](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201209152229.png)



---



## 使用Spring Initializer向导快速创建SpringBoot项目

![image-20201215191353267](/image-20201215191353267.png)

![image-20201218152235643](/image-20201218152235643.png)

![image-20201218152254707](/image-20201218152254707.png)

创建完成

![image-20201218152334558](/image-20201218152334558.png)

访问成功

![image-20201218160942525](/image-20201218160942525.png)



 **默认生成的SpringBoot项目：**

* 主程序已经生成好了，我们只需要我们自己的逻辑
* resources文件中的目录结构
  * static：保存所有的静态的资源；js css images
  * templates：保存所有的模板页面;(SpringBoot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面)，但是可以使用模板引擎(freemarker，thymeleaf)
  * application.properties：springboot应用的配置文件，可以修改SpringBoot提供的一些自动配置的默认值（例如端口号）
    * ![image-20201218170740214](/image-20201218170740214.png)
    * ![image-20201218170758980](/image-20201218170758980.png)



----

# 配置

## 配置文件

SpringBoot使用全局的配置文件，**通过全局配置文件修改SpringBoot的自动配置的默认值**(例如：端口号，contextPath等)，提供了两种配置文件

* application.properties
* application.yml



### YAML

* **.yml是YAML（YAML Ain't Markup Language）语言的文件，也是一种标记语言，但是以数据为中心，比json、xml等更适合做配置文件**

  * **标记语言：**
    * **以前的配置文件；大多数都使用*.xml,xml就是一种标志语言**

* **YAML配置实例**

  * ```yaml
    server:
      port: 8081
    ```

* **XML配置实例**

  * ```xml
    <server>
        <port>
          8081
        </port>
    </server>
    ```

* **YAML的基本语法**

  * 使用K:(空格)V的表示一对键值对(空格必须有)

  * 以空格的缩进来控制层级关系；只要是左对齐的一列数据，都是一个层级的,**例如下面的server就是path和port的父级，port和path就是统一级别**

    * ```yaml
      server: 
         path: /hello
         port: 8081
       
      ```

* **值的写法**

  * 字面量：普通的值(数字，字符串，布尔)

    * k：v(字面值直接来写)

      * 字符串默认不用加上单引号或双引号

        * 双引号：不会转义字符串里面的特殊字符，特殊字符回座位本身想表示的意思

          * 例子：name："张三 \n 李四"  的输出就是 张三 换行 李四

        * 单引号：回转义字符，特殊字符会变成不同字符输出‘

          * 例子：name："张三 \n 李四"  的输出就是 张三 \n 李四

            

  * 对象，Map

    * 对象例子：

      * ```yaml
        正常写法
        pojo:
          name: xl
          age: 14
        行内写法
        pojo: { name:xl,age: 14}
        ```

  * 数组（list，set）

    * 用-表示数组中的元素

      * 例子：

        * ```yaml
          正常写法
          pets:
            - cat
            - dog
            - pig
          行内写法
          pets: [cat,dog,pig]
          ```

  常用写法：

  

  ![image-20201218200343903](/image-20201218200343903.png)

  ![image-20201218200408002](/image-20201218200408002.png)

  ![image-20201218200421475](/image-20201218200421475.png)

  ![image-20201218200433723](/image-20201218200433723.png)

* 代码：

  * java

    * ```java
      package springboot.Bean;
      
      import org.springframework.boot.context.properties.ConfigurationProperties;
      import org.springframework.stereotype.Component;
      
      import java.util.Date;
      import java.util.List;
      import java.util.Map;
      
      
      /**
       * 将我们的配置文件的每一个属性的值,映射到组件中
       * @ConfigurationProperties:告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
       *     注意：prefix不可以是大写
       *     只有这个组件是容器中的组件，才能使用容器提供的@ConfigurationProperties功能
       * */
      @Component
      @ConfigurationProperties(prefix = "person")
      public class person {
          private String name;
          private int age;
          private boolean isMan;
          private Date birth;
          private Map<String,Object> maps;
          private List<String> list;
          private Dog dog;
      
          public String getName() {
              return name;
          }
      
          public void setName(String name) {
              this.name = name;
          }
      
          public int getAge() {
              return age;
          }
      
          public void setAge(int age) {
              this.age = age;
          }
      
          public boolean isMan() {
              return isMan;
          }
      
          public void setMan(boolean man) {
              isMan = man;
          }
      
          public Date getBirth() {
              return birth;
          }
      
          public void setBirth(Date birth) {
              this.birth = birth;
          }
      
          public Map<String, Object> getMaps() {
              return maps;
          }
      
          public void setMaps(Map<String, Object> maps) {
              this.maps = maps;
          }
      
          public List<String> getList() {
              return list;
          }
      
          public void setList(List<String> list) {
              this.list = list;
          }
      
          public Dog getDog() {
              return dog;
          }
      
          public void setDog(Dog dog) {
              this.dog = dog;
          }
      
          @Override
          public String toString() {
              return "person{" +
                      "name='" + name + '\'' +
                      ", age=" + age +
                      ", isMan=" + isMan +
                      ", birth=" + birth +
                      ", maps=" + maps +
                      ", list=" + list +
                      ", dog=" + dog +
                      '}';
          }
      }
      
      ```

    

    * yml: 

      * ```yaml
        #  public class person {
        #    private String name;
        #    private int age;
        #    private boolean isMan;
        #    private Date birth;
        #    private Map<String,Object> maps;
        #    private List<String> list;
        #    private Dog dog;
        #      public class Dog {
        #      private String name;
        #      private Integer age;
        
        person:
          name: 张三
          age: 15
          isMan: true
          birth: 2027/7/12
          maps:
            bro: 李四
            wife: 金莲
            sister: meimei
          list:
            - money
            - gun
          dog:
            name: timi
            age:  2
        
        ```

    * **spring-boot-configuration-processor：** 配置文件处理器,配置文件进行绑定就会有提示

      * ```xml
        <dependency>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-configuration-processor</artifactId>
                    <optional>true</optional>
                </dependency>
        ```

  * **YAML语法规范：**https://yaml.org/



### Properties

```properties
server.port=8081

person.name=lisi
person.age=17
person.isMan=false
person.birth=2000/7/7
person.list=1,2,6
person.maps.k1=k2
person.maps.k2=k3
person.dog.name=wangwang
person.dog.age=15
```

   

### @value

* 刚才通过@configurationProperties(prefix="xxx"),来注入类

* 我们还可以通过Spring注解@value注入属性值
  * @value可以使用${key},表示可以在环境变量和环境文件中取值
  * @value还可以使用#{key},表示可以使用SPring表达式语言

![image-20201218205651631](/image-20201218205651631.png)

例子：       ![image-20201218210729583](/image-20201218210729583.png)



### @ConfigurationProperties和@value的比较

|              | @ConfigurationProperties | @Value     |
| ------------ | ------------------------ | ---------- |
| 功能         | 批量注入                 | 一个个指定 |
| 松散绑定     | 支持                     | 不支持     |
| Spel         | 不支持                   | 支持       |
| JSR303       | 支持                     | 不支持     |
| 复杂类型封装 | 支持                     | 不支持     |

![image-20201218212927903](/image-20201218212927903.png)



* 如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value

  * 例子：

    ![image-20201218213506087](/image-20201218213506087.png)

* 如果说，我们专门编写一个javaBean来和配置文件进行映射，使用@ConfigurationProperties



### @PropertySource和@ImprotResource和@Beans

#### @PropertySource

* 用于读取指定的配置文件（只可以读取properties后缀名的文件）

* 例子：

  已经注释了默认配置文件的内容

  ![image-20201219190235053](/image-20201219190235053.png)

  将同样的值输入进person.propertis

  ![image-20201219190314330](/image-20201219190314330.png)

  输出结果：

  ![image-20201219190401219](/image-20201219190401219.png)



#### @ImportResource

* 导入Spring的配置文件，让配置文件里面的内容，将这个注解标注到配置类
* 例子：

​    创建一个service，加入到Spring容器

![image-20201219191631052](/image-20201219191631052.png)

   直接通过@Autowired获取值，运行结果报错



![image-20201219191929365](/image-20201219191929365.png)

在主配置类加上次注解@ImportResource

![image-20201219192956438](/image-20201219192956438.png)



 **但是在实际中，我们不可能使用Spirng的配置文件来import，SpringBoot推荐的方式通过Spring的注解@Bean来实现**



#### @Bean

* Spring以前的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="helloService" class="springboot.Service.helloService"></bean>

</beans>
```

* SpringBoot推荐给容器中添加组件的方式；推荐使用全注解的方式
  * 1.配置类====Spring配置文件

  * ```java
    package springboot.config;
    
    
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import springboot.Bean.person;
    import springboot.Service.helloService;
    
    
    /**
     *  @Configuration 指明当前类是一个配置类，就是来替代之间的Spring配置文件
     *    相当于在配置文件中用<bean></bean>加入组件
     *
     * */
    
    @Configuration
    public class myConfiguration {
    
        /**@Bean: 将方法的返回值添加到容器中，容器中这个组件默认的id就是方法名*/
        @Bean
        public helloService helloService() {
          return   new helloService();
        }
    }
    
    ```

  * ![image-20201219195812593](/image-20201219195812593.png)



### 配置文件占位符

 ![image-20201219200351682](/image-20201219200351682.png)

例子：

这里只选用了properties，其实yaml也可以

![image-20201219201059545](/image-20201219201059545.png)



### Profile

* Spring对不同环境提供不同配置功能的支持，可以通过激活、指定参数等方式快速切换环境

#### 多Profile文件

我们可以在主配置文件编写的时候，文件名可以是 application-{profilename}.properties/yaml

例子：

![image-20201219202613857](/image-20201219202613857.png)

当我们什么也不指定的时候，默认读取主配置文件

![image-20201219202715113](/image-20201219202715113.png)

我们可以指定**profilename**

![image-20201219203054696](/image-20201219203054696.png)

![image-20201219203120051](/image-20201219203120051.png)

####  yml支持多文档块方式

![image-20201219204842417](/image-20201219204842417.png)

#### 激活指定的profile

##### 通过配置文件配置

* ![image-20201219203009115](/image-20201219203009115.png)

![image-20201219203123786](/image-20201219203123786.png)

##### 命令行

* 命令行参数：--spring.profiles.active=profileName
* ![image-20201219205537231](/image-20201219205537231.png)
* 或者是打包好，在命令行里面运行
* ![image-20201220194613342](/image-20201220194613342.png)

##### jvm参数

* Dspring.profiles.active=profileName
* ![image-20201220194212503](/image-20201220194212503.png)

#### 加载顺序

##### 配置文件的加载位置

![image-20201220195739086](/image-20201220195739086.png)

**优先级如下**

![image-20201220200617838](/image-20201220200617838.png)

SpringBoot会从这四个位置全部加载主配置文件，会出现**互补配置**

**互补配置：**

![image-20201220201610697](/image-20201220201610697.png)

![image-20201220201735213](/image-20201220201735213.png)



我们还可以通过**spring.config.location**来指定配置文件

* **项目打包好了以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置(和Spring.profiles.active不一样，前者是面向不同的文件目录下的不同的配置文件，后者则是面向同一个文件目录下的不同的配置文件);指定的配置文件和默认加载的这些配置文件共同起作用形成互补配置**
* 例子：同样的道理，相当于将这个配置文件进行覆盖和互补配置
* 在C:/Users/lenovo/Desktop/application.properties创建配置文件
* ![image-20201220210450685](/image-20201220210450685.png)
* ![image-20201220210634998](/image-20201220210634998.png)
* 但是2.1.2版本以后的的SpringBoot需要通过 **--spring.config.addtional-location**来替换**spring.config.location**否则只会覆盖





##### 外部配置文件加载顺序

**Spring Boot 支持多种外部配置方式，优先级如下从高到低；高优先级的配置覆盖低优先级配置，所有的配置会形成互补配置**
**这些方式优先级如下：https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-external-config**

* 1.命令行参数	
  * 举例：
    * 可以指定特定的默认值例如端口号：java   -jar  3-configuration2-0.0.1-SNAPSHOT.jar   --server.port=6666
    * ![image-20201220213006864](/image-20201220213006864.png)
*  2.来自java:comp/env的JNDI属性
* 3.Java系统属性（System.getProperties()）
* 4.操作系统环境变量
* 5.RandomValuePropertySource配置的random.*属性值

  ==**优选加载带profile **==

* **6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件**
* **7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

  ==**再加载不带profile**==

* **8.jar包外部的application.properties或application.yml(不带spring.profile)配置文件**
* **9.jar包内部的application.properties或application.yml(不带spring.profile)配置文件**
* 10.@Configuration注解类上的@PropertySource
* 11.通过SpringApplication.setDefaultProperties指定的默认属性



6-9举例

当我们什么也不干启动项目

![image-20201220213957107](/image-20201220213957107.png)

当我们在war包外创建外部配置文件（不带profile）

![image-20201220214418352](/image-20201220214418352.png)

![image-20201220214355352](/image-20201220214355352.png)

当我们在war包外创建外部配置文件（带profile）

略







## 配置原理

[配置文件能配置的属性参照](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties-core)



1. SpringBoot启动的时候加载主配置类开启了自动配置功能==@EnableAutoConfiguration==

2. **@EnableAutoConfiguration作用**：

   1. 利用**AutoConfigurationImportSelector**导入一些组件

   2. 通过类中**selectImports(AnnotationMetadata annotationMetadata) **里面的调用**getAutoConfigurationEntry(annotationMetadata)**方法

      ![image-20201221192332774](/image-20201221192332774.png)

   3. **getAutoConfigurationEntry(annotationMetadata)**方法中通过调用**List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)**获取==configuration==

      ![image-20201221192526975](/image-20201221192526975.png)

   4. ![image-20201221195724251](/image-20201221195724251.png)

      ![image-20201221202349248](/image-20201221202349248.png)

      META-INF/spring.factoryies：

      ```properties
      # Initializers
      org.springframework.context.ApplicationContextInitializer=\
      org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
      org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener
      
      # Application Listeners
      org.springframework.context.ApplicationListener=\
      org.springframework.boot.autoconfigure.BackgroundPreinitializer
      
      # Auto Configuration Import Listeners
      org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
      org.springframework.boot.autoconfigure.condition.ConditionEvaluationReportAutoConfigurationImportListener
      
      # Auto Configuration Import Filters
      org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
      org.springframework.boot.autoconfigure.condition.OnBeanCondition,\
      org.springframework.boot.autoconfigure.condition.OnClassCondition,\
      org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition
      
      # Auto Configure
      org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
      org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
      org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
      org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
      org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
      org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
      org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
      org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
      org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration,\
      org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
      org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
      org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
      org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRestClientAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.r2dbc.R2dbcDataAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.r2dbc.R2dbcRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.r2dbc.R2dbcTransactionManagerAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
      org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
      org.springframework.boot.autoconfigure.elasticsearch.ElasticsearchRestClientAutoConfiguration,\
      org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
      org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
      org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
      org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
      org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
      org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
      org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
      org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
      org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration,\
      org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration,\
      org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration,\
      org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
      org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
      org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
      org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
      org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
      org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
      org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
      org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
      org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
      org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
      org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
      org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
      org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
      org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
      org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
      org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration,\
      org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
      org.springframework.boot.autoconfigure.availability.ApplicationAvailabilityAutoConfiguration,\
      org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
      org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
      org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
      org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
      org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
      org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
      org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
      org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration,\
      org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
      org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
      org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration,\
      org.springframework.boot.autoconfigure.r2dbc.R2dbcAutoConfiguration,\
      org.springframework.boot.autoconfigure.rsocket.RSocketMessagingAutoConfiguration,\
      org.springframework.boot.autoconfigure.rsocket.RSocketRequesterAutoConfiguration,\
      org.springframework.boot.autoconfigure.rsocket.RSocketServerAutoConfiguration,\
      org.springframework.boot.autoconfigure.rsocket.RSocketStrategiesAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.rsocket.RSocketSecurityAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.saml2.Saml2RelyingPartyAutoConfiguration,\
      org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
      org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
      org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration,\
      org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
      org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration,\
      org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration,\
      org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
      org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
      org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
      org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.reactive.function.client.ClientHttpConnectorAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration,\
      org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
      org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration,\
      org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration,\
      org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration,\
      org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration,\
      org.springframework.boot.autoconfigure.webservices.client.WebServiceTemplateAutoConfiguration
      
      # Failure analyzers
      org.springframework.boot.diagnostics.FailureAnalyzer=\
      org.springframework.boot.autoconfigure.data.redis.RedisUrlSyntaxFailureAnalyzer,\
      org.springframework.boot.autoconfigure.diagnostics.analyzer.NoSuchBeanDefinitionFailureAnalyzer,\
      org.springframework.boot.autoconfigure.flyway.FlywayMigrationScriptMissingFailureAnalyzer,\
      org.springframework.boot.autoconfigure.jdbc.DataSourceBeanCreationFailureAnalyzer,\
      org.springframework.boot.autoconfigure.jdbc.HikariDriverConfigurationFailureAnalyzer,\
      org.springframework.boot.autoconfigure.r2dbc.ConnectionFactoryBeanCreationFailureAnalyzer,\
      org.springframework.boot.autoconfigure.session.NonUniqueSessionRepositoryFailureAnalyzer
      
      # Template availability providers
      org.springframework.boot.autoconfigure.template.TemplateAvailabilityProvider=\
      org.springframework.boot.autoconfigure.freemarker.FreeMarkerTemplateAvailabilityProvider,\
      org.springframework.boot.autoconfigure.mustache.MustacheTemplateAvailabilityProvider,\
      org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAvailabilityProvider,\
      org.springframework.boot.autoconfigure.thymeleaf.ThymeleafTemplateAvailabilityProvider,\
      org.springframework.boot.autoconfigure.web.servlet.JspTemplateAvailabilityProvider
      
      ```

      其中每一个xxxAutoConfiguration类都是容器的一个组件，都加入到容器中；通过他们作自动配置；

   5. 以HttpEncodingAutoConfiguration为例解释自动配置：

      ```java
      @Configuration(proxyBeanMethods = false)//表示这是一个配置类
      @EnableConfigurationProperties(ServerProperties.class)//表示启动指定类，ServerProperties.class中对应的属性值和当前类的属性绑定
      @ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)//spring底层注解，根据不同条件判断，如果满足，真个配置类的配置就会生效，当前这个@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)表示是否是web应用
      @ConditionalOnClass(CharacterEncodingFilter.class)//判断当前项目有没有这个类（CharacterEncodingFilter.class）是springMvc进行解决乱码的过滤器
      @ConditionalOnProperty(prefix = "server.servlet.encoding", value = "enabled", matchIfMissing = true)//表示是否存在这个配置server.servlet.encoding    matchIfMissing表示若果不存在也是可以成立的
      public class HttpEncodingAutoConfiguration {
      ```

      **ServerProperties.class**

      ```java
      @ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)//表示从配置文件和bean的属性进行绑定
      public class ServerProperties {
      ```

      **ServerProperties.class** 相当于用类代替了配置文件,换句话说所有在配置文件中能配置的属性都是在xxxProperties中；配置文件能配置什么就可以参照某个功能的对应的属性类

   6. 配置类就是根据当前不同的条件判断决定这个配置类是否生效，如果生效那就通过@bean加入进容器

      ```java
      /*
       * Copyright 2012-2020 the original author or authors.
       *
       * Licensed under the Apache License, Version 2.0 (the "License");
       * you may not use this file except in compliance with the License.
       * You may obtain a copy of the License at
       *
       *      https://www.apache.org/licenses/LICENSE-2.0
       *
       * Unless required by applicable law or agreed to in writing, software
       * distributed under the License is distributed on an "AS IS" BASIS,
       * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
       * See the License for the specific language governing permissions and
       * limitations under the License.
       */
      
      package org.springframework.boot.autoconfigure.web.servlet;
      
      import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
      import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
      import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
      import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
      import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
      import org.springframework.boot.autoconfigure.web.ServerProperties;
      import org.springframework.boot.context.properties.EnableConfigurationProperties;
      import org.springframework.boot.web.server.WebServerFactoryCustomizer;
      import org.springframework.boot.web.servlet.filter.OrderedCharacterEncodingFilter;
      import org.springframework.boot.web.servlet.server.ConfigurableServletWebServerFactory;
      import org.springframework.boot.web.servlet.server.Encoding;
      import org.springframework.context.annotation.Bean;
      import org.springframework.context.annotation.Configuration;
      import org.springframework.core.Ordered;
      import org.springframework.web.filter.CharacterEncodingFilter;
      
      @Configuration(proxyBeanMethods = false)//表示这是一个配置类
      @EnableConfigurationProperties(ServerProperties.class)//表示启动指定类，ServerProperties.class中对应的属性值和当前类的属性绑定，并且加入到IOC容器中
      @ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)//spring底层注解，根据不同条件判断，如果满足，真个配置类的配置就会生效，当前这个@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)表示是否是web应用
      @ConditionalOnClass(CharacterEncodingFilter.class)//判断当前项目有没有这个类（CharacterEncodingFilter.class）是springMvc进行解决乱码的过滤器
      @ConditionalOnProperty(prefix = "server.servlet.encoding", value = "enabled", matchIfMissing = true)//表示是否存在这个配置server.servlet.encoding    matchIfMissing表示若果不存在也是可以成立的
      public class HttpEncodingAutoConfiguration {
          
          /*这里已经和SpringBoot配置文件映射了，因为这个类只有一个构造器,表示会从容器中获取,在类注解上
          @EnableConfigurationProperties(ServerProperties.class)已经将该类注入到容器中了
          */
      	private final Encoding properties;
      
      	public HttpEncodingAutoConfiguration(ServerProperties properties) {
      		this.properties = properties.getServlet().getEncoding();
      	}
      
      	@Bean   //给容器添加一个组件
      	@ConditionalOnMissingBean
      	public CharacterEncodingFilter characterEncodingFilter() {
      		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
      		filter.setEncoding(this.properties.getCharset().name());//从properties中获取编码形式
      		filter.setForceRequestEncoding(this.properties.shouldForce(Encoding.Type.REQUEST));
      		filter.setForceResponseEncoding(this.properties.shouldForce(Encoding.Type.RESPONSE));
      		return filter;
      	}
      
      	@Bean
      	public LocaleCharsetMappingsCustomizer localeCharsetMappingsCustomizer() {
      		return new LocaleCharsetMappingsCustomizer(this.properties);
      	}
      
      	static class LocaleCharsetMappingsCustomizer
      			implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>, Ordered {
      
      		private final Encoding properties;
      
      		LocaleCharsetMappingsCustomizer(Encoding properties) {
      			this.properties = properties;
      		}
      
      		@Override
      		public void customize(ConfigurableServletWebServerFactory factory) {
      			if (this.properties.getMapping() != null) {
      				factory.setLocaleCharsetMappings(this.properties.getMapping());
      			}
      		}
      
      		@Override
      		public int getOrder() {
      			return 0;
      		}
      
      	}
      
      }
      
      ```

      **所以我们可以知道，在配置类中的属性我们可以类推回去，在主配置文件中配置属性**

      ![image-20201221211251884](/image-20201221211251884.png)

      ![image-20201221211334929](/image-20201221211334929.png)

      **总结：**

      * SpringBoot会加载大量的自动配置类

      * 我们看看我们需要的功能有没有SpringBoot默认写好的自动配置类

      * 我们看看这个自动配置类到底配置可那些组件；只要我们要用的(@Bean)组件在这里，我们就不用去配置了

      * 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性，我们就可以在SpringBoot配置文件文件中指定这些属性的值

      * ![image-20201221212329885](/image-20201221212329885.png)

        

        

        

        

        

      

#### 小细节@Conditional

**@Conditional派生注解（Spring注解半原生的@Conditional作用）**

作用：必须是@Conditional派生注解指定的条件成立，才给容器中添加组件，配置类里面的所有内容才会生效

![image-20201223212838613](/image-20201223212838613.png)



所以说自动配置类必须在一定的条件下才可以生效，举个例子：

![image-20201223214229729](/image-20201223214229729.png)

这个类中有很多@ConditionalXXX，说明必须符合条件才可以启动这个类，但是一个个的调试过于疲惫，所以我们使用SpringBoot提供的

![image-20201223214446665](/image-20201223214446665.png)

**输出结果：**

![image-20201223215004161](/image-20201223215004161.png)

他会告诉你有哪些自动配置类启动了







# 日志

## 1.日志框架

* 开发系统的时候，我们需要定点输出等操作，实现调试关键数据；但是在项目发布的时候，有的输出局太需要，一个个删除过于恶心人，所以需要日志框架，查看一些运行的消息 
* 随着时代的更新换代，需求不断在更新，所以出现了日志门面（日志的抽象层（接口）），后续的代码更新只需要面向这个接口就可以了

## 2.市面上的日志框架

**市场上存在非常多的日志框架。JUL（java.util.logging），JCL（Apache Commons Logging），Log4j，Log4j2，Logback、SLF4j、jboss-logging等。Spring Boot在框架内容部使用JCL，spring-boot-starter-logging采用了slf4j+logback的形式，Spring Boot也能自动适配（jul、log4j2、logback）并简化配置**

![image-20201224204828948](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201224205504.png)

左边选一个，右边来和实现

最常用的日志门面：==SLF4j==

最常用的日志实现：==LogBack==

SpringBoot：底层是Spring框架，Spirng框架默认使用**JCL**

​      SpringBoot选用**SLF4j**和**logback**





## 3.SLF4j的使用

### 1.如何在系统中使用SLF4j

以后开发的时候，日志记录，我们不应该调用日志的实现类，而是调用日志抽象层里面的方法；

首先给系统导入Slf4j和logback的实现jar

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

 

![image-20201224210424422](/image-20201224210424422.png)



每一个实现框架都有自己的配置文件，SLF4J只提供接口，真正需要配置文件的是实现层吗，所以说配置文件只需要把关注点放在实现层就可了，换句话说，**配置文件就是实现层本身的配置文件**

###  2.遗留问题

当前开发的系统（slf4j+logback）：Spring（jcl）、Hibernate（jboss-logging）..........

我们用的日志框架和工具的底层框架不一样，所以我们需要同意日志框架。就是让所有和我同一使用SLF4J进行输出？

解决办法：

![image-20201224212942308](/image-20201224212942308.png)



**解决办法：**

1. **排除对应的jar包**
2. **使用替换包替换原来的包**
3. **通过SLF4J实现**



### 3.SpringBoot日志关系

```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
      <version>2.4.1</version>
      <scope>compile</scope>
</dependency>
```

底层依赖关系

![image-20201225192901895](/image-20201225192901895.png)

总结：

1. SpringBoot底层使用SLF4J和Logback的框架组合
2. SpringBoot也把其他的日志都替换成了slf4j
3. 中间替换包

![image-20201225193952726](/image-20201225193952726.png)

4.若使用其他框架，我们一定要把这个框架的默认日志依赖移除掉

**SpringBoot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉**



###  4.测试

```java
package springboot;

import org.junit.jupiter.api.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class ApplicationTests {

    Logger logger = LoggerFactory.getLogger(getClass());

    @Test
    void contextLoads() {
        /**
         *日志级别表示由低到高   trace<debug<info<warn<error
         *  我们可以调节日志级别,日志就只会在删除这个级别以及比他等级低的
         *
         */
       logger.trace("这是trace");
       logger.debug("这是debug");
       /**springboot默认给我们使用的是info级别,可以在配置文件中配置对应的级别logging.level.项目父包名=隔离级别
        *     没有指定级别的就用SpringBoot默认规定的空偶尔：root级别
        * */
       logger.info("这是info");
       logger.warn("这还warn");
       logger.error("这是error");

    }

}

```



对应的配置：

```properties
# 只说几个
# 指定隔离等级
logging.level.springboot=debug

#不指定路径,在当前项目下生SpringBoot.log
logging.file.name=SpringBoot.log
#可以指定完整路径
#logging.file.name=E:/SpringBoot.log

#在当前磁盘的根路径下创建Spring文件夹和里面的log文件夹 ：使用spring.log 作为默认文件
#logging.file.path=/spring/log

# 指定在控制台输出的日志格式    日期          线程号     向左对齐  全类名50个字符
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n

# 指定在文件内输出的日志格式
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss.SSS}-----[%thread]----%-5level %logger{50} - %msg%n
```

输出格式对应的意义

```xml
    日志输出格式：
		%d表示日期时间，
		%thread表示线程名，
		%-5level：级别从左显示5个字符宽度
		%logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
		%msg：日志消息，
		%n是换行符
    -->
    %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
```

| logging.file | logging.path | Example  | Description                        |
| ------------ | ------------ | -------- | ---------------------------------- |
| (none)       | (none)       |          | 只在控制台输出                     |
| 指定文件名   | (none)       | my.log   | 输出日志到my.log文件               |
| (none)       | 指定目录     | /var/log | 输出到指定目录的 spring.log 文件中 |

### 5.指定配置

在你的类路径下放上每个日志框架自己的配置文件即可；SpringBoot就不适用其他默认配置了

![image-20201225211740104](/image-20201225211740104.png)



logback.xml:直接就被日志框架识别了

logback-spring.xml：日志框架就不直接加载这个配置文件，有SpringBoot来加载：

在logback-spring.xml里面配置

```xml
<appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!--
        日志输出格式：
			%d表示日期时间，
			%thread表示线程名，
			%-5level：级别从左显示5个字符宽度
			%logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
			%msg：日志消息，
			%n是换行符
        -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <springProfile name="dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}---------->[ %thread ] - [ %-5level ] [ %logger{50} : %line ] - %msg%n</pattern>
            </springProfile>
            <springProfile name="!dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}============[ %thread ] - [ %-5level ] [ %logger{50} : %line ] - %msg%n</pattern>
            </springProfile>
        </layout>
    </appender>
```



![image-20201225222635265](/image-20201225222635265.png)



可以通过参数配置和在主配置文件配置参数等方法配置profiles.active

![image-20201225224947276](/image-20201225224947276.png)



但是logback文件名字不修改成logback-Spring.xml还去使用，会出现这个错误

![image-20201225225114519](/image-20201225225114519.png)





##  4.切换其他框架

* 通过slf4j官方的日志适配图，进行相关的切换

### 步骤

1. 排除所有底层的其他日志实现框架（实现统一日志输出）
2. 将需要的日志的依赖导入
3. 将错误的转换包删除掉，例如：你需要log4j，但是你还存在sl4j-jul的jar包，就会发生错误
4. 加入对应日志实现层的配置文件
5. 运行

### 测试

对应的pom

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
    <groupId>SpringBoot</groupId>
    <artifactId>4-log</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>4-log</name>
    <description>Demo project for Spring Boot Log</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
<!--                排除logback实现-->
                <exclusion>
                    <groupId>ch.qos.logback</groupId>
                    <artifactId>logback-classic</artifactId>
                </exclusion>
             
            </exclusions>
        </dependency>

<!--        通过slf4j接口由log4j实现-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            
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

配置文件

![image-20201226173044749](/image-20201226173044749.png)

配置文件内容

```properties
### set log levels ###
log4j.rootLogger = debug ,  stdout ,  D ,  E

### 输出到控制台 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern =  %d{ABSOLUTE} %5p %c{ 1 }:%L - %m%n

#### 输出到日志文件 ###
#log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
#log4j.appender.D.File = logs/log.log
#log4j.appender.D.Append = true
#log4j.appender.D.Threshold = DEBUG ## 输出DEBUG级别以上的日志
#log4j.appender.D.layout = org.apache.log4j.PatternLayout
#log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
#
#### 保存异常信息到单独文件 ###
#log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
#log4j.appender.D.File = logs/error.log ## 异常日志文件名
#log4j.appender.D.Append = true
#log4j.appender.D.Threshold = ERROR ## 只输出ERROR级别以上的日志!!!
#log4j.appender.D.layout = org.apache.log4j.PatternLayout
#log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

```



运行结果：

![image-20201226173316695](/image-20201226173316695.png)

pom图：

![image-20201225231040761](/image-20201225231040761.png)



### SpringBoot官方提供场景启动器

![image-20201226174129206](/image-20201226174129206.png)

使用方法：

​	![image-20201226185926784](/image-20201226185926784.png)









# Web

使用SpringBoot；

1. **创建SpringBoot，选中我们需要的模块**
2. **SpringBoot已经默认将这些场景配置好了，只需要少量配置**
3. **自己编写业务代码**

如何才能自己做到好的web开发，需要了解自动配置原理

* xxxxAutoConfiguration：帮我们给容器自动配置组件
  * xxxxAutoConfiguration中的XXXProperties：配置类来封装配置文件的内容
    * ![image-20201226190945975](/image-20201226190945975.png)



## 1.SpringBoot对静态资源的映射规则

### 1.webjars静态资源

![image-20201226204659227](/image-20201226204659227.png)

所有的==/webjars/**==（就是webjars/所有请求）都去==classpath:/META-INF/resources/webjars/==下面

说白了就是以jar包的形式引入项目，你的请求会被SpringBoot接受处理去/META-INF/resources/webjars/下面寻找静态资源

**jquery对应的pom**

```xml
<!--        静态资源-->
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.5.1</version>
        </dependency>
```

**jquery对应的webjars的目录**

![image-20201226193951932](/image-20201226193951932.png)

**webjars官网：https://www.webjars.org/**

**测试**

浏览器测试请求对应的资源,访问路径是：http://localhost:8080/webjars/jquery/3.5.1/jquery.js

![image-20201226194039560](/image-20201226194039560.png)

**我们还可以设置和资源有关的参数，例如静态资源的缓存实际爱你**

![image-20201226205250065](/image-20201226205250065.png)

在配置文件中修改属性

![image-20201226205405110](/image-20201226205405110.png)



### 2./**访问当前项目下的任何资源

![image-20201226210328349](/image-20201226210328349.png)

也就是说访问资源路径是下面这个四个（静态资源文件夹）

```java
"classpath:/META-INF/resources/", 
"classpath:/resources/",
"classpath:/static/", 
"classpath:/public/" 
"/"：当前项目的根路径
```

举个例子：

  当你访问localhost:8080/abc====去下面的的静态文件夹访问

![image-20201226211948964](/image-20201226211948964.png)

### 3.欢迎页的设置

![image-20201226213444835](/image-20201226213444835.png)

静态资源文件夹下的所有index.html页面；全部都被/**映射

也就是说

localhost:8080/====从静态资源文件夹里面寻找资源

![image-20201226213230734](/image-20201226213230734.png)

访问结果

![image-20201226213303438](/image-20201226213303438.png)





### 4.配置喜爱的图标

所有路径下的**/favicon.ico都会映射到静态文件夹下面寻找



### 5.手动修改静态文件夹的位置

* 通过**spring.web.resources.static-locations=指定的路径**

![image-20201226214346291](/image-20201226214346291.png)

访问结果404

![image-20201226214525396](/image-20201226214525396.png)





## 2.模板引擎

JSP、Velocity、Freemarker、Thymeleaf



### Thymeleaf

* Thymeleaf是一款用于渲染XML/XHTML/HTML5内容的模板引擎。类似JSP，Velocity，FreeMaker等，它也可以轻易的与Spring MVC等Web框架进行集成作为Web应用的模板引擎。与其它模板引擎相比，Thymeleaf最大的特点是能够直接在浏览器中打开并正确显示模板页面，而不需要启动整个Web应用
* Spring Boot推荐使用Thymeleaf、Freemarker等后现代的模板引擎技术；一但导入相关依赖，会自动配置ThymeleafAutoConfiguration、FreeMarkerAutoConfiguration

#### 1.引入thymeleaf

1. 引入thymeleaf的场景启动器

   ```xml
   <!--  引入thymeleaf的starter-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-thymeleaf</artifactId>
           </dependency>
   ```





#### 2.Thymeleaf语法

```java
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {

   private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;

   public static final String DEFAULT_PREFIX = "classpath:/templates/";

   public static final String DEFAULT_SUFFIX = ".html";
```

说白了就是**Thymeleaf可以自动渲染在类路径下的templates下的.html为后缀名的文件**，

![image-20201228184018279](/image-20201228184018279.png)

**Thymeleaf官方文档：**https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html

使用：

1. 在页面上边导入thymeleaf的名称空间<html xmlns:th="http://www.thymeleaf.org">，方便有提示

   ```html
   <html xmlns:th="http://www.thymeleaf.org">
   ```

2. 使用thymeleaf

   ```html
   <!DOCTYPE html>
   <html xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
    <body>
         Success!
   
    <hr>
    <h1  th:class="${giao}" th:text="${giao}">不通过web直接访问静态资源就出现这段文本内容</h1>
   </body>
   </html>
   ```

​      运行结果：

![image-20201228191859379](/image-20201228191859379.png)



3. **语法规则**

   * th:text：改变当前元素里面的文本内容
   * th:任何html任何属性；来替换原来属性的值
   * ![image-20201228193145476](/image-20201228193145476.png)
   * ![image-20201229155322541](/image-20201229155322541.png)

4. 表达式语法

   ```properties
   Simple expressions:（表达式语法）
       Variable Expressions: ${...}：获取变量值；OGNL；
       		1）、获取对象的属性、调用方法
       		2）、使用内置的基本对象：
                       #ctx : the context object.
                       #vars: the context variables.
                       #locale : the context locale.
                       #request : (only in Web Contexts) the HttpServletRequest object.
                       #response : (only in Web Contexts) the HttpServletResponse object.
                       #session : (only in Web Contexts) the HttpSession object.
                       #servletContext : (only in Web Contexts) the ServletContext object.
                       例子：
                       ${session.foo}
               3）、内置的一些工具对象：
                   #execInfo : information about the template being processed.
                   #messages : methods for obtaining externalized messages inside variables expressions, in the samewayas they would be                             obtained using #{…} syntax.
                   #uris : methods for escaping parts of URLs/URIs
                   #conversions : methods for executing the configured conversion service (if any).
                   #dates : methods for java.util.Date objects: formatting, component extraction, etc.
                   #calendars : analogous to #dates , but for java.util.Calendar objects.
                   #numbers : methods for formatting numeric objects.
                   #strings : methods for String objects: contains, startsWith, prepending/appending, etc.
                   #objects : methods for objects in general.
                   #bools : methods for boolean evaluation.
                   #arrays : methods for arrays.
                   #lists : methods for lists.
                   #sets : methods for sets.
                   #maps : methods for maps.
                   #aggregates : methods for creating aggregates on arrays or collections.
                   #ids : methods for dealing with id attributes that might be repeated (for example, as a result of                   an iteration).
   
              4），变量选择表达式
   
               Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
                   补充：配合 th:object="${session.user}：
                    样例：
                       <div th:object="${session.user}">
                       <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
                       <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
                       <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
                       </div>
   
               Message Expressions: #{...}：获取国际化内容
               Link URL Expressions: @{...}：定义URL；
                       @{/order/process(execId=${execId},execType='FAST')}
               Fragment Expressions: ~{...}：片段引用表达式
                       <div th:insert="~{commons :: main}">...</div>
   
               Literals（字面量）
                     Text literals: 'one text' , 'Another one!' ,…
                     Number literals: 0 , 34 , 3.0 , 12.3 ,…
                     Boolean literals: true , false
                     Null literal: null
                     Literal tokens: one , sometext , main ,…
               Text operations:（文本操作）
                   String concatenation: +
                   Literal substitutions: |The name is ${name}|
               Arithmetic operations:（数学运算）
                   Binary operators: + , - , * , / , %
                   Minus sign (unary operator): -
               Boolean operations:（布尔运算）
                   Binary operators: and , or
                   Boolean negation (unary operator): ! , not
               Comparisons and equality:（比较运算）
                   Comparators: > , < , >= , <= ( gt , lt , ge , le )
                   Equality operators: == , != ( eq , ne )
               Conditional operators:条件运算（三元运算符）
                   If-then: (if) ? (then)
                   If-then-else: (if) ? (then) : (else)
                   Default: (value) ?: (defaultvalue)
               Special tokens:
                   No-Operation: _ 
   ```

   

5. 实例

   ```html
   <!DOCTYPE html>
   <html xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
    <body>
    th:text:
    <div th:text="${hello}">hello</div>
    <br>
    th:utext:
    <div th:utext="${hello}">hello</div>
    <br>
    
    th:each1:
   <!-- th:each每次遍历都会生成这个标签-->
    <h4 th:each="user:${users}">
        <tr th:text="${user}"></tr>
    </h4>
   
    <br>
    th:each2:
    <h4  th:text="${user}" th:each="user:${users}"></h4>
    <br>
   
    th:each2:
    <h4 th:each="user:${users}">
         <tr >[[${user}]]</tr>
    </h4>
    <br>
    </body>
   
   </html>
   ```

   



##  3.SpringMvc的自动配置原理

https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-webservices

**精髓：**

### 1. Spring MVC auto-configuration

Spring Boot 自动配置好了SpringMVC

以下是SpringBoot对SpringMVC的默认配置:**==（WebMvcAutoConfiguration）==**

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

  - 自动配置了ViewResolver（视图解析器：根据方法的返回值得到视图对象（View），视图对象决定如何渲染（转发？重定向？））
  - ContentNegotiatingViewResolver：组合所有的视图解析器的；
  - ==如何定制：我们可以自己给容器中添加一个视图解析器；自动的将其组合进来；==
  - ![image-20201229133818433](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201229133820.png)

- Support for serving static resources, including support for WebJars (see below).静态资源文件夹路径,webjars

- Static `index.html` support. 静态首页访问

- Custom `Favicon` support (see below).  favicon.ico

  

- 自动注册了 of `Converter`, `GenericConverter`, `Formatter` beans.

  - Converter：转换器；  public String hello(User user)：类型转换使用Converter
  - `Formatter`  格式化器；  2017.12.17===Date；

```java
		@Bean
		@ConditionalOnProperty(prefix = "spring.mvc", name = "date-format")//在文件中配置日期格式化的规则
		public Formatter<Date> dateFormatter() {
			return new DateFormatter(this.mvcProperties.getDateFormat());//日期格式化组件
		}
```

![image-20201229140720796](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201229140721.png)

​	==自己添加的格式化器转换器，我们只需要放在容器中即可==

- Support for `HttpMessageConverters` (see below).

  - HttpMessageConverter：SpringMVC用来转换Http请求和响应的；User---Json；

  - `HttpMessageConverters` 是从容器中确定；获取所有的HttpMessageConverter；

    ==自己给容器中添加HttpMessageConverter，只需要将自己的组件注册容器中（@Bean,@Component）==

    

- Automatic registration of `MessageCodesResolver` (see below).定义错误代码生成规则

- Automatic use of a `ConfigurableWebBindingInitializer` bean (see below).

  ==我们可以配置一个ConfigurableWebBindingInitializer来替换默认的；（添加到容器）==

  ```
  初始化WebDataBinder；
  请求数据=====JavaBean；
  ```

**org.springframework.boot.autoconfigure.web：web的所有自动场景，深入学习web就要研究这个包；**



If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`.

## 4.如何修改SpringBoot的配置默认配置

模式：

1. SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自动配置的(@Bean,@Componet)如果有就用用户自己配置的，如果没有，才自动配置如果有些组件可以有多个(viewResolver)将用户配合和自己默认的组合起来
2.  在SpringBoot中有很多的xxxwebMvcConfigurer帮助我们进行扩展
3. 在SpringBoot中有很多的xxxCustomizer帮助我们进行扩展

==心得==，只要是从容器中获取的，都可以通过@bean等方式加入到容器，在自己配置，前提就是在xxxMvcAutoConfiguration，已经帮你写好**从容器取出，并自动加载**的方法



## 5.扩展SpringMvc

If you want to keep Spring Boot MVC features, and you just want to add additional [MVC configuration](https://docs.spring.io/spring/docs/4.3.14.RELEASE/spring-framework-reference/htmlsingle#mvc) (interceptors, formatters, view controllers etc.) you can add your own `@Configuration` class of type `WebMvcConfigurerAdapter`, but **without** `@EnableWebMvc`. If you wish to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter` or `ExceptionHandlerExceptionResolver` you can declare a `WebMvcRegistrationsAdapter` instance providing such components.

如果你想要保留SpringBoot的mvc功能,你只是想加入额外的SpringMVC功能，例如拦截器，格式化组件，视图转换器等，你能加入你自己的**WebMvcConfigurer**类型得`@Configuration`配置类，但是不能`@EnableWebMvc`（自动配置）

### 1.实例

1. 编写一个配置类(@Configuratiob)，继承是`WebMvcConfigurer`

   ```java
   package springboot.config;
   
   
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
   import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
   /**通过调用WebMvcConfigurer接口，继承方法，来扩展SpringMVC
    * */
   @Configuration
   public class MyMvcConfig implements WebMvcConfigurer {
       @Override
       public void addViewControllers(ViewControllerRegistry registry) {
             registry.addViewController("/test").setViewName("success");
             registry.addViewController("/test1").setViewName("index");
   
       }
   }
   
   ```

   我们通过查看WebMvcConfigurer中的方法，全是用来扩展SpringMvc的功能的

   ![image-20201229154917886](/image-20201229154917886.png)

### 2.原理

  1.   WebMvcAutoConfiguration是SprngMVC的自动配置类

  2.   在做其他自动配置的时候，@Import(EnableWebMvcConfiguration.class)

     ```java
     @Configuration(proxyBeanMethods = false)
     @EnableConfigurationProperties(WebProperties.class)
     public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration implements ResourceLoaderAware {
     
         
         
     ```

     2.1 它的父类DelegatingWebMvcConfiguration

     ```java
     @Configuration(proxyBeanMethods = false)
     public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
     
     	private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();
     
        //从容器中所有的WebMvcConfigurer
     	@Autowired(required = false)
     	public void setConfigurers(List<WebMvcConfigurer> configurers) {
     		if (!CollectionUtils.isEmpty(configurers)) {
     			this.configurers.addWebMvcConfigurers(configurers);
     		}
     	}
         
         //一个参考实现；将所有的WebMvcConfigurer相关配置一起调用
       	@Override
     	protected void addViewControllers(ViewControllerRegistry registry) {
     		this.configurers.addViewControllers(registry);
     	}
       
         
     ```

     2.2 	**private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();**中的类型==WebMvcConfigurerComposite==	

     ```java
     class WebMvcConfigurerComposite implements WebMvcConfigurer {
     ```

	private final List<WebMvcConfigurer> delegates = new ArrayList<>();


	public void addWebMvcConfigurers(List<WebMvcConfigurer> configurers) {
		if (!CollectionUtils.isEmpty(configurers)) {
			this.delegates.addAll(configurers);
		}
	}
	//通过foreach和多态自动向上转型实现加载功能
	@Override
	public void addViewControllers(ViewControllerRegistry registry) {
		for (WebMvcConfigurer delegate : this.delegates) {
			delegate.addViewControllers(registry);
		}
	}


     ```

​	也就是说在==EnableWebMvcConfiguration==的setConfigurers中获取了所有类型为`WebMvcConfigurer`的值，我们的自动配置类也继承这个类，也会被装进容器

  我们的自动配置类会自动被调用





## 6.全面接管SpringMVC

SpringBoot对SpringMvc的自动配置不需要了，所有关于web模块的自动配置都是失效了，需要我们自己配置

我们需要在配置类中添加@EnableWebMvc

```java
package springboot.config;


import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
/**通过调用WebMvcConfigurer接口，继承方法，来扩展SpringMVC
 * */
@EnableWebMvc
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
          registry.addViewController("/test").setViewName("success");
          registry.addViewController("/test1").setViewName("index");

    }
}
```

为什么加上@EnableWebMvc就会失效了

1. ```java
   
   @Retention(RetentionPolicy.RUNTIME)
   @Target(ElementType.TYPE)
   @Documented
   @Import(DelegatingWebMvcConfiguration.class)
   public @interface EnableWebMvc {
   }
   
   ```

2. @Import(DelegatingWebMvcConfiguration.class)中的DelegatingWebMvcConfiguration类发现继承自WebMvcConfigurationSupport

   ```java
   @Configuration(proxyBeanMethods = false)
   public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
   
   	private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();
   
   
   	@Autowired(required = false)
   	public void setConfigurers(List<WebMvcConfigurer> configurers) {
   		if (!CollectionUtils.isEmpty(configurers)) {
   			this.configurers.addWebMvcConfigurers(configurers);
   		}
   	}
   
   ```

   

3. 我们在查看WebMvcAutoConfiguration类中的注解**@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)**

   ```java
   @Configuration(proxyBeanMethods = false)
   @ConditionalOnWebApplication(type = Type.SERVLET)
   @ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
   /**
   @ConditionalOnMissingBean(WebMvcConfigurationSupport.class)表示:没有WebMvcConfigurationSupport.class才开始继续进行 WebMvcAutoConfiguration的加载
   */
   @ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
   @AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
   @AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
   		ValidationAutoConfiguration.class })
   public class WebMvcAutoConfiguration {
   ```

**@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)表示:没有WebMvcConfigurationSupport.class才开始继续进行 WebMvcAutoConfiguration的加载，而DelegatingWebMvcConfiguration就是继承的WebMvcConfigurationSupport类，相当于根本就加载**









## 7.RestfulCRUD

### 1.默认访问首页

配置viewController

```java
package springboot.Config;


import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
/**通过调用WebMvcConfigurer接口，继承方法，来扩展SpringMVC
 * */
//@EnableWebMvc
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

//    @Override
//    public void addViewControllers(ViewControllerRegistry registry) {
//        registry.addViewController("/").setViewName("index");
//        registry.addViewController("index.html").setViewName("index");
//
//    }
    /**所有的WebMvcConfigurer都会被WebMvcConfigurerComposite中从容器中取出来，然后一起起作用*/
    @Bean
    public WebMvcConfigurer  webMvcConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void addViewControllers(ViewControllerRegistry registry) {
                    registry.addViewController("/").setViewName("index");
                    registry.addViewController("/index.html").setViewName("index");

            }
        };
    }
}

```

修改页面的引入静态资源的路径，好处就是当我们修改**项目路径**的时候，模板引擎会自动帮我们加上

![image-20201229213051851](/image-20201229213051851.png)



### 2.国际化

1. 编写**国际化配置文件**
2. 使用ResourceBundleMessageSource管理国际化资源文件
3. 在页面使用fmt:message取出国际化内容



**步骤：**

1. 编写国际化配置文件，抽取页面需要显示的国际化

   ![image-20201230130536966](/image-20201230130536966.png)

2. SpringBoot已经自动配置好了ResourceBundleMessageSource

   ```java
   /*
    * Copyright 2012-2019 the original author or authors.
    *
    * Licensed under the Apache License, Version 2.0 (the "License");
    * you may not use this file except in compliance with the License.
    * You may obtain a copy of the License at
    *
    *      https://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */
   
   package org.springframework.boot.autoconfigure.context;
   
   import java.time.Duration;
   
   import org.springframework.boot.autoconfigure.AutoConfigureOrder;
   import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
   import org.springframework.boot.autoconfigure.condition.ConditionMessage;
   import org.springframework.boot.autoconfigure.condition.ConditionOutcome;
   import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
   import org.springframework.boot.autoconfigure.condition.SearchStrategy;
   import org.springframework.boot.autoconfigure.condition.SpringBootCondition;
   import org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration.ResourceBundleCondition;
   import org.springframework.boot.context.properties.ConfigurationProperties;
   import org.springframework.boot.context.properties.EnableConfigurationProperties;
   import org.springframework.context.MessageSource;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.ConditionContext;
   import org.springframework.context.annotation.Conditional;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.support.AbstractApplicationContext;
   import org.springframework.context.support.ResourceBundleMessageSource;
   import org.springframework.core.Ordered;
   import org.springframework.core.io.Resource;
   import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
   import org.springframework.core.type.AnnotatedTypeMetadata;
   import org.springframework.util.ConcurrentReferenceHashMap;
   import org.springframework.util.StringUtils;
   
   /**
    * {@link EnableAutoConfiguration Auto-configuration} for {@link MessageSource}.
    *
    * @author Dave Syer
    * @author Phillip Webb
    * @author Eddú Meléndez
    * @since 1.5.0
    */
   @Configuration(proxyBeanMethods = false)
   @ConditionalOnMissingBean(name = AbstractApplicationContext.MESSAGE_SOURCE_BEAN_NAME, search = SearchStrategy.CURRENT)
   @AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
   @Conditional(ResourceBundleCondition.class)
   @EnableConfigurationProperties
   public class MessageSourceAutoConfiguration {
   
   	private static final Resource[] NO_RESOURCES = {};
       //绑定了配置文件的spring.messages
   	@Bean
   	@ConfigurationProperties(prefix = "spring.messages")
   	public MessageSourceProperties messageSourceProperties() {
   		return new MessageSourceProperties();
   	}
   
       
   	@Bean
   	public MessageSource messageSource(MessageSourceProperties properties) {
   		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
   		if (StringUtils.hasText(properties.getBasename())) {
                /**messageSource.setBasenames方法的意义设定是国际化资源文件的基础名,方法的参数里面的properties.getBasename()，在MessageSourceProperties我们可以知道properties.getBasename()的返回值就是messages,也就是说，在上面我们提到的，MessageSourceProperties要先去和配置文件的spring.messages绑定一下，换句话说我们可以通过在配置文件中通过Spring.messages设定国际化文件的基础名，当然这些全部都在类路径下。
                */
   			messageSource.setBasenames(StringUtils
   					.commaDelimitedListToStringArray(StringUtils.trimAllWhitespace(properties.getBasename())));
   		}
   		if (properties.getEncoding() != null) {
   			messageSource.setDefaultEncoding(properties.getEncoding().name());
   		}
   		messageSource.setFallbackToSystemLocale(properties.isFallbackToSystemLocale());
   		Duration cacheDuration = properties.getCacheDuration();
   		if (cacheDuration != null) {
   			messageSource.setCacheMillis(cacheDuration.toMillis());
   		}
   		messageSource.setAlwaysUseMessageFormat(properties.isAlwaysUseMessageFormat());
   		messageSource.setUseCodeAsDefaultMessage(properties.isUseCodeAsDefaultMessage());
   		return messageSource;
   	}
   
   ```

   ![image-20201230132209671](/image-20201230132209671.png)

   

3. 去页面获取国际化的值

   

```html
<!DOCTYPE html>
<html lang="en"  xmlns:th="http://www.thymeleaf.org">
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
		<meta name="description" content="">
		<meta name="author" content="">
		<title>Signin Template for Bootstrap</title>
		<!-- Bootstrap core CSS -->
		<link href="/css/bootstrap.min.css" th:href="@{webjars/bootstrap/5.0.0-beta1/css/bootstrap.css}" rel="stylesheet">
		<!-- Custom styles for this template -->
		<link href="/css/signin.css" th:href="@{/css/signin.css}" rel="stylesheet">
	</head>

	<body class="text-center">
		<form class="form-signin" action="../templates/dashboard.html">
			<img class="mb-4" th:src="@{/img/bootstrap-solid.svg}" src="/img/bootstrap-solid.svg" alt="" width="72" height="72">
			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
			<label class="sr-only" th:text="#{login.username}">Username</label>
			<input type="text" class="form-control" placeholder="Username" required="" autofocus="" th:placeholder="#{login.username}">
			<label class="sr-only" th:text="#{login.password}" >Password</label>
			<input type="password" class="form-control" placeholder="Password" required="" th:placeholder="#{login.password}">
			<div class="checkbox mb-3">
				<label>
          <input type="checkbox" value="remember-me" > [[#{login.remember}]]
        </label>
			</div>
			<button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.subBtn}">Sign in</button>
			<p class="mt-5 mb-3 text-muted">© 2017-2018</p>
			<a class="btn btn-sm">中文</a>
			<a class="btn btn-sm">English</a>
		</form>
	</body>

</html>
```



显示效果：

根据浏览器默认语言不一样而改变

![image-20201230154446930](/image-20201230154446930.png)



**点击链接切换国际化**

**原理：**

通过调节国际化Locale(区域化对象)；LocaleResolver（获取区域信息对象）

```java
	@Override
		@Bean
		@ConditionalOnMissingBean(name = DispatcherServlet.LOCALE_RESOLVER_BEAN_NAME)
		@SuppressWarnings("deprecation")
		public LocaleResolver localeResolver() {
             //下面表示，若不给配置文件任何配置，那么就用下面的默认localeResolver,默认的就是根据请求头带来的区域信息Locale进行国际化
			if (this.webProperties.getLocaleResolver() == WebProperties.LocaleResolver.FIXED) {
				return new FixedLocaleResolver(this.webProperties.getLocale());
			}
			if (this.mvcProperties.getLocaleResolver() == WebMvcProperties.LocaleResolver.FIXED) {
				return new FixedLocaleResolver(this.mvcProperties.getLocale());
			}
            //若没有，则使用AcceptHeaderLocaleResolver
			AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
			Locale locale = (this.webProperties.getLocale() != null) ? this.webProperties.getLocale()
					: this.mvcProperties.getLocale();
			localeResolver.setDefaultLocale(locale);
			return localeResolver;
		}

```

**请求头：**

![image-20201230160254368](/image-20201230160254368.png)

对应的前端：

![image-20201230182158149](/image-20201230182158149.png)

创建自定义的LocalReslover

```java
package springboot.Component;

import org.springframework.util.StringUtils;
import org.springframework.web.servlet.LocaleResolver;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Locale;

/**
 * 思路:如果通过链接传入请求头进行国际化设计
 *     让链接携带区域信息
 *
 *
 */
public class MyLocalResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        //获取区域信息
        String localeMessage = request.getParameter("l");
//        创建区域对象,先通过Locale.getDefault()获得区域的默认值
        Locale locale=Locale.getDefault();
         if(!StringUtils.isEmpty(localeMessage)){
             //例如：'zh_CN'将它分为zh CN zh为语言，CH为国家zh_CN
             String[] split = localeMessage.split("_");
             locale= new Locale(split[0], split[1]);


         }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {

    }
}

```



对应的LocaleReslover在配置类中加入到配置文件

```java
package springboot.Config;


import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import springboot.Component.MyLocalResolver;

/**通过调用WebMvcConfigurer接口，继承方法，来扩展SpringMVC
 * */
//@EnableWebMvc
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    }
//    我们要添加一个localResolver，让自动配置的localResolver失效
    @Bean
    public LocaleResolver  localeResolver(){

        MyLocalResolver myLocalResolver = new MyLocalResolver();
        return myLocalResolver;
    }
}

```



### 3.登入

开发期间模板引擎页面修改以后，要实时生效





1. 在配置文件中设定关闭thymeleaf缓存

   ​	

   ```properties
   #关闭thymeleaf的缓存
   spring.thymeleaf.cache=false
   ```

2. 通过Ctrl+F9实现重新编译

 登录错误消息的回显

Controller

```java
package springboot.Controller;

import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import java.util.Map;

@Controller
public class LoginController {

    @PostMapping("/login")
    public String login(@RequestParam("username") String username, @RequestParam("password") String password,
                        Map<String,String> map) {
        if(!StringUtils.isEmpty(username)&&password.equals("123456")) {
        //登入成功
        return "dashboard";
        }   else {
        //登入失败
            map.put("errorMessage", "您的用户名或密码错误");
            return "index" ;
        }
    }

}

```

html

```html
<!--	     进行判断,当判断完成之后，才会执行这个标签		-->
			<p style="color:red" th:text="${errorMessage}" th:if=" ${not #strings.isEmpty(errorMessage)}"></p>

```





### 4.登录检测

创建拦截器：

```java
package springboot.Controller;

import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpSession;
import java.util.Map;

@Controller
public class LoginController {
    
    @PostMapping("/login")
    public String login(@RequestParam("username") String username, @RequestParam("password") String password,
                        Map<String,String> map, HttpSession session) {
        if(!StringUtils.isEmpty(username)&&password.equals("123456")) {
        //登入成功
           session.setAttribute("username",username);
        return "redirect:dashboard.html";
        }   else {
        //登入失败
            map.put("errorMessage", "您的用户名或密码错误");
            return "index" ;
        }
    }

}

```

在webmvcConfigurer配置类配置拦截器及拦截路径

```java
package springboot.Config;


import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import springboot.Component.LoginHandlerInterceptor;
import springboot.Component.MyLocalResolver;

import java.util.Arrays;

/**通过调用WebMvcConfigurer接口，继承方法，来扩展SpringMVC
 * */
//@EnableWebMvc
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

//    @Override
//    public void addViewControllers(ViewControllerRegistry registry) {
//        registry.addViewController("/").setViewName("index");
//        registry.addViewController("index.html").setViewName("index");
//
//    }
    /**所有的WebMvcConfigurer都会被WebMvcConfigurerComposite中从容器中取出来，然后一起起作用*/
    @Bean
    public WebMvcConfigurer  webMvcConfigurer(){
        return new WebMvcConfigurer() {
            //SpringMvc扩展试图转换器
            @Override
            public void addViewControllers(ViewControllerRegistry registry) {
                    registry.addViewController("/").setViewName("index");
                    registry.addViewController("/index.html").setViewName("index");
                    registry.addViewController("/dashboard.html").setViewName("dashboard");
            }
            /**SpringMvc扩展拦截器
             *       SpringBoot已经做好了静态资源的映射，但是webjars等小细节还是要自己手动加入
             * */
            @Override
            public void addInterceptors(InterceptorRegistry registry) {
                registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").excludePathPatterns("/","/index.html","/login","/webjars/**","/css/**","/img/**");
            }
        };
    }
//    我们要添加一个localResolver，让自动配置的localResolver失效
    @Bean
    public LocaleResolver  localeResolver(){

        MyLocalResolver myLocalResolver = new MyLocalResolver();
        return myLocalResolver;
    }
}

```

**补充：**

/* 是拦截所有的文件夹，不包含子文件夹
/** 是拦截所有的文件夹及里面的子文件夹

 

相当于/*只有后面一级

/** 可以包含多级





###  5.Restful的CRUD

实验要求：

### 1.RestfulCRUD：CRUD满足Rest风格；

URI：  /资源名称/资源标识       HTTP请求方式区分对资源CRUD操作

|      | 普通CRUD（uri来区分操作） | RestfulCRUD       |
| ---- | ------------------------- | ----------------- |
| 查询 | getEmp                    | emp---GET         |
| 添加 | addEmp?xxx                | emp---POST        |
| 修改 | updateEmp?id=xxx&xxx=xx   | emp/{id}---PUT    |
| 删除 | deleteEmp?id=1            | emp/{id}---DELETE |

### 2.实验的请求架构;

| 实验功能                             | 请求URI | 请求方式 |
| ------------------------------------ | ------- | -------- |
| 查询所有员工                         | emps    | GET      |
| 查询某个员工(来到修改页面)           | emp/1   | GET      |
| 来到添加页面                         | emp     | GET      |
| 添加员工                             | emp     | POST     |
| 来到修改页面（查出员工进行信息回显） | emp/1   | GET      |
| 修改员工                             | emp     | PUT      |
| 删除员工                             | emp/1   | DELETE   |

### 3.员工列表

#### thymeleaf公共页面元素抽取

```html
1、抽取公共片段
<div th:fragment="copy">
&copy; 2011 The Good Thymes Virtual Grocery
</div>

2、引入公共片段
<div th:insert="~{footer :: copy}"></div>
~{templatename::selector}：模板名::选择器
~{templatename::fragmentname}:模板名::片段名

3、默认效果：
insert的公共片段在div标签中
如果使用th:insert等属性进行引入，可以不用写~{}：
行内写法可以加上：[[~{}]];[(~{})]；
```



三种引入公共片段的th属性：

**th:insert**：将公共片段整个插入到声明引入的元素中

**th:replace**：将声明引入的元素替换为公共片段

**th:include**：将被引入的片段的内容包含进这个标签中



```html
<footer th:fragment="copy">
&copy; 2011 The Good Thymes Virtual Grocery
</footer>

引入方式
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>

对应效果
<div>
    <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
</div>

<footer>
&copy; 2011 The Good Thymes Virtual Grocery
</footer>

<div>
&copy; 2011 The Good Thymes Virtual Grocery
</div>
```

也可以使用选择器，但是子选择的id 在你插入的时候要写成~{模板名::#抽取的id}

 ![image-20201028213024867](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201028213026.png)

引入片段的时候传入参数： 

```html
<nav class="col-md-2 d-none d-md-block bg-light sidebar" id="sidebar">
    <div class="sidebar-sticky">
        <ul class="nav flex-column">
            <li class="nav-item">
                <a class="nav-link active"
                   th:class="${activeUri=='main.html'?'nav-link active':'nav-link'}"
                   href="#" th:href="@{/main.html}">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-home">
                        <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
                        <polyline points="9 22 9 12 15 12 15 22"></polyline>
                    </svg>
                    Dashboard <span class="sr-only">(current)</span>
                </a>
            </li>

<!--引入侧边栏;传入参数-->
<div th:replace="commons/bar::#sidebar(activeUri='emps')"></div>
```

###  4.员工注册

添加页面

```html
<form>
    <div class="form-group">
        <label>LastName</label>
        <input type="text" class="form-control" placeholder="zhangsan">
    </div>
    <div class="form-group">
        <label>Email</label>
        <input type="email" class="form-control" placeholder="zhangsan@atguigu.com">
    </div>
    <div class="form-group">
        <label>Gender</label><br/>
        <div class="form-check form-check-inline">
            <input class="form-check-input" type="radio" name="gender"  value="1">
            <label class="form-check-label">男</label>
        </div>
        <div class="form-check form-check-inline">
            <input class="form-check-input" type="radio" name="gender"  value="0">
            <label class="form-check-label">女</label>
        </div>
    </div>
    <div class="form-group">
        <label>department</label>
        <select class="form-control">
            <option>1</option>
            <option>2</option>
            <option>3</option>
            <option>4</option>
            <option>5</option>
        </select>
    </div>
    <div class="form-group">
        <label>Birth</label>
        <input type="text" class="form-control" placeholder="zhangsan">
    </div>
    <button type="submit" class="btn btn-primary">添加</button>
</form>
```

**知识补充：**在Handler方法中，返回值为**视图名**只是通过模板引擎来视图映射，但是当你的返回值**"forward"+视图名**或**"redirect"+视图名**才是真正的把请求路径发个浏览器在经过SpringMVC处理

提交数据格式不对：生日的日期格式不同

例如：2017-12-12 ，2018/12/12，2017.12.12

日期格式化

### 5.CRUD-员工修改

修改添加二合一表单

```html
<!--需要区分是员工修改还是添加；-->
<form th:action="@{/emp}" method="post">
    <!--发送put请求修改员工数据-->
    <!--
1、SpringMVC中配置HiddenHttpMethodFilter;（SpringBoot自动配置好的）
2、页面创建一个post表单
3、创建一个input项，name="_method";值就是我们指定的请求方式
-->
    <input type="hidden" name="_method" value="put" th:if="${emp!=null}"/>
    <input type="hidden" name="id" th:if="${emp!=null}" th:value="${emp.id}">
    <div class="form-group">
        <label>LastName</label>
        <input name="lastName" type="text" class="form-control" placeholder="zhangsan" th:value="${emp!=null}?${emp.lastName}">
    </div>
    <div class="form-group">
        <label>Email</label>
        <input name="email" type="email" class="form-control" placeholder="zhangsan@atguigu.com" th:value="${emp!=null}?${emp.email}">
    </div>
    <div class="form-group">
        <label>Gender</label><br/>
        <div class="form-check form-check-inline">
            <input class="form-check-input" type="radio" name="gender" value="1" th:checked="${emp!=null}?${emp.gender==1}">
            <label class="form-check-label">男</label>
        </div>
        <div class="form-check form-check-inline">
            <input class="form-check-input" type="radio" name="gender" value="0" th:checked="${emp!=null}?${emp.gender==0}">
            <label class="form-check-label">女</label>
        </div>
    </div>
    <div class="form-group">
        <label>department</label>
        <!--提交的是部门的id-->
        <select class="form-control" name="department.id">
            <option th:selected="${emp!=null}?${dept.id == emp.department.id}" th:value="${dept.id}" th:each="dept:${depts}" th:text="${dept.departmentName}">1</option>
        </select>
    </div>
    <div class="form-group">
        <label>Birth</label>
        <input name="birth" type="text" class="form-control" placeholder="zhangsan" th:value="${emp!=null}?${#dates.format(emp.birth, 'yyyy-MM-dd HH:mm')}">
    </div>
    <button type="submit" class="btn btn-primary" th:text="${emp!=null}?'修改':'添加'">添加</button>
</form>
```

### 6.CRUD-员工删除

```html
<tr th:each="emp:${emps}">
    <td th:text="${emp.id}"></td>
    <td>[[${emp.lastName}]]</td>
    <td th:text="${emp.email}"></td>
    <td th:text="${emp.gender}==0?'女':'男'"></td>
    <td th:text="${emp.department.departmentName}"></td>
    <td th:text="${#dates.format(emp.birth, 'yyyy-MM-dd HH:mm')}"></td>
    <td>
        <a class="btn btn-sm btn-primary" th:href="@{/emp/}+${emp.id}">编辑</a>
        <button th:attr="del_uri=@{/emp/}+${emp.id}" class="btn btn-sm btn-danger deleteBtn">删除</button>
    </td>
</tr>


<script>
    $(".deleteBtn").click(function(){
        //删除当前员工的
        $("#deleteEmpForm").attr("action",$(this).attr("del_uri")).submit();
        return false;
    });
</script>
```





## 8.错误处理机制

### 1.SpringBoot默认的错误处理机制

#### 1.默认效果

   1.    返回一个默认的错误页面

         ![image-20210104142450911](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210104143017.png)

   2.    如果是其他客户端。默认响应一个JSON数据

         ![image-20210104143031028](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210104143031.png)

####  2.服务器通过什么来判断请求方式浏览器还是客户端？通过请求头不一样来判断

浏览器发送请求的请求头

![image-20210104152355424](/image-20210104152355424.png)

客户端发送请求的请求头

![image-20210104152441622](/image-20210104152441622.png)



#### 3.原理

##### 1.四个组件

参照ErrorMvcAutoConfiguration:错误处理的自动配置，ErrorMvcAutoConfiguration中 给容器增加了以下四个组件

   1.    **DefaultErrorAttributes**

   2.    **BasicErrorController：处理默认的/error请求**

         ```java
         @Controller
         @RequestMapping("${server.error.path:${error.path:/error}}")
         /*
            处理/error请求,如自定义了，则处理/error.path否则默认处理/error
         **/
         public class BasicErrorController extends AbstractErrorController {
         
         	private final ErrorProperties errorProperties;
             
             
         	@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)//像上文提到过的，当请求头Accept=text/html的时候，会进入这个方法，产生html数据，方法返回值为modelandview,
         	public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
         		HttpStatus status = getStatus(request);
         		Map<String, Object> model = Collections
         				.unmodifiableMap(getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
         		response.setStatus(status.value());
                 //去哪个页面作为错误页面；包含页面地址和页面内容
         		ModelAndView modelAndView = resolveErrorView(request, response, status, model);
         		return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
         	}
         
         	@RequestMapping
         	public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {//像上文提到过得，当请求Accept=*/*的时候，进入到这个方法，返回JSON数据，方法的返回值为ResponseEntity
         		HttpStatus status = getStatus(request);
         		if (status == HttpStatus.NO_CONTENT) {
         			return new ResponseEntity<>(status);
         		}
         		Map<String, Object> body = getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.ALL));
         		return new ResponseEntity<>(body, status);
         	}
         ```

         

   3.    **ErrorPageCustomizer：定制错误规则**

         ```java
         public class ErrorProperties {
         
         	/**
         	 * 系统出现错误以后来到error请求进行处理；就好像web.xml注册的错误页面规则
         	 */
         	@Value("${error.path:/error}")
         	private String path = "/error";
         ```

         

   4.    **DefaultErrorViewResolver**

         ```java
         public class DefaultErrorViewResolver implements ErrorViewResolver, Ordered {
         
            private static final Map<Series, String> SERIES_VIEWS;
         
            static {
               Map<Series, String> views = new EnumMap<>(Series.class);
               views.put(Series.CLIENT_ERROR, "4xx");
               views.put(Series.SERVER_ERROR, "5xx");
               SERIES_VIEWS = Collections.unmodifiableMap(views);
            }
             
             
         	private ModelAndView resolve(String viewName, Map<String, Object> model) {
                  //默认SpringBoot可以去找到一个页面error/404
         		String errorViewName = "error/" + viewName;
                  //模板引擎可以解析这个页面得知就用模板引擎解析        
         		TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName,
         				this.applicationContext);
         		if (provider != null) {
                      //模板引擎可用的情况下返回到errorViewName指定的视图地址
         			return new ModelAndView(errorViewName, model);
         		}
                 //模板引擎不可以用，则返回 resolveResource(errorViewName, model);
         		return resolveResource(errorViewName, model);
         	}
             
             private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
                 //表示从静态资源下来寻找对应errorViewName的页面       
         		for (String location : this.resources.getStaticLocations()) {
         			try {
         				Resource resource = this.applicationContext.getResource(location);
         				resource = resource.createRelative(viewName + ".html");
         				if (resource.exists()) {
         					return new ModelAndView(new HtmlResourceView(resource), model);
         				}
         			}
         			catch (Exception ex) {
         			}
         		}
         		return null;
         	}
             
         ```

##### 原理实现步骤

​		一旦系统出现`4xx`或者`5xx`之类的错误；errorPageCustomizer就会生效（定制错误响应规则）

![image-20210104144922848](/image-20210104144922848.png)

​          然后会被BasicErrorController处理，根据响应头Accept的值不同来进入不同的方法

​          1.响应页面：去哪个页面是由DefaultErrorViewResolver

​      ![image-20210104154008139](/image-20210104154008139.png)
​    

```java
private ModelAndView resolve(String viewName, Map<String, Object> model) {
     //默认SpringBoot可以去找到一个页面error/404,
	String errorViewName = "error/" + viewName;
     //模板引擎可以解析这个页面得知就用模板引擎解析        
	TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName,
			this.applicationContext);
	if (provider != null) {
         //模板引擎可用的情况下返回到errorViewName指定的视图地址
		return new ModelAndView(errorViewName, model);
	}
    //模板引擎不可以用，则返回 resolveResource(errorViewName, model);
	return resolveResource(errorViewName, model);
}

private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
    //表示从静态资源下来寻找对应errorViewName的页面       
	for (String location : this.resources.getStaticLocations()) {
		try {
			Resource resource = this.applicationContext.getResource(location);
			resource = resource.createRelative(viewName + ".html");
			if (resource.exists()) {
				return new ModelAndView(new HtmlResourceView(resource), model);
			}
		}
		catch (Exception ex) {
		}
	}
	return null;
}
```
### 2.定制错误响应

1. 如何定制错误的页面

   1. 有模板引擎：从上文可知在模板引擎的路径下error/状态码,也就是说，将错误页面命名为 **错误状态码.html**放在error下，SpringBoot会通过错误的状态码来找打到对应的文件

      ![image-20210104190000553](/image-20210104190000553.png)

      我们还可以视使用4xx和5xx的方式作为错误页面，来匹配这种类型的所有错误，当然精确优先（404会去跳转404.html）

      页面还可以取出：

      ​     timestamp：时间戳

      ​     status：状态码

      ​	 error：错误提示

      ​     exception：异常对象

      ​	 message： 异常消息

      ​     errors：JSR303数据校验的错误都在这里

      

   2. 无模板引擎（模板引擎找不到这个错误页面），静态资源文件夹下找，可以访问到，但是没有像上文一样异常的回显

      1. 举个例子：
         1. ![image-20210104195155135](/image-20210104195155135.png)
         2. ![image-20210104195543388](/image-20210104195543388.png)

   3. 默认以上两种情况都没有，默认来到SpringBoot的错误提示页面（就是那个大白页面）

2. 如何定制错误的JSON数据

   1. 写死的

      1. 自定义异常处理和返回定制json数据

      ```java
      package springboot.ExceptionHandler;
      
      
      import org.springframework.web.bind.annotation.ControllerAdvice;
      import org.springframework.web.bind.annotation.ExceptionHandler;
      import org.springframework.web.bind.annotation.ResponseBody;
      import springboot.Exception.UserNotExistException;
      
      import java.util.HashMap;
      import java.util.Map;
      
      @ControllerAdvice
      public class MyExceptionHandler {
      
      
          @ExceptionHandler(UserNotExistException.class)
          @ResponseBody
          public Map hadnleUserNotExistException(Exception e){
              //参数  Exception e 可以获取异常信息   
              Map map=new HashMap<>();
              map.put("code", "user.notexitst");
              map.put("message", e.getMessage());
              return map;
          }
      }
      ```

      2. 运行效果![image-20210105160333560](/image-20210105160333560.png)

   2. 自适应

      1. 代码

         ```java
         @ControllerAdvice
         public class MyExceptionHandler {
         
         
         //    @ExceptionHandler(UserNotExistException.class)
         //    //这样其实不好，是手动写好你的浏览器和客户端都返回json的，我们需要自适应
         //    @ResponseBody
         //    public Map hadnleUserNotExistException(Exception e){
         //
         //        Map map=new HashMap<>();
         //        map.put("code", "user.notexitst");
         //        map.put("message", e.getMessage());
         //        return map;
         //    }
         
         //   自适应
             @ExceptionHandler(UserNotExistException.class)
             public String hadnleUserNotExistException(Exception e){
         
                 Map map=new HashMap<>();
                 map.put("code", "user.notexitst");
                 map.put("message", e.getMessage());
                 //转发到error请求,也就是说BasicErrorController会拦截到  "${server.error.path:${error.path:/error}}" 请求
                 return "forward:/error";
             }
         }
         ```

      2. 客户端和浏览器的反映效果是自适应的

         ![image-20210105163154457](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210108212550.png)

      3. ```java
         package springboot.ExceptionHandler;
         
         
         import org.springframework.web.bind.annotation.ControllerAdvice;
         import org.springframework.web.bind.annotation.ExceptionHandler;
         import org.springframework.web.bind.annotation.ResponseBody;
         import springboot.Exception.UserNotExistException;
         
         import javax.servlet.http.HttpServletRequest;
         import java.util.HashMap;
         import java.util.Map;
         
         @ControllerAdvice
         public class MyExceptionHandler {
         
         
         //    @ExceptionHandler(UserNotExistException.class)
         //    //这样其实不好，是手动写好你的浏览器和客户端都返回json的，我们需要自适应
         //    @ResponseBody
         //    public Map hadnleUserNotExistException(Exception e){
         //
         //        Map map=new HashMap<>();
         //        map.put("code", "user.notexitst");
         //        map.put("message", e.getMessage());
         //        return map;
         //    }
         
         //   自适应
             @ExceptionHandler(UserNotExistException.class)
             public String hadnleUserNotExistException(Exception e, HttpServletRequest request){
         
                 Map map=new HashMap<>();
                 map.put("code", "user.notexitst");
                 map.put("message", "用户信息不对");
                 //转发到error请求,也就是说BasicErrorController会拦截到  "${server.error.path:${error.path:/error}}" 请求
                 //BasicErrorController会处理4xx和5xx的异常代码，我们做自己定义状态码
                 request.setAttribute("javax.servlet.error.status_code",500);
                 return "forward:/error";
             }
         }
         ```

3. 将我们的定制数据携带出去；

   出现错误以后，会来到/error请求，会被BasicErrorController处理，响应出去可以获取的数据是由getErrorAttributes得到的（是AbstractErrorController（ErrorController）规定的方法）；

   ​	1、完全来编写一个ErrorController的实现类【或者是编写AbstractErrorController的子类】，放在容器中；

   ​	2、页面上能用的数据，或者是json返回能用的数据都是通过errorAttributes.getErrorAttributes得到；

   ​			容器中DefaultErrorAttributes.getErrorAttributes()；默认进行数据处理的；

   自定义ErrorAttributes

   ```java
   //给容器中加入我们自己定义的ErrorAttributes
   @Component
   public class MyErrorAttributes extends DefaultErrorAttributes {
   
       @Override
       public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
           Map<String, Object> map = super.getErrorAttributes(requestAttributes, includeStackTrace);
           map.put("company","atguigu");
           return map;
       }
   }
   ```

   最终的效果：响应是自适应的，可以通过定制ErrorAttributes改变需要返回的内容，

   ![](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210108212536.png)

   当我们需要传入自定义的json数据，我们可以将数据传入req、session等域中

   ![image-20210108213226019](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210108213226.png)
   
   通过最后webRequest.getAttribute(参数名,对应Scope的数字);
   
   ![image-20210108212929733](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210108212930.png)
   
   ![image-20210108213720157](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20210108213721.png)







##  9.配置嵌入式Servlet容器

SpringBoot默认使用的是嵌入式的Servlet容器(Tomcat)

通过Pom文件可知道依赖关系，tom通过嵌入式的方法是作为SpringBoot的servlet容器

![image-20210109154944552](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210109154944552.png)

### 1.嵌入式容器的配置

1. 如何定制和修改Servlet容器的相关配置

   1. 修改和Server有关的配置（上文我们讲过可以通过修改ServerProperties类中对应在配置文件中的属性的值）

      1. ![image-20210109160940106](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210109160940106.png)

      ```properties
      server.port=6666
      server.servlet.context-path=WEB
      
      # 通用的Servlet容器设置
      server.xxx
      
      # Tomcat的设置
      server.tomcat.accept-count=
      server.tomcat.accesslog.ipv6-canonical=true
      server.tomcat.accesslog.locale=
      
      ```

   2. 编写一个WebServerFactoryCustomizer<ConfigurableWebServerFactory>（嵌入式Servlet容器定制器）来修改Servlet容器配置

      1. ```java
         @Configuration
         public class MyMvcConfig implements WebMvcConfigurer {
         
             @Bean
             public WebServerFactoryCustomizer<ConfigurableWebServerFactory> webServerFactoryCustomizer(){
                 /**
                  * public interface WebServerFactoryCustomizer<T extends WebServerFactory> {
                  *             void customize(T fa ctory);
                  *ConfigurableWebServerFactory继承自WebServerFactory，所以写成这种方式
                  * */
                 return new WebServerFactoryCustomizer<ConfigurableWebServerFactory>() {
                     //定制 嵌入式的servlet容器的相关规则
                     @Override
                     public void customize(ConfigurableWebServerFactory factory) {
                           factory.setPort(8081);
                     }
                 };
         
         
             }
         ```

2. SpringBoot能不能修改支持其他的Servlet容器



### 2.注册三大组件

由于SpringBoot默认是以jar包的方式启动嵌入式的Servlet容器来启动SpringBoot的web应用,我们没有传统web那样的结构，也就是说没有web.xml,没法注册Servlet,Filter,Listener这些组件

SpringBoot提供了一些类来注册组件,下方↓

![image-20210109170654855](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210109170654855.png)



#### Servlet

```java
package springboot.Servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class MyServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("My name is MyServlet");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      doPost(req, resp);
    }
}
```

![image-20210109194726815](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210109194726815.png)



#### Filter

```java
package Filters;

import javax.servlet.*;
import java.io.IOException;

public class MyFilter implements Filter {


    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("MyFilter_doFilter");
        //放行Filter
        chain.doFilter(request, response);
    }

}
```

![image-20210109200102251](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210109200102251.png)



#### Listener

```java
package springboot.Listeners;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("web开始了");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("web结束了");
    }
}
```

SpringBoot帮我们自动配置SpringMVC的时候,自动注册了SpringMVC的前端控制器：DispatcherServlet

```java
@Bean(name = DEFAULT_DISPATCHER_SERVLET_REGISTRATION_BEAN_NAME)
@ConditionalOnBean(value = DispatcherServlet.class, name = DEFAULT_DISPATCHER_SERVLET_BEAN_NAME)
public DispatcherServletRegistrationBean dispatcherServletRegistration(DispatcherServlet dispatcherServlet,
      WebMvcProperties webMvcProperties, ObjectProvider<MultipartConfigElement> multipartConfig) {
    /**下面就是注册到SpringBoot，构造函数中的参数webMvcProperties.getServlet().getPath()的值就是"/"，表示拦截所有的路径
    包括静态资源,但是不拦截JSP请求；/*会拦截jsp请求,可以通过webMvcProperties中的包含属性path可以知道可以在配置文件中配置spring.mvc.servlet.path
    配置拦截路径
    */
   DispatcherServletRegistrationBean registration = new DispatcherServletRegistrationBean(dispatcherServlet,
         webMvcProperties.getServlet().getPath());
   registration.setName(DEFAULT_DISPATCHER_SERVLET_BEAN_NAME);
   registration.setLoadOnStartup(webMvcProperties.getServlet().getLoadOnStartup());
   multipartConfig.ifAvailable(registration::setMultipartConfig);
   return registration;
}
```



### 3.嵌入式服务器的变更

SpringBoot默认提供了Undertow、Tomcat、Jetty

1. 我们能适应嵌入式的Tomcat因为他在spring-boot-starter-web引入了关于tomcat的依赖，所以如果我们如果使用其他的嵌入式Servlet容器，要先去排除这个依赖

   ![image-20210110124332917](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210110124332917.png)

2. 引入其他的Servlet容器

   ![image-20210110131708077](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210110131708077.png)

3. 运行结果

   ![image-20210110131728211](/C:/Users/lenovo/AppData/Roaming/Typora/typora-user-images/image-20210110131728211.png)