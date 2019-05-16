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

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
    <version>2.1.4.RELEASE</version>
    <scope>compile</scope>
</dependency>
```

Spring Boot使用这个来做日志功能