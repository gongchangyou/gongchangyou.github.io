---

layout: post
title: "maven plugin"
date: 2022-05-14 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# maven 插件编写

参考文章： [https://blog.csdn.net/weixin_44433834/article/details/122620067](https://blog.csdn.net/weixin_44433834/article/details/122620067)



项目地址： [https://github.com/gongchangyou/mavenplugin](https://github.com/gongchangyou/mavenplugin)





1. 修改打包方式

   1. ```
      <packaging>maven-plugin</packaging>
      ```

2. ### 引入插件开发相关依赖

   ```
    <dependencies>
           <!--插件的抽象类-->
           <dependency>
               <groupId>org.apache.maven</groupId>
               <artifactId>maven-plugin-api</artifactId>
               <version>3.6.2</version>
           </dependency>
           <!--MOJO注解的来源-->
           <dependency>
               <groupId>org.apache.maven.plugin-tools</groupId>
               <artifactId>maven-plugin-annotations</artifactId>
               <version>3.6.0</version>
               <scope>provided</scope>
           </dependency>
    
       </dependencies>
   ```

   ```
           <dependency>
               <groupId>org.apache.maven</groupId>
               <artifactId>maven-core</artifactId>
               <version>3.6.2</version>
           </dependency>
   ```

   

3. ### 继承插件父类，设置目标

4. ### 绑定到构建生命周期

5. ### 默认可获取的参数

6. 编写代码

```
package com.hundsun.broker;
 
import org.apache.commons.lang3.StringUtils;
import org.apache.maven.plugin.AbstractMojo;
import org.apache.maven.plugin.MojoExecutionException;
import org.apache.maven.plugin.MojoFailureException;
import org.apache.maven.plugins.annotations.LifecyclePhase;
import org.apache.maven.plugins.annotations.Mojo;
import org.apache.maven.plugins.annotations.Parameter;
 
import java.io.File;
import java.util.List;
 
/**
 * @author yyzsx
 * @version 1.0.0
 * @ProjectName yyzsx-maven-plugin
 * @ClassName MyMojo.java
 * @Description 演示-输出项目信息
 * @createTime 2021年08月08日 13:53:00
 */
@Mojo(name = "showProjectInfo", defaultPhase = LifecyclePhase.COMPILE)
public class MyMojo extends AbstractMojo {
    /**
     * 项目根路径
     */
    @Parameter(defaultValue = "${basedir}", readonly = true, required = true)
    private File basedir;
 
    /**
     * 获取pom文件中项目版本，或者支持-Dversion=xx，进行传入
     */
    @Parameter(property = "version", defaultValue = "${project.version}", readonly = true, required = true)
    private String version;
 
    /**
     * 获取本地maven仓库地址
     */
    @Parameter(defaultValue = "${settings.localRepository}", readonly = true, required = true)
    private String localRepository;
 
    /**
     * 参数配置，作者
     */
    @Parameter(property = "author", defaultValue = "${showProjectInfo.author}", readonly = true, required = true)
    private String author;
 
    /***
     * 参数集合
     */
    @Parameter(property = "lists", readonly = true)
    private List lists;
 
    @Override
    public void execute() throws MojoExecutionException, MojoFailureException {
        getLog().info("*******************ShowProjectInfo*******************");
        getLog().info("--------------------MyFirstMojo,begin--------------------");
        System.out.println("欢迎语：Hello Maven Plugin");
        System.out.println("项目本地仓库localRepository:" + this.localRepository);
        System.out.println("项目跟路径basedir:" + basedir);
        System.out.println("项目版本version:" + this.version);
        System.out.println("项目作者author:" + author);
        System.out.println("项目包含文件类型lists：" + StringUtils.join(lists.toArray(), ","));
        getLog().info("--------------------MyFirstMojo,end-----------------------");
    }
}
 
```

7. mvn install

8. 调用命令执行

   ```
   mvn compile
   ```

   或者 指定执行goal
   ```
   mvn com.mouse:test-maven-plugin:0.0.1-SNAPSHOT:showProjectInfo
   
   格式是这样的: mvn groupId:artifactId:version:goal
   
   缩写： mvn test:showProjectInfo
   ```

   



IDEA 开发maven plugin . 在command line这里无需写mvn

![]({{ site.baseurl}}/images/202205/WechatIMG145.png){: width="800" }





### 每次编辑mojo文件后，需要mvn install 再执行 mvn test:showProjectInfo





问题1： 有时候会报 Index 23886 out of bounds for length 6498 之类的错

回答1：  添加如下配置

```
 <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-plugin-plugin</artifactId>
                <version>3.6.0</version>
                <executions>
                    <execution>
                        <id>default-descriptor</id>
                        <phase>process-classes</phase>
                    </execution>
                    <!-- if you want to generate help goal -->
                    <execution>
                        <id>help-goal</id>
                        <goals>
                            <goal>helpmojo</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```
