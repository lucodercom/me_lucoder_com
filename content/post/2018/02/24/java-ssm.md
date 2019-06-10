---
title: "Java中的SSM框架和配置"
date: 2018-02-24T14:21:50+08:00
tags: ["Java","Javaweb","SSM框架"]
categories: ["Java","Javaweb","SSM框架"]
toc: false
---

# 什么是SSM框架
SSM框架是JavaWeb中常用的框架，包括`Spring`、`Spring MVC`、`Mybatis`三种框架的一个整合。其中任意两个框架都可以相互组合`SS框架`、`SM`（O(∩_∩)O~）框架（两种情况：`Spring + Mybatis`、`Spring MVC + Mybatis`）。具体的详细情况可以参考这篇文章<a href='http://blog.csdn.net/daniel_fei/article/details/56670751' target='_blank'>http://blog.csdn.net/daniel_fei/article/details/56670751</a>。

# 框架基本结构

```views
-       |----controllers  控制器
|       |----dao          dao层
|       |----mapper       mybatis的mapper文件
|--src--|----pojo         简单java对象
|       |----service      服务层
|       |----service.impl 服务实现层
|
|
|               |----jdbc.properties    jdbc配置
|               |----log4j.properties   log4j配置
|-- resources --|----spring-mvc.xml     springmvc配置
|               |----spring-mybatis.xml spring整合mybatis配置
|   
|---|Deployed Resources     部署资源文件
```

# 框架整合过程

创建一个`maven`的网站项目(不要问为什么)，然后按照下面步骤配置。

## 数据库初始化

> 请预先整理好数据库，我这里使用的MariaDB(mysql)，也可以使用其他数据库。

```sql
--mysql-connctor-java 5.1.32
DROP TABLE IF EXISTS `user_t`;  
  
CREATE TABLE `user_t` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `user_name` varchar(40) NOT NULL,  
  `password` varchar(255) NOT NULL,  
  `age` int(4) NOT NULL,  
  PRIMARY KEY (`id`)  
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;  
  
insert  into `user_t`(`id`,`user_name`,`password`,`age`) values (1,'测试','sfasgfaf',24);  

```

## maven配置文件

> 下面的是maven项目的`pom.xml`文件配置。

```xml
<properties>  
        <!-- spring版本号 -->  
        <spring.version>4.0.2.RELEASE</spring.version>  
        <!-- mybatis版本号 -->  
        <mybatis.version>3.2.6</mybatis.version>  
        <!-- log4j日志文件管理包版本 -->  
        <slf4j.version>1.7.7</slf4j.version>  
        <log4j.version>1.2.17</log4j.version>  
    </properties>  
    <dependencies>  
        <dependency>  
            <groupId>junit</groupId>  
            <artifactId>junit</artifactId>  
            <version>4.11</version>  
            <!-- 表示开发的时候引入，发布的时候不会加载此包 -->  
            <scope>test</scope>  
        </dependency>  
        <!-- spring核心包 -->  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-core</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-web</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-oxm</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-tx</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-jdbc</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-webmvc</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-aop</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-context-support</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-test</artifactId>  
            <version>${spring.version}</version>  
        </dependency>  
        <!-- mybatis核心包 -->  
        <dependency>  
            <groupId>org.mybatis</groupId>  
            <artifactId>mybatis</artifactId>  
            <version>${mybatis.version}</version>  
        </dependency>  
        <!-- mybatis/spring包 -->  
        <dependency>  
            <groupId>org.mybatis</groupId>  
            <artifactId>mybatis-spring</artifactId>  
            <version>1.2.2</version>  
        </dependency>  
        <!-- 导入java ee jar 包 -->  
        <dependency>  
            <groupId>javax</groupId>  
            <artifactId>javaee-api</artifactId>  
            <version>7.0</version>  
        </dependency>  
        <!-- 导入Mysql数据库链接jar包 -->  
        <dependency>  
            <groupId>mysql</groupId>  
            <artifactId>mysql-connector-java</artifactId>  
            <version>5.1.30</version>  
        </dependency>  
        <!-- 导入dbcp的jar包，用来在applicationContext.xml中配置数据库 -->  
        <dependency>  
            <groupId>commons-dbcp</groupId>  
            <artifactId>commons-dbcp</artifactId>  
            <version>1.2.2</version>  
        </dependency>  
        <!-- JSTL标签类 -->  
        <dependency>  
            <groupId>jstl</groupId>  
            <artifactId>jstl</artifactId>  
            <version>1.2</version>  
        </dependency>  
        <!-- 日志文件管理包 -->  
        <!-- log start -->  
        <dependency>  
            <groupId>log4j</groupId>  
            <artifactId>log4j</artifactId>  
            <version>${log4j.version}</version>  
        </dependency>  
        <!-- 格式化对象，方便输出日志 -->  
        <dependency>  
            <groupId>com.alibaba</groupId>  
            <artifactId>fastjson</artifactId>  
            <version>1.1.41</version>  
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
        <!-- 映入JSON -->  
        <dependency>  
            <groupId>org.codehaus.jackson</groupId>  
            <artifactId>jackson-mapper-asl</artifactId>  
            <version>1.9.13</version>  
        </dependency>  
        <!-- 上传组件包 -->  
        <dependency>  
            <groupId>commons-fileupload</groupId>  
            <artifactId>commons-fileupload</artifactId>  
            <version>1.3.1</version>  
        </dependency>  
        <dependency>  
            <groupId>commons-io</groupId>  
            <artifactId>commons-io</artifactId>  
            <version>2.4</version>  
        </dependency>  
        <dependency>  
            <groupId>commons-codec</groupId>  
            <artifactId>commons-codec</artifactId>  
            <version>1.9</version>  
        </dependency> 
    </dependencies>  
```

## properties配置

首先是jdbc的配置`jdbc.properties`

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/mybatis?useUnicode=true&characterEncoding=UTF-8
username=testroot
password=123
#定义初始连接数
initialSize=0
#定义最大连接数
maxActive=20
#定义最大空闲
maxIdle=20
#定义最小空闲
minIdle=1
#定义最长等待时间
maxWait=60000
```

然后是log4j的配置`log4j.properties`如下：

```properties
#定义LOG输出级别
log4j.rootLogger=INFO,Console,File
#定义日志输出目的地为控制台
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.Target=System.out
#可以灵活地指定日志输出格式，下面一行是指定具体的格式
log4j.appender.Console.layout = org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=[%c] - %m%n

#文件大小到达指定尺寸的时候产生一个新的文件
log4j.appender.File = org.apache.log4j.RollingFileAppender
#指定输出目录
log4j.appender.File.File = logs/springmvcMybist/ssm.log
#定义文件最大大小
log4j.appender.File.MaxFileSize = 10MB
#输出所以日志，如果换成DEBUG表示输出DEBUG以上级别日志
log4j.appender.File.Threshold = ALL
log4j.appender.File.layout = org.apache.log4j.PatternLayout
log4j.appender.File.layout.ConversionPattern =[%p] [%d{yyyy-MM-dd HH\:mm\:ss}][%c]%m%n
```

## spring-mybatis整合

> 文件名为`spring-mybatis.xml`，注意文件的位置。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="http://www.springframework.org/schema/beans  
                        http://www.springframework.org/schema/beans/spring-beans-4.0.xsd  
                        http://www.springframework.org/schema/context  
                        http://www.springframework.org/schema/context/spring-context-4.0.xsd  
                        http://www.springframework.org/schema/mvc  
                        http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">
    <!-- 这里的 base-package 需要修改，修改为你所需要的即可 -->
    <!-- 自动扫描 -->
    <context:component-scan base-package="com.mxcms" />

    <!-- 引入配置文件 -->
    <bean id="propertyConfigurer"
        class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="location" value="classpath:jdbc.properties" />
    </bean>

    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close">
        <property name="driverClassName" value="${driver}" />
        <property name="url" value="${url}" />
        <property name="username" value="${username}" />
        <property name="password" value="${password}" />
        <!-- 初始化连接大小 -->
        <property name="initialSize" value="${initialSize}"></property>
        <!-- 连接池最大数量 -->
        <property name="maxActive" value="${maxActive}"></property>
        <!-- 连接池最大空闲 -->
        <property name="maxIdle" value="${maxIdle}"></property>
        <!-- 连接池最小空闲 -->
        <property name="minIdle" value="${minIdle}"></property>
        <!-- 获取连接最大等待时间 -->
        <property name="maxWait" value="${maxWait}"></property>
    </bean>

    <!-- spring和MyBatis完美整合，不需要mybatis的配置映射文件 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- 这里的value需要修改为你的mapper文件位置，注意这里是斜线 / 不是 . -->
        <!-- 自动扫描mapping.xml文件 -->
        <property name="mapperLocations" value="classpath:com/mxcms/mapper/*.xml"></property>
    </bean>

    <!-- 这里的value需要修改为你的dao文件夹位置 -->
    <!-- DAO接口所在包名，Spring会自动查找其下的类 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.mxcms.dao" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
    </bean>

    <!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
    <bean id="transactionManager"
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
</beans>
```

## maven 自动生成代码

下面需要使用maven自动生成工具生成代码，可以

下载

压缩包，解压文件，然后按照下面方法进行自动生成代码。

(1). 准备好你的下面的文件

```shell
mybatis-3.2.7.jar
mybatis-generator-core-1.3.2.jar
mysql-connector-java-5.1.25-bin.jar
```

(2). 修改自动生成代码配置文件

修改配置文件`generatorConfig.xml`的相关内容，准备生成代码。

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE generatorConfiguration  
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"  
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">  
<generatorConfiguration>  
<!-- 数据库驱动-->  
    <classPathEntry  location="mysql-connector-java-5.1.25-bin.jar"/>  
    <context id="DB2Tables"  targetRuntime="MyBatis3">  
        <commentGenerator>  
            <property name="suppressDate" value="true"/>  
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->  
            <property name="suppressAllComments" value="true"/>  
        </commentGenerator> 
        <!-- 这里的连接字符串需要修改为你的真实可用字符串。 --> 
        <!--数据库链接URL，用户名、密码 -->  
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://127.0.0.1/mybatis" userId="testroot" password="123">  
        </jdbcConnection>  
        <javaTypeResolver>  
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>  
        <!-- 这里需要修改这个文件夹名称 -->
        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="com.mxcms.pojo" targetProject="src">  <!--这个targetPackage需要修改-->
            <property name="enableSubPackages" value="true"/>  
            <property name="trimStrings" value="true"/>  
        </javaModelGenerator>  
        <!--这个targetPackage需要修改-->
        <!-- 生成映射文件的包名和位置-->  
        <sqlMapGenerator targetPackage="com.mxcms.mapper" targetProject="src">  
            <property name="enableSubPackages" value="true"/>  
        </sqlMapGenerator>  
        <!--这个targetPackage需要修改-->
        <!-- 生成DAO的包名和位置-->  
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.mxcms.dao" targetProject="src">  
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>  
        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->  
        <table tableName="user_t" domainObjectName="User" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
    </context>  
</generatorConfiguration>
```

(3). 制作一个bat脚本

```bat
IF NOT EXIST "src" MD "src"

java -jar mybatis-generator-core-1.3.2.jar -configfile generatorConfig.xml -overwrite

pause();
```

(4). 生成代码

将上面的所有文件放到一个文件夹中，然后运行bat文件，将会自动生成代码。
> 注意:表配置请务必一次写完，虽然中途仍然可以自动生成并复制，建议一次性设计好数据库和关系。

(5). 复制代码

将上面的所有代码复制到`src/main/java`下面即可。
> 一个错误信息：

你或许会遇到下面的错误：

```shell
 org.apache.ibatis.binding.BindingException: Invalid bound statement (not found)
```

首先检验一下你的路径是否正确，如果确保路径正确，可以尝试将`mapper`文件夹放到`resource`中。

## 实现service

到此为止dao层做好了，下面开始做服务层。

(1) 创建service接口

```java
public interface IUserService {
    public User getUserById(int userId);
}
```

(2). 实现接口

```java
@Service("userService")
public class UserService implements IUserService {
    @Resource
    private UserMapper userDao;
    public User getUserById(int userId) {
        return this.userDao.selectByPrimaryKey(userId);
    }
}
```

## 测试

到了这一步恭喜你`spring + mybatis`已经做好了，可以开始测试了。

```java
@RunWith(SpringJUnit4ClassRunner.class) // 表示继承了SpringJUnit4ClassRunner类
@ContextConfiguration(locations = { "classpath:spring-mybatis.xml" })

public class TestMyBatis {
	private static Logger logger = Logger.getLogger(TestMyBatis.class);
	// private ApplicationContext ac = null;
	@Resource
	private IUserService userService = null;

	// @Before
	// public void before() {
	// ac = new ClassPathXmlApplicationContext("applicationContext.xml");
	// userService = (IUserService) ac.getBean("userService");
	// }

	@Test
	public void test() {
		User user = userService.getUserById(1);
		// System.out.println(user.getUserName());
		// logger.info("值："+user.getUserName());
		logger.info(JSON.toJSONString(user));
	}
}
```

下面右键`debug as junit`，如果看到你的数据库的用户信息打印出来了，恭喜你。初步整合成功，如果报错好好看看步骤，然后认真跟着错误修改一下。

## ssm整合

经过上面的战斗，你已经完成`spring + mybatis`的整合，下面就会变得很简单。 配置`spring.xml`文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="http://www.springframework.org/schema/beans  
                            http://www.springframework.org/schema/beans/spring-beans-4.0.xsd  
                            http://www.springframework.org/schema/context  
                            http://www.springframework.org/schema/context/spring-context-4.0.xsd  
                            http://www.springframework.org/schema/mvc  
                            http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">

    <!--避免IE执行AJAX时，返回JSON出现下载文件 -->
    <bean id="mappingJacksonHttpMessageConverter"
        class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter">
        <property name="supportedMediaTypes">
            <list>
                <value>text/html;charset=UTF-8</value>
            </list>
        </property>
    </bean>
    <!-- 启动SpringMVC的注解功能，完成请求和注解POJO的映射 -->
    <bean
        class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
        <property name="messageConverters">
            <list>
                <ref bean="mappingJacksonHttpMessageConverter" />    <!-- JSON转换器 -->
            </list>
        </property>
    </bean>
    <!-- 对静态资源的访问 -->
    <!-- <mvc:resources mapping="/js/**" location="/js/" /> <mvc:resources mapping="/css/**" 
        location="/css/" /> -->

    <!-- 自动扫描该包，使SpringMVC认为包下用了@controller注解的类是控制器 -->
    <context:component-scan base-package="com.mxcms.controller" />
    <!-- 添加注解驱动 -->
    <mvc:annotation-driven enable-matrix-variables="true" />
    <!-- 允许对静态资源文件的访问 -->
    <mvc:default-servlet-handler />
    <!-- 定义跳转的文件的前后缀 ，视图模式配置 -->
    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 这里的配置我的理解是自动给后面action的方法return的字符串加上前缀和后缀，变成一个 可用的url地址 -->
        <property name="prefix" value="/WEB-INF/views/" />
        <property name="suffix" value=".jsp" />
    </bean>
    <!-- 配置文件上传，如果没有使用文件上传可以不用配置，当然如果不配，那么配置文件中也不必引入上传组件包 -->
    <bean id="multipartResolver"
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 默认编码 -->
        <property name="defaultEncoding" value="UTF-8" />
        <!-- 文件大小最大值 -->
        <property name="maxUploadSize" value="10485760000" />
        <!-- 内存中的最大值 -->
        <property name="maxInMemorySize" value="40960" />
    </bean>
</beans>
```

## web.xml整合

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://java.sun.com/xml/ns/javaee"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    version="3.0">
    <display-name>Archetype Created Web Application</display-name>
    
    <!-- Spring和mybatis的配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-mybatis.xml</param-value>
    </context-param>
    
    <!-- 编码过滤器 -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <async-supported>true</async-supported>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
    <!-- Spring监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    
    <!-- 防止Spring内存溢出监听器 -->
    <listener>
        <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
    </listener>
    
    <!-- Spring MVC servlet -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
        <async-supported>true</async-supported>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <!-- 此处可以可以配置成*.do，对应struts的后缀习惯 -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <welcome-file-list>
        <welcome-file>/index.html</welcome-file>
        <welcome-file>/index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
```

## controllers编写配置

编写controller

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @Resource
    private IUserService userService;
    @RequestMapping("/showUser")
    public String toIndex(HttpServletRequest request, Model model) {
        int userId = Integer.parseInt(request.getParameter("id"));
        User user = this.userService.getUserById(userId);
        model.addAttribute("user", user);
        return "User";
    }
}
```

[https://pan.lanzou.com/1378060](https://pan.lanzou.com/1378060)