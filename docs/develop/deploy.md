# 服务部署

---

微服务测试完成后，需要部署到实际的容器中运行（在"单元测试"时使用的手工创建rewin.ubsi.module.json文件的方式仅在调试时使用），部署微服务可以通过UBSI的部署工具来完成。



首先需要完成部署前的准备工作：

- 启动一个独立的UBSI Container微服务容器

  `java -jar ubsi-core-ce-2.3.1-jar-with-dependencies.jar`

  > JAR包下载可以访问 https://github.com/open-ubsi/ubsi-core

  容器启动后默认的监听地址为本机（localhost）的7112端口

- 在项目目录下执行ubsi-maven插件的部署命令（已经在pom.xml中正确配置了ubsi-maven-plugin）

  `mvn ubsi:deploy`



利用ubsi-maven插件，还可以直接在项目目录下启动容器并加载微服务：

`mvn ubsi:run`



除了ubsi-maven插件，UBSI正式版本还提供了不依赖项目maven环境的独立命令行部署工具，或者通过UBSI治理工具的服务仓库进行服务部署。详见：https://ubsi-home.github.io/docs/develop/deploy.html

