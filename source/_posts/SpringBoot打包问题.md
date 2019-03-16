---
title: SpringBoot打包问题
date: 2019-01-24 12:52:26
tags: SpringBoot
categories: note
---
# SpringBoot web项目打包坑= =
这两天写一个项目需要把SpingBoot的web项目打包部署，然而打包的war和jar神特么居然访问不了里面的资源，相当懵逼，折腾了我一两天，找来找去才发现解决方案。  
 懵逼的我，居然一定硬要1.4.2版本的maven插件，这个坑的原因估计待日后深究springboot才能清楚了。。。。。
   <build>
```xml
        <finalName>project_name</finalName>
        <resources>
            <resource>
                <directory>${basedir}/src/main/webapp</directory>
                <targetPath>META-INF/resources</targetPath>
                <includes>
                    <include>**/**</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <filtering>false</filtering>
                <includes>
                    <include>**/**</include>
                </includes>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>1.4.2.RELEASE</version>
            </plugin>
        </plugins>

    </build>

```
