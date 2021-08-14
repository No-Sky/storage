# Springboot整合MybatisPlus（超详细）完整教程~ - 易水寒的博客 - 博客园
## 新建 springboot 项目

开发工具：idea2019.2,maven3

![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatispluss.jpg)

![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-1.jpg)

### pom.xml

```null
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.2.0</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.28</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.47</version>
        </dependency>

```

### application.yml:

```null
server:
  port: 8081
  servlet:
    context-path: /

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/demo?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false&allowPublicKeyRetrieval=true
    username: root
    password: lyja
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
    serialization:
      write-dates-as-timestamps: false

mybatis-plus:
  configuration:
    map-underscore-to-camel-case: true
    auto-mapping-behavior: full
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  mapper-locations: classpath*:mapper/**/*Mapper.xml
  global-config:
    
    db-config:
      
      logic-not-delete-value: 1
      
      logic-delete-value: 0
```

### mybatisplus 分页插件 MybatisPlusConfig：

```null
package com.example.conf;

import com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;


@Configuration
public class MybatisPlusConfig {

    
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        return new PaginationInterceptor();
    }
}

```

### mybatisplus 自动生成代码 GeneratorCodeConfig.java：

```null
package com.example.conf;

import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.Scanner;


public class GeneratorCodeConfig {

    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotEmpty(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        
        AutoGenerator mpg = new AutoGenerator();

        
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath + "/src/main/java");
        gc.setAuthor("astupidcoder");
        gc.setOpen(false);
        
        gc.setSwagger2(false);
        mpg.setGlobalConfig(gc);

        
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://127.0.0.1:3306/demo?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false&allowPublicKeyRetrieval=true");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("lyja");
        mpg.setDataSource(dsc);

        
        PackageConfig pc = new PackageConfig();

        pc.setParent("com.example");
        pc.setEntity("model.auto");
        pc.setMapper("mapper.auto");
        pc.setService("service");
        pc.setServiceImpl("service.impl");
        mpg.setPackageInfo(pc);

        







        

        
        

        

        








        



        
        TemplateConfig templateConfig = new TemplateConfig();

        
        
        
        
        

        templateConfig.setXml(null);
        mpg.setTemplate(templateConfig);

        
        StrategyConfig strategy = new StrategyConfig();
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        strategy.setSuperEntityClass("com.baomidou.mybatisplus.extension.activerecord.Model");
        strategy.setEntityLombokModel(true);
        strategy.setRestControllerStyle(true);

        strategy.setEntityLombokModel(true);
        

        

        strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
        strategy.setControllerMappingHyphenStyle(true);
        strategy.setTablePrefix(pc.getModuleName() + "_");
        mpg.setStrategy(strategy);
        mpg.setTemplateEngine(new FreemarkerTemplateEngine());
        mpg.execute();
    }
}

```

## 测试

建表：  
![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-2.jpg)

执行 GeneratorCodeConfig.java 文件，输入表名 user：  
![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-3.jpg)

解决方法：在数据库连接中配置添加 allowPublicKeyRetrieval=true  
![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-4.jpg)

![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-5.jpg)

查看生成的文件；  
![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-6.jpg)

### 添加扫描 mapper 注解

启动 springboot 的 application 启动类：会报错，提示找不到 mapper 文件，我们需要在 springboot 启动类上添加扫描 mapper 的注解：

```null
package com.example;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.example.mapper")
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}

```

UserController.java 中新增接口：

```null
  @Autowired
    private IUserService userService;
    @PostMapping("/getUser")
    public User getUser(){
        return userService.getById(1);
    }
```

postman 测试：  
![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-8.jpg)

![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-7.jpg)

没问题。

上面是 mybatisplus 测试成功，下面我们继续测试我们自己写的 sql 是否成功。

在 resources 目录下新建 mapper 文件夹，新建 UserMapper.xml 文件：

```null
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.mapper.auto.UserMapper">

    
    <select id="findAllUser" resultType="com.example.model.auto.User">
       select * from user
    </select>

</mapper>
```

UserMapper.java

```null
package com.example.mapper.auto;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.example.model.auto.User;

import java.util.List;


public interface UserMapper extends BaseMapper<User> {

    public List<User>findAllUser();
}
```

IUserService:

```null
package com.example.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.example.model.auto.User;

import java.util.List;


public interface IUserService extends IService<User> {

    public List<User> findAllUser();
}

```

UseServiceImpl.java:

```null
package com.example.service.impl;

import com.example.model.auto.User;
import com.example.mapper.auto.UserMapper;
import com.example.service.IUserService;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;


@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {

    @Autowired
    private UserMapper userMapper;
    @Override
    public List<User> findAllUser() {
        return userMapper.findAllUser();
    }
}

```

UserController.java:

```null
package com.example.controller;


import com.example.model.auto.User;
import com.example.service.IUserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import org.springframework.web.bind.annotation.RestController;

import java.util.List;


@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private IUserService userService;
    @PostMapping("/getUser")
    public User getUser(){
        return userService.getById(1);
    }


    @PostMapping("/findAllUser")
    public List<User> findAllUser(){
        return userService.findAllUser();
    }
}

```

测试 findAllUser 接口：  
![](http://img.liuyj.top/springboot%E6%95%B4%E5%90%88mybatisplus-9.jpg)

常用的工具类：

ResultInfo.java

```null
package com.example.conf;

import lombok.Data;

import java.io.Serializable;


@Data
public class ResultInfo implements Serializable {

    
    private Integer code;
    
    private String message;
    
    private Object result;

    private Integer total;

    
    public ResultInfo() {
        super();
    }

    public ResultInfo(Status status) {
        super();
        this.code = status.code;
        this.message = status.message;
    }

    public ResultInfo result(Object result) {
        this.result = result;
        return this;
    }

    public ResultInfo message(String message) {
        this.message = message;
        return this;
    }
    public ResultInfo total(Integer total) {
        this.total = total;
        return this;
    }

    
    public ResultInfo(Integer code, String message) {
        super();
        this.code = code;
        this.message = message;
    }

    
    public ResultInfo(Integer code, Object result) {
        super();
        this.code = code;
        this.result = result;
    }

    
    public ResultInfo(Integer code, String message, Object result) {
        super();
        this.code = code;
        this.message = message;
        this.result = result;
    }
}

```

Status.java

```null
package com.example.conf;


public enum Status {

    
    SUCCESS(2000, "成功"),
    UNKNOWN_ERROR(9998,"未知异常"),
    SYSTEM_ERROR(9999, "系统异常"),


    INSUFFICIENT_PERMISSION(4003, "权限不足"),

    WARN(9000, "失败"),
    REQUEST_PARAMETER_ERROR(1002, "请求参数错误"),

    
    LOGIN_EXPIRE(2001, "未登录或者登录失效"),
    LOGIN_CODE_ERROR(2002, "登录验证码错误"),
    LOGIN_ERROR(2003, "用户名不存在或密码错误"),
    LOGIN_USER_STATUS_ERROR(2004, "用户状态不正确"),
    LOGOUT_ERROR(2005, "退出失败，token不存在"),
    LOGIN_USER_NOT_EXIST(2006, "该用户不存在"),
    LOGIN_USER_EXIST(2007, "该用户已存在");

    public int code;
    public String message;

    Status(int code, String message) {
        this.code = code;
        this.message = message;
    }
}

```

### 附录：

一份详尽的 yml 配置文件 (关于数据源的配置比较详尽)：

```null
server:
  port: 8085
  servlet:
    context-path: /test


spring:
  
  redis:
    host: 127.0.0.1
    port: 6379
    timeout: 20000
    
    
    
    
    
    
    password: lyja
    application:
      name: test
    jedis:
      pool:
        max-active: 8
        min-idle: 0
        max-idle: 8
        max-wait: -1
    database: 0

  autoconfigure:
    exclude: com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceAutoConfigure
  datasource:
    dynamic:
      
      primary: master
      strict: false
      datasource:
        master:
          driver-class-name: com.mysql.cj.jdbc.Driver
          url: jdbc:mysql://127.0.0.1:3306/test?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false
          username: root
          password: lyja
    
    druid:
      
      stat-view-servlet:
        enabled: true
        url-pattern: /druid/*
        login-username: admin
        login-password: admin
        
        initial-size: 5
        
        max-active: 30
        
        min-idle: 5
        
        max-wait: 60000
        
        time-between-eviction-runs-millis: 60000
        
        min-evictable-idle-time-millis: 300000
        
        validation-query: select count(*) from dual
        
        test-while-idle: true
        
        test-on-borrow: false
        
        test-on-return: false
        
        pool-prepared-statements: false
        
        max-pool-prepared-statement-per-connection-size: 50
        
        filters: stat,wall
        
        connection-properties:
          druid.stat.mergeSql: true
          druid.stat.slowSqlMillis: 500
        
        use-global-data-source-stat: true
        filter:
          stat:
            log-slow-sql: true
            slow-sql-millis: 1000
            merge-sql: true
          wall:
            config:
              multi-statement-allow: true
  servlet:
    multipart:
      
      enabled: true
      
      file-size-threshold: 2KB
      
      max-file-size: 200MB
      
      max-request-size: 215MB

mybatis-plus:
  configuration:
    map-underscore-to-camel-case: true
    auto-mapping-behavior: full
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  mapper-locations: classpath*:mapper/**/*Mapper.xml
  global-config:
    
    db-config:
      
      logic-not-delete-value: 1
      
      logic-delete-value: 0
logging:
  level:
    root: info
    com.example: debug
```

 [https://www.cnblogs.com/liuyj-top/p/12976396.html](https://www.cnblogs.com/liuyj-top/p/12976396.html)
