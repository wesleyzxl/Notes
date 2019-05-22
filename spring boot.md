## spring boot入门

J2EE一站式解决方案

优点：

- 快速创建独立运行的spring项目以及与主流框架集成
- 使用嵌入式的servlet容器，用用无需打成war包，打成jar包即可
- starters（启动器）自动以来与版本控制
- 大量的自动配置，简化开发，也可修改默认值
- 无需配置xml，五代码生成，开箱即用
- 准生产环境的运行时应用监控
- 与云计算的天然集成

### 微服务

2014, Martin Fowler

微服务：架构风格

一个应用应该是一组小型服务，可以通过http的方式进行互通

每一个功能元素最终都是一个可独立替换和独立升级的软件单元

### 入门

#### maven设置

```xml
<profile>
            <id>jdk-1.8</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
            </activation>
            <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
            </properties>
        </profile>
```

#### spring boot hello world

功能：浏览器发送hello请求，服务器接受请求并处理，响应hello world字符串

编写主程序

```java
package com.hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // spring应用启动
        SpringApplication.run(HelloWorldMainApplication.class, args);
    }
}
```

编写相关的Controller、Service

```java
package com.hello.Controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello() {
        return "Hello World";
    }
}
```

运行主程序启动

简化部署，在pom.xml中配置maven插件

```java
	<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

启动在maven的lifecycle中的package程序会生成jar文件，使用java -jar命令会启动web应用

1. pom.xml文件

   1. 父项目

   ```xml
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>2.1.4.RELEASE</version>
       <relativePath/> <!-- lookup parent from repository -->
   </parent>
   
   <!-- 依赖的父项目中还依赖另一个父项目 -->
   <parent>
   	<groupId>org.springframework.boot</groupId>
   	<artifactId>spring-boot-dependencies</artifactId>
   	<version>2.1.4.RELEASE</version>
   	<relativePath>../../spring-boot-dependencies</relativePath>
   </parent>
   
   <!-- spring-boot-dependencies 中有properties标签，它管理了各种依赖的版本 -->
   ```

   在以后导入依赖时不需要写版本，没有在它管理范围的仍然需要写版本号
   2. 导入的依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   ```

   spring-boot-starter：spring-boot场景启动器；spring-boot-starter-web导入了web模块正常运行所依赖的组件

   spring boot将所有的功能场景都抽取出来，做成一个starter（启动器），只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器

2. 主程序类，主入口类

   @**SpringBootApplication**: Spring Boot应用标注在某个类上说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用

   该注解中的标有其他注解

   ```java
   @Target(ElementType.TYPE)
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   @Inherited
   @SpringBootConfiguration
   @EnableAutoConfiguration
   @ComponentScan(excludeFilters = {
   		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
   		@Filter(type = FilterType.CUSTOM,
   				classes = AutoConfigurationExcludeFilter.class) })
   ```

   @SpringBootApplication中又有注解

   1. @**SpringBootConfiguration**，@SpringBootConfiguration标注在某个类上说明它是SpringBoot的配置类。而@SpringBootConfiguration中又有@*Configuration*注解标注，@Configuration标注的类为spring的配置类，配置类也是容器的一个组件，@Component

   2. @**EnableAutoConfiguration**: 开启Spring Boot自动配置。该注解类中的注解有：

      ```java
      @Target(ElementType.TYPE)
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      @Inherited
      @AutoConfigurationPackage
      @Import(AutoConfigurationImportSelector.class)
      ```

      @*AutoConfigurationPackage*: 自动配置包





使用spring initializer快速创建Spring Boot项目，其中resources文件中的目录结构

```
resources
	static
	templates
	application.properties
```

- static中方静态资源
- templates中放所有的模板页面（Spring Boot默认jar包使用嵌入式的Tomcat，默认不支持jsp页面），可以使用freemarker，thymeleaf等模板引擎。
- application.properties: spring boot应用的配置文件。可以修改一些默认配置



## Spring Boot 配置

### 配置文件

spring boot使用一个全局的配置文件，配置文件的名字是固定的

- application.properties
- application.yml

配置文件的作用为修改spring boot自动配置的默认值



YAML(YAML Ain't Markup Language)

markup language: 标记语言

yml是YAML语言的文件，以数据为中心，比json、xml等更适合作配置文件

yml配置实例

```yaml
server:
  port: 8080
```

xml配置

```xml
<server>
	<port>8080</port>
</server>
```

#### YAML语法

1. 基本语法

   k:**[空格]**v ：表示一对键值对。

   以空格的缩进来控制层级关系，只要是左对齐的一类数据，都是属于同一个层级

   属性和值是大小写敏感的

2. 值的写法

   字面量：普通的值（数字，字符串，布尔）

   > 字符串默认不用加上单引号或者双引号
   >
   > ""：双引号不会转义字符，例如
   >
   > ​		name: "jane\nwesley"
   >
   > ​		输出为jane
   >
   > ​					wesley
   >
   > ''：单引号会转义字符，特殊字符最终只是一个普通的字符串，不会转义。如上面的例子中\n不会换行

   

   对象、Map（属性和值）：即键值对

   > k: v：在下一行来写对象的属性和值的关系，但注意缩进
   >
   > ​	对象还是k: v的方式，例如

   ```yaml
   friend: 
       lastName: zhangsan
       age: 23
   ```

   ```yaml
   friend: {lastName: zhangsan, age: 23}
   ```

   

   数组（包括List、Set）

   用- 表示数组中的一个元素

   ```yaml
   pets:
    - cat
    - dog
    - pig
   ```

   ```yaml
   pets: [cat, dog, pig]
   ```

   

#### 配置文件值注入

1. 通过yaml配置文件

```yaml
person:
    lastName: hello
    age: 23
    boss: false
    birth: 2010/02/03
    maps: {k1: v1, k2: 25}
    lists:
    - lisi
    - zhaoliu
    dog:
    name: 狗
    age: 3
```

javaBean

```java
package com.example.demo.entity;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;

/**
* 将配置文件中配置的每一个属性的值，映射到这个组件中
*
* @ConfigurationProperties: 告诉spring boot将本类中的所有属性和配置文件中相关的配置进行绑定
* prefix = "person" : 配置文件中哪个下面的所有属性进行一一映射
*
* @Component 只有这个组件时容器中的组件，才能使用容器的@ConfigurationProperties功能
*/

@ConfigurationProperties(prefix = "person")
@Component
public class Person {

    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
    
    // toString, setter, getter
}
```

```java
package com.example.demo.entity;

public class Dog {

    private String name;
    private Integer age;

    // toString, setter, getter
}
```

在pom.xml中导入配置文件处理器，之后白那些配置就会有提示

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

2. 在用properties文件配置时需要注意**编码**问题，properties默认使用ascii码，所以需要在ide中设置Transparent native-to-ascii conversion

```properties
person.last-name=张
person.age=18
person.birth=2010/03/02
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a, b, c
person.dog.name=dog
person.dog.age=15
```



3. 除了通过@ConfigurationProperties注解外，还可以通过Spring的注解@Value

```java
/**
* @Value 相当于在xml文件中配置
* <bean class="Person">
*     <properties name="lastName" value="[字面量（可以接收布尔值转义符等）]/${key}（从环境变量、配置文件中获取值）"/#{SpEL}/>    
* </bean>
*/

@Component
public class Person{
    @Value("${person.last-name}")
    private String lastName;
    
    // ...
}
```

​	

|                       | @ConfigurationProperties | @Value   |
| --------------------- | ------------------------ | -------- |
| 功能                  | 批量主入配置文件中的属性 | 逐个指定 |
| Relaxed binding       | 支持                     | 不支持   |
| SpEL                  | 不支持                   | 支持     |
| JSR303数据校验        | 支持                     | 不支持   |
| 复杂类型封装（如map） | 支持                     | 不支持   |

Relaxed binding：例如lastName属性在配置文件中可以写为last-name, lastName, last_name, LAST_NAME进行配置

- 如果只是在某个业务逻辑中需要获取一下配置文件中的某项值使用
- 如果专门写了一个JavaBean来和配置文件进行映射，直接使用@ConfigurationProperties

4. JSR303数据校验

    ```java
    @ConfigurationProperties(prefix = "person")
    @Component
    @Validated
    public class Person {
        
        @Email
        private String email;
        
        // ...
    }
    ```

    可以通过@Validated ...

5. @PropertySource & @ImportResource

   - @**PropertySource**可以指定加载指定的配置文件，相对的@ConfigurationProperties为加载全局配置文件

   ```java
   @PropertySource(value = {"classpath:person.properties"})
   @Component
   public class Person {
       // ...
   }
   ```

   - @**ImportResource**导入Spring的配置文件，让配置文件中的内容生效

     Spring Boot中没有Spring的配置文件，如果手动白那些配置文件不能识别，想让Spring配置文件生效，需要在Spring Boot的主程序上配置@ImportResource

     ```java
     package com.example.demo;
     
     import org.springframework.boot.SpringApplication;
     import org.springframework.boot.autoconfigure.SpringBootApplication;
     import org.springframework.context.annotation.ImportResource;
     
     @ImportResource(locations = {"classpath:bean.xml"})
     @SpringBootApplication
     public class DemoApplication {
     
         public static void main(String[] args) {
             SpringApplication.run(DemoApplication.class, args);
         }
     
     }
     ```

   spring boot不推荐以配置文件的方式给容器添加组件，而推荐使用全注解的方式

   例如需要添加HelloService组件

   ```java
   package com.example.demo.service;
   
   public class HelloService {
   }
   ```

   单独创建一个类添加组件，使用@Bean和@Configuration

   ```java
   package com.example.demo.config;
   
   import com.example.demo.service.HelloService;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   
   @Configuration
   public class MyAppConfig {
   
       // 组件默认的id为方法名
       @Bean
       public HelloService helloService() {
           return new HelloService();
       }
   }
   ```

   

#### 配置文件占位符

1. 随机数

   ```java
   ${random.value}
   ${random.int}
   ${random.long}
   ${random.int(10)}
   ${random.int[1024,65536]}
   ```

2. 占位符获取之前配置的值，如果没有可以使用：指定默认值

   ```properties
   app.name=MyAPP
   app.description=${app.name} is a Spring Boot application
   ```

   ${app.name:默认值}来指定找不到属性时的默认值

#### Profile

1. 多Profile文件

   在主配置文件编写的时候，文件名可以是application-{profile}.properties/.yml

   例如配置application-dev.properties application-prod.properties 

   Spring Boot默认使用application.properties

2. yml支持多文档方式

   ```yaml
   server:
     port: 8080
   spring:
     profiles:
       active: dev
   ---
   server:
     port: 8081
   spring:
     profiles: dev
   ---
   server:
     port: 80
   spring:
     profiles: prod
   ```

   通过yaml的多文档块的方法，在最上面的文档块中指定是哪个文档块生效，默认为最上面的文档块生效

3. 激活指定profile

   - 在主配置文件application.properties中指定spring.profile.active=dev就可以指定配置文件为application-dev.properties

   - 命令行：

     --spring.profiles.active=dev

     测试时在IDEA中的Run/Debug Configurations中的Spring Boot主程序的Configuration中配置Program arguments为--spring.profiles.active=dev即可

     也可以在项目打包成jar文件时用java -jar [jar文件名] --spring.profiles.active=dev

   - 虚拟机参数

     -Dspring.profile.active=dev

     在测试时，IDEA的VM options指定-Dspring.profile.active=dev

#### 配置文件加载位置

spring boot启动会扫描以下位置的application.properties或者application.yml文件作为Spring Boot的默认配置文件

- file:./config/
- file:./
- classpath:/config/
- classpath:/

以上时按照优先级从高到低的顺序，所有位置的文件都会被加载，**互补配置**，高优先级配置内容会覆盖低优先级配置内容

类路径classpath:/即为resources文件夹，file:./即为该项目的根目录

也可以通过配置spring.config.location来改变默认配置。项目打包好后，可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置

```
java -jar xxx.jar --spring.config.location=d:/....
```

#### 外部配置加载顺序

Spring Boot也可以从一下位置加载配置，优先级从高到底；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置

1. 命令行参数

   java -jar xxx.jar --server.port=8087 --server.context.path=/abc

2. 来自java:comp/env的JNDI属性

3. Java系统属性（System.getProperties()）

4. 操作系统环境变量

5. RandomValuePropertySource配置的random.*属性值

6. jar包外部的application-{profile}.properties或application.yml（带spring.profile）配置文件

7. jar包内部的application-{profile}.properties或application.yml（带spring.profile）配置文件

8. jar包外部的application.properties或application.yml（不带spring.profile）配置文件

9. jar包内部的application.properties或application.yml（不带spring.profile）配置文件

10. @Configuraion注解类上的@PropertySource

11. 通过SpringApplication.setDefaultProperties指定的默认属性



**6-9 由jar包外向jar包内进行寻找，优先加载带profile**

jar包外即为打包后的jar文件外面配置一个application.properties文件，然后通过命令行启动jar包即可使用外部配置文件

#### 自动配置原理

1. Spring Boot启动时，加载主配置类，@SpringBootApplication中开启了自动配置@EnableAutoConfiguration

2. @EnableAutoConfiguration作用

   利用@SpringBootApplication中的@Import(EnableAutoConfigurationImportSelector.class)给容器导入一些组件





## Spring Boot日志

常见的日志框架JUL, JCL Jboss-logging, logback, log4j, slf4j

| 日志门面（日志的抽象层）                                     | 日志实现                                       |
| ------------------------------------------------------------ | ---------------------------------------------- |
| ~~JCL(Jakarta Commons Logging)~~, slf4j(Simple Logging Fcade for Java), ~~Jboss-logging~~ | Log4j, JUL(Java.util.logging), log4j2, Logback |

从左边选一个门面（抽象层），右边选择一个实现

- 日志门面：slf4j

- 日志实现：Logback

Spring Boot底层时spring框架，spring框架默认使用JCL；SpringBoot选择slf4j和Logback

### slf4j使用

开发时日志记录方法的调用应该调用日志抽象层里面的方法，而不是调用日志的实现类

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

每一个日志的实现框架都有自己的配置文件，使用slf4j以后，**配置文件还是用日志实现框架本身的配置文件**

![](https://www.slf4j.org/images/concrete-bindings.png)

在系统中使用到其他框架时，其他框架也有自己的日志实现框架支持，为了统一日志记录，即使是别的框架也需要统一使用slf4j进行输出

![](https://www.slf4j.org/images/legacy.png)

如何让系统中所哟日志都统一到slf4j

1. 将系统中其他日志框架先排除出去
2. 用中间包来替换原有的日志框架
3. 导入slf4j其他的实现



### spring boot日志依赖关系

Spring Boot使用这个来做日志功能

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
    <version>2.1.4.RELEASE</version>
    <scope>compile</scope>
</dependency>
```

底层依赖关系

![](https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring Boot/Snipaste_2019-05-16_13-17-47.png)

总结

- Spring Boot底层也是使用slf4j+logback的方式进行日志记录
- Spring Boot也把其他的日志都替换成了slf4j
- 通过中间转换包
- 如果引入其他框架，一定要把这个框架的默认日志依赖移除掉

Spring Boot能自动适配所有的日志，且底层使用slf4j+logback的方式记录日志，引入其他框架的时候只需要把这个框架依赖的日志框架排除掉

### 日志的使用

1. 默认配置

   ```java
   package com.example.demo;
   
   import org.junit.Test;
   import org.junit.runner.RunWith;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.boot.test.context.SpringBootTest;
   import org.springframework.test.context.junit4.SpringRunner;
   
   @RunWith(SpringRunner.class)
   @SpringBootTest
   public class DemoApplicationTests {
       Logger logger = LoggerFactory.getLogger(getClass());
   
       @Test
       public void testLog() {
   
           // 日志的级别由低到高
           // 可以调整输出日志级别，日志只会在这个级别以及更高级别的输出
           logger.trace("this is trance log");
           logger.debug("this is debug log");
           logger.info("info log");
           logger.warn("warning log");
           logger.error("error log");
       }
   
   }
   ```

   ![https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring%20Boot/Snipaste_2019-05-20_14-34-02.png](https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring Boot/Snipaste_2019-05-20_14-34-02.png)

   spring boot 默认使用的info级别，可以在配置文件中配置指定包或者类的日志级别，如果没有指定级别就使用spirngboot默认规定的的级别：root级别

2. 修改默认配置

   ```properties
   # 修改日志级别，可以指定包或者类
   logging.level.com.example.demo=trace
   
   # 在当前磁盘的根目录下创建该文件夹并生成spring.log日志文件
   logging.path=/spring/log
   
   # 可以指定日志文件名，如果没有路径则会创建在项目的根目录下，也可以指定路径加文件名
   # logging.path 和 logging.file只选择一个
   logging.file=springboot.log
   ```

   修改日志的格式

   ```properties
   # 控制台日志输出格式
   logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
   
   # 指定文件中日志输出格式
   logging.pattern.file=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
   ```

   日志输出格式中

   ```html
   <!--
   	%d 代表日期时间
   	%thread 代表线程名
   	%-5level 级别从左显示5个字符宽度
   	%logger{50} 表示logger名字最长50个字符，否则按照句点分割
   	%msg 日志消息
   	%n 换行符
   -->
   ```

   | logging.file | logging.path | Example  | Description                      |
   | ------------ | ------------ | -------- | -------------------------------- |
   | (none)       | (none)       |          | 只在控制台输出                   |
   | 指定文件名   | (none)       | my.log   | 输出日志到my.log                 |
   | (none)       | 指定目录     | /var/log | 输出到指定目录的spring.log文件中 |

3. 指定配置

   其他框架中有其他的日志框架时，给类路径下放上每个日志框架自己的配置文件即可

   | Logging System          | Customization                                                |
   | ----------------------- | ------------------------------------------------------------ |
   | Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, or `logback.groovy` |
   | Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
   | JDK (Java Util Logging) | `logging.properties`                                         |

   更推荐用logback-spring.xml而不是logback.xml，logback.xml直接被日志框架识别，而logback-spring.xml不会被日志框架直接加载，由spring boot解析日志配置，可以使用spring boot的profile功能。

   在logback-spring.xml配置文件中使用springProfile标签实现profile功能

   ```xml
   <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
   	<layout class="ch.qos.logback.classic.PatternLayout">
       	<springProfile name="dev">
           	<pattern>%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n</pattern>
           </springProfile>
           <!-- 在不是dev配置下使用该日志配置 -->
           <springProfile name="!dev">
           	<pattern>%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n</pattern>
           </springProfile>
       </layout>
   </appender>
   ```

   log4j2也是类似的方法



## Spring Boot的Web开发





### spring boot对静态资源的映射规则

WebMvcAutoConfiguration类中的内部类WebMvcAutoConfigurationAdapter中有方法

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
    CacheControl cacheControl = this.resourceProperties.getCache()
        .getCachecontrol().toHttpCacheControl();
    if (!registry.hasMappingForPattern("/webjars/**")) {
        customizeResourceHandlerRegistration(registry
                                             .addResourceHandler("/webjars/**")
                                             .addResourceLocations("classpath:/META-INF/resources/webjars/")
                                             .setCachePeriod(getSeconds(cachePeriod))
                                             .setCacheControl(cacheControl));
    }
    String staticPathPattern = this.mvcProperties.getStaticPathPattern();
    if (!registry.hasMappingForPattern(staticPathPattern)) {
        customizeResourceHandlerRegistration(
            registry.addResourceHandler(staticPathPattern)
            .addResourceLocations(getResourceLocations(
                this.resourceProperties.getStaticLocations()))
            .setCachePeriod(getSeconds(cachePeriod))
            .setCacheControl(cacheControl));
    }
}
```

1. "webjars"：以jar包的方式引入静态资源

   所有 /webjars/** ，都去 classpath:/META-INF/resources/webjars/ 找资源

   以jQuery为例，在[webjars](<https://www.webjars.org/>)中找到并导入maven依赖

   ![https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring%20Boot/Snipaste_2019-05-21_02-25-43.png](https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring Boot/Snipaste_2019-05-21_02-25-43.png)

   所以调用时从webjars目录下指定位置即可

   WebMvcAutoConfiguration中成员变量
   
   ```java
    // 在ResourceProperties类中可以配置静态资源相关的参数，如缓存时间等
    private final ResourceProperties resourceProperties;
   ```

2. "/**" 

   访问当前项目的任何资源

   从上面addResourceHandlers方法中可知路径从ResourceProperties类中配置

   ```java
   @ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
   public class ResourceProperties {
   	private static final String[] CLASSPATH_RESOURCE_LOCATIONS = {
                   "classpath:/META-INF/resources/", "classpath:/resources/",
                   "classpath:/static/", "classpath:/public/" };
   
       private String[] staticLocations = CLASSPATH_RESOURCE_LOCATIONS;
   
       public String[] getStaticLocations() {
               return this.staticLocations;
   	}
       
       //...
   }
   ```

   静态资源的文件夹

   - "classpath:/META-INF/resources/", 
   - "classpath:/resources/",
   - "classpath:/static/", 
   - "classpath:/public/" 

3. 额外

   在WebMvcAutoConfiguration类中的内部类WebMvcAutoConfigurationAdapter中有另外两个方法

   ```java
   // 配置网站首页
   @Bean
   public WelcomePageHandlerMapping welcomePageHandlerMapping(
       ApplicationContext applicationContext) {
       return new WelcomePageHandlerMapping(
           new TemplateAvailabilityProviders(applicationContext),
           applicationContext, getWelcomePage(),
           this.mvcProperties.getStaticPathPattern());
   }
   
   // 配置网页图标
   @Configuration
   @ConditionalOnProperty(value = "spring.mvc.favicon.enabled",
                          matchIfMissing = true)
   public static class FaviconConfiguration implements ResourceLoaderAware {
   
       private final ResourceProperties resourceProperties;
   
       private ResourceLoader resourceLoader;
   
       public FaviconConfiguration(ResourceProperties resourceProperties) {
           this.resourceProperties = resourceProperties;
       }
   
       @Override
       public void setResourceLoader(ResourceLoader resourceLoader) {
           this.resourceLoader = resourceLoader;
       }
   
       @Bean
       public SimpleUrlHandlerMapping faviconHandlerMapping() {
           SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
           mapping.setOrder(Ordered.HIGHEST_PRECEDENCE + 1);
           mapping.setUrlMap(Collections.singletonMap("**/favicon.ico",
                                                      faviconRequestHandler()));
           return mapping;
       }
   
       @Bean
       public ResourceHttpRequestHandler faviconRequestHandler() {
           ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
           requestHandler.setLocations(resolveFaviconLocations());
           return requestHandler;
       }
   
       private List<Resource> resolveFaviconLocations() {
           String[] staticLocations = getResourceLocations(
               this.resourceProperties.getStaticLocations());
           List<Resource> locations = new ArrayList<>(staticLocations.length + 1);
           Arrays.stream(staticLocations).map(this.resourceLoader::getResource)
               .forEach(locations::add);
           locations.add(new ClassPathResource("/"));
           return Collections.unmodifiableList(locations);
       }
   
   }
   ```

   - 欢迎页； 静态资源文件夹下的所有index.html页面；被"/**"映射；localhost:8080/   找首页

   - 所有的 **/favicon.ico  都是在静态资源文件下找

4. 默认的静态资源位置可以配置

   在配置文件中

   ```xml
   spring.resources.static-locations=classpath:/hello/, classpath:/hi/
   ```

   可以配置多个路径，用","分割，默认的配置将会被覆盖



### 模板引擎

JSP、Velocity、Freemarker、Thymeleaf

![https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring%20Boot/template-engine.png](https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring Boot/template-engine.png)

1. 引入thymeleaf

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   ```

2. thymeleaf使用

   spring-boot-autoconfigure的jar下org.springframework.boot.autoconfigure.thymeleaf

   ```java
   @ConfigurationProperties(prefix = "spring.thymeleaf")
   public class ThymeleafProperties {
   
   	private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;
   
   	public static final String DEFAULT_PREFIX = "classpath:/templates/";
   
   	public static final String DEFAULT_SUFFIX = ".html";
       
       //...
   }
   ```

   只要我们把HTML页面放在classpath:/templates/，thymeleaf就能自动渲染

   - 导入thymeleaf的名称空间

     ```html
     <html lang="en" xmlns:th="http://www.thymeleaf.org">
     ```

   - 使用thymeleaf的使用

     ```java
     package com.boot.bootweb01.controller;
     
     import org.springframework.stereotype.Controller;
     import org.springframework.web.bind.annotation.RequestMapping;
     
     import java.util.Map;
     
     @Controller
     public class HelloController {
     
         @RequestMapping("/hello")
         public String hello(Map<String, Object> map) {
             map.put("hello", "world");
             return "hello";
         }
     }
     ```

     

     ```html
     <!DOCTYPE html>
     <html lang="en" xmlns:th="http://www.thymeleaf.org">
     <head>
         <meta charset="UTF-8">
         <title>hello</title>
     </head>
     <body>
     <!-- th:text 将div里的文本内容设置为... -->
     <!-- 如果直接打开静态页面将呈现div里面的内容，而如果是在服务器中启动则是获取到的内容 -->
     <div th:text="${hello}">这里是欢迎信息</div>
     </body>
     </html>
     ```

   - th:任意html属性；来替换原生属性的值

     例如th:text, th:id, th:name等

     ![https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring%20Boot/2018-02-04_123955.png](https://raw.githubusercontent.com/wesleyzxl/Notes/master/pic/Spring Boot/2018-02-04_123955.png)

     ```
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
                     
                     ${session.foo}
                 3）、内置的一些工具对象：
     #execInfo : information about the template being processed.
     #messages : methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.
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
     #ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).
     
         Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
         	补充：配合 th:object="${session.user}：
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

     示例：

     ```java
     package com.boot.bootweb01.controller;
     
     import org.springframework.stereotype.Controller;
     import org.springframework.web.bind.annotation.RequestMapping;
     
     import java.util.Arrays;
     import java.util.Map;
     
     @Controller
     public class HelloController {
     
         @RequestMapping("/hello")
         public String hello(Map<String, Object> map) {
             map.put("hello", "你好");
             map.put("users", Arrays.asList("<h2>zhangsan</h2>", "wangwu"));
             return "hello";
         }
     }
     ```

     模板页面

     ```java
     <!DOCTYPE html>
     <html lang="en" xmlns:th="http://www.thymeleaf.org">
     <head>
         <meta charset="UTF-8">
         <title>hello</title>
     </head>
     <body>
     <!-- th:text 将div里的文本内容设置为... -->
     <!-- 如果直接打开静态页面将呈现div里面的内容，而如果是在服务器中启动则是获取到的内容 -->
     <div th:text="${hello}">这里是欢迎信息</div>
     <hr/>
     
     <!-- th:utext="${}"不会转义特殊字符 -->
     <!-- 在h3标签内使用会使每个遍历的字符串都加上h3标签 -->
     <h3 th:utext="${user}" th:each="user : ${users}"></h3>
     <hr/>
     
     <!-- h2里面有三个span -->
     <h2>
         <!-- [[]]相当于特殊字符会转义，而[()]不会转义特殊字符 -->
         <span th:each="user : ${users}">[(${user})] </span>
     </h2>
     
     </body>
     </html>
     ```

