---

layout: post
title: "antrun"
date: 2022-05-28 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# maven antrun plugin

有时候我们希望打包的时候添加一些行为，比如把文件从resource 文件夹下拷贝到另外一个地方.

这时候就可以使用 maven antrun plugin.



参考文章： [https://maven.apache.org/plugins/maven-antrun-plugin/usage.html](https://maven.apache.org/plugins/maven-antrun-plugin/usage.html)

```
<project>
  [...]
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-antrun-plugin</artifactId>
        <version>1.8</version>
        <executions>
          <execution>
            <phase> <!-- Maven的生命周期阶段 --> </phase>
            <configuration>
              <target>

                <!--
                  将任务Ant任务放在这里，还可以在这里添加一个build.xml文件
                -->

              </target>
            </configuration>
            <goals>
              <goal>run</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  [...]
</project>
```



代码仓库:   [https://github.com/gongchangyou/transactional](https://github.com/gongchangyou/transactional) 这里借用下transactional仓库演示一下



直接打包， 就可以看到具体的target任务

```
mvn package -Dmaven.test.skip=true
```



