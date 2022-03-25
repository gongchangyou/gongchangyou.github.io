---
layout: post
title: "mysql-docker"
date: 2022-03-25 10:25:06 +0800
comments: true
category: mysql
tag: [mysql]
---



#  MySQL docker 部署

MySQL是互联网最常用的数据库，今天来部署并且用java调用



[https://www.runoob.com/docker/docker-install-mysql.html](https://www.runoob.com/docker/docker-install-mysql.html)



因为本地已经有3306端口了

```
docker run -itd --name mysql-test -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```



root账号默认不开远程连接 [https://blog.csdn.net/qq_34885405/article/details/93041509](https://blog.csdn.net/qq_34885405/article/details/93041509)



修改密码

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'yourpassword';
```



终端显示格式化

```
select * from user \G;
```





mac电脑 +MySQL 8 认证错误 [https://www.jianshu.com/p/9a645c473676](https://www.jianshu.com/p/9a645c473676) 修改认证方式





<font color="red">怎么连上？</font>

1. 创建用户

   ```
   CREATE USER 'test'@'%' IDENTIFIED BY 'xxx';
   ```

2. 修改认证方式

   ```
   ALTER USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY 'xxx';
   ```

3. 用root用户建一张DB macaque

4. 给test用户授权

   ```
   grant all privileges on macaque.* to 'test'@'%';
   ```





不知道为啥 之前用的Sequel Pro 有问题，无法连上，可能是无法兼容mysql 8.0吧。只能使用IDEA 自带的 database连上了。







### 使用mybatis-plus 实现CRUD

代码仓库： [https://github.com/gongchangyou/dubbo-demo-producer](https://github.com/gongchangyou/dubbo-demo-producer)

1. pom依赖，因为我的项目中已经有dubbo 和 rocketMQ集成，再加入mybatis-plus 可能会有很多冲突。

    ```
      <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>3.4.3</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>8.0.28</version>
            </dependency>
    ```



    问题： Spring-jdbc-5.3.6中的NamedParameterJdbcTemplate 依赖了 org/springframework/util/ConcurrentLruCache. 所以还需要升级spring-core到 5.3.6



    ![]({{ site.baseurl}}/images/202203/WechatIMG65.png){: width="800" }
    
    ![]({{ site.baseurl}}/images/202203/WechatIMG66.png){: width="800" }



2. 添加db配置

    ```
    #datasource
    spring.datasource.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
    spring.datasource.hikari.connection-test-query=select 1
    spring.datasource.url=jdbc:mysql://localhost:3306/macaque?characterEncoding=utf8&useSSL=true
    spring.datasource.username=test
    spring.datasource.password=test123
    ```

3. 添加model
                   {% highlight java %}
        package com.braindata.dubbodemo.repository.db.model;

        import com.baomidou.mybatisplus.annotation.IdType;
        import com.baomidou.mybatisplus.annotation.TableId;
        import com.baomidou.mybatisplus.annotation.TableName;
        import lombok.AllArgsConstructor;
        import lombok.Builder;
        import lombok.Data;
        import lombok.NoArgsConstructor;
              
        @Builder
        @AllArgsConstructor
        @NoArgsConstructor
        @Data
        @TableName("macaque")
        public class Macaque {
              
          @TableId(type = IdType.AUTO)
          private long id;
          private String name;
          private String memo;
              
        }
       {% endhighlight %}



 4 . 添加mapper
                   {% highlight java %}
package com.braindata.dubbodemo.repository.db.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.braindata.dubbodemo.repository.db.model.Macaque;
import org.springframework.stereotype.Component;

/**
 * @author gongchangyou
 * @version 1.0
 * @date 2021/11/17 7:45 下午
 */
@Component
public interface MacaqueMapper extends BaseMapper<Macaque> {
}

       {% endhighlight %}
                   

5 . 添加repository
       {% highlight java %}
package com.braindata.dubbodemo.repository;

import com.baomidou.mybatisplus.extension.conditions.query.LambdaQueryChainWrapper;
import com.braindata.dubbodemo.repository.db.model.Macaque;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

/**
 * @author gongchangyou
 * @version 1.0
 * @date 2021/11/17 8:37 下午
 */
@Component
public class MacaqueRepository {
    @Autowired
    com.braindata.dubbodemo.repository.db.mapper.MacaqueMapper macaqueMapper;

    public int insertMacaque(Macaque macaque) {
        return macaqueMapper.insert(macaque);
    }

    public int updateMacaque(Macaque macaque) {
        return macaqueMapper.updateById(macaque);
    }

    public Macaque getMacaqueById(Long id) {
        return macaqueMapper.selectById(id);
    }

    public Macaque getMacaqueByName(String name) {
        return new LambdaQueryChainWrapper<>(macaqueMapper)
                .eq(Macaque::getName, name)
                .one();
    }


}


              {% endhighlight %}

6 . 最后在Application上添加MapperScan注解

​       {% highlight java %}

package com.braindata.dubbodemo;

import com.braindata.rocketmq.MessageSource;
import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.stream.annotation.EnableBinding;

@EnableBinding(MessageSource.class)
@SpringBootApplication(scanBasePackages = {"com.braindata" })
//@EnableNacosDiscovery
//@DubboComponentScan(value = "com.braindata.dubbodemo.impl")
@EnableDubbo(scanBasePackages = "com.braindata.dubbodemo.impl")
@MapperScan("com.braindata.dubbodemo.repository.db.mapper")
public class DubboDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DubboDemoApplication.class, args);
    }

}

​       {% endhighlight %}



7 . 测试ok

       {% highlight java %}
       import com.braindata.dubbodemo.DubboDemoApplication;
import com.braindata.dubbodemo.repository.MacaqueRepository;
import com.braindata.dubbodemo.repository.db.model.Macaque;
import lombok.extern.slf4j.Slf4j;
import lombok.val;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

/**
 * @author gongchangyou
 * @version 1.0
 * @date 2022/3/15 9:41 上午
 */

@Slf4j
@SpringBootTest(classes= DubboDemoApplication.class)
public class DBTest {

    //mongodb 客户端
    @Autowired
    private MacaqueRepository repository;


    @Test
    public void insert() {
        val result = repository.insertMacaque(new Macaque().builder()
                        .name("CJ184")
                        .memo("184memo")
                .build());
        log.info("result={}", result);
    }
    
    @Test
    public void update() {
        val result = repository.updateMacaque(new Macaque().builder()
                        .id(2)
                .name("CJ185")
                .memo("185memo")
                .build());
        log.info("result={}", result);
    }
    
    @Test
    public void search() {
        val result = repository.getMacaqueById(2L);
        log.info("result={}", result);
    }
    
    @Test
    public void searchByName() {
        val result = repository.getMacaqueByName("CJ185");
        log.info("result={}", result);
    }
}
              {% endhighlight %}