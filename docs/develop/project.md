# 开启Java Project

---

创建一个Java的maven project，然后在pom.xml中添加必要的元素，完整的内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.rewin</groupId>
    <artifactId>ubsi-service-demo</artifactId>
    <version>1.0</version>

    <properties>
        <java.version>1.8</java.version>
        <ubsi.version>2.3.1</ubsi.version>
    </properties>

    <name>ubsi.demo.hello</name>
    <description>UBSI微服务示例</description>

    <dependencies>
        <dependency>
            <groupId>com.rewin</groupId>
            <artifactId>ubsi-core-ce</artifactId>
            <version>${ubsi.version}</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.10.1</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>utf-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0-M7</version>
                <configuration>
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>

            <plugin>
                <groupId>com.rewin</groupId>
                <artifactId>ubsi-maven-plugin</artifactId>
                <version>1.0.0</version>
                <configuration>     <!-- 插件的配置参数 -->
                    <services>      <!-- 本项目包含的微服务 -->
                        <service>
                            <name>ubsi.demo.hello</name>	<!-- 服务名字 -->
                            <className>ubsi.demo.hello.Service</className>	<!-- 服务类名 -->
                        </service>
                    </services>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```



对于一个完整的UBSI微服务开发项目，需要在pom.xml中配置下面的内容：

- 添加ubsi-core核心包的依赖，目前最新的版本是2.3.1
- 添加junit的依赖，这个用来进行服务接口的单元测试
- 设置ubsi的maven插件，用来部署或运行微服务
- 如果需要将微服务的JAR包发布到maven私有仓库，还应该设置mvn deploy时的maven仓库地址（仓库的登录账号配置在maven的settings.xml中）