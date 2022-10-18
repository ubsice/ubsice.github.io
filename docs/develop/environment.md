# 开发环境准备

---

UBSI微服务需要的开发环境：

- Java 8+

  UBSI目前还不支持其他语言来开发微服务，如果有异构系统需要访问UBSI的微服务，可以通过[UBSI Gateway网关](../gateway/readme.md) 接入。

  > Java 8应该使用8u291之后的版本

- Maven

  常用的Java集成开发工具（如IntelliJ）都会带有maven工具，如果没有也可以单独下载并安装；

  开发UBSI微服务必须依赖ubsi-core核心包，目前这个包还未发布到maven的中央仓库，而是单独托管在github-packages中 ( https://github.com/orgs/open-ubsi/packages )，所以需要在maven环境的settings.xml中添加如下配置：

  ```xml
  <servers>
    ......
  
    <server>
      <id>github</id>
      <username>{your-github-account}</username>
      <password>{your-github-Personal_Access_Token}</password>
      <!-- 访问github-packages需要拥有github账号，并创建Personal_Access_Token(PAT) -->
      <!-- 创建PAT: 登录github.com -> 个人Settings -> Developer settings -> Personal access tokens -> Generate new token -->
      <!-- 注意PAT需要赋予权限：public_repo | read:packages -->
    </server>
  </servers>
  
  <profiles>
    ......
  
    <profile>
      <id>dev</id>
      <repositories>
        ......
        
        <repository>
          <id>github</id>
          <url>https://maven.pkg.github.com/open-ubsi/release</url>
          <!-- 这是ubsi@github-packages的maven-repository-url -->
        </repository>
      </repositories>
    </profile>
  </profiles>
  
  <activeProfiles>
    <activeProfile>dev</activeProfile>
  </activeProfiles>
  ```

- Maven私有仓库服务

  UBSI的部署工具及服务仓库管理工具都需要将开发完成的微服务Jar包发布到maven私有仓库中，如果开发环境已经部署了maven仓库服务，需要在maven的settings.xml中进行正确配置：

  ```xml
  <servers>
    ......
  
    <!-- 将JAR包发布到maven私有仓库时使用的账号 -->
    <server>
      <id>maven-release</id>
      <username>{your-maven-account}</username>
      <password>{your-maven-password}</password>
    </server>
    <server>
      <id>maven-snapshot</id>
      <username>{your-maven-account}</username>
      <password>{your-maven-password}</password>
    </server>
  </servers>
  
  <profiles>
    ......
  
    <profile>
      <id>dev</id>
      <repositories>
        ......
        
        <!-- maven私有仓库地址 -->
        <repository>
          <id>maven-public</id>
          <url>{your-maven-public-repository-url}</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
          </snapshots>
        </repository>
      </repositories>
    </profile>
  </profiles>
  
  <activeProfiles>
    <activeProfile>dev</activeProfile>
</activeProfiles>
  ```

  如果没有maven私有仓库服务，可以自行下载并安装Nexus3进行搭建，或者在阿里云上申请一个：
  
  https://developer.aliyun.com/mvn/search
