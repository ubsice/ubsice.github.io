# 创建过滤器的Project

---

创建一个Java的maven project，然后在pom.xml中添加必要的元素，完整的内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.rewin</groupId>
    <artifactId>ubsi-filter-demo</artifactId>
    <version>1.0</version>

    <name>ubsi.demo.filter</name>
    <description>UBSI过滤器示例</description>

    <properties>
        <java.version>1.8</java.version>
        <ubsi.version>2.3.1</ubsi.version>
    </properties>

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
                    <filters>      <!-- 本项目包含的过滤器 -->
                        <filter>
                            <className>ubsi.demo.filter.Container</className>	<!-- 过滤器类名 -->
                        </filter>
                    </filters>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```


