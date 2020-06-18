# Java 开发环境——Maven配置

[toc]

Maven是一个项目管理和综合工具。[Maven](http://www.yiibai.com/maven)提供了开发人员构建一个完整的生命周期框架。开发团队可以自动完成项目的基础工具建设，Maven使用标准的目录结构和默认构建生命周期。

在多个开发团队环境时，Maven可以设置按标准在非常短的时间里完成配置工作。由于大部分项目的设置都很简单，并且可重复使用，Maven让开发人员的工作更轻松，同时创建报表，检查，构建和测试自动化设置。

## 下载maven

前往官网下载最新版Maven包 http://maven.apache.org/download.cgi

注意：Maven 3.3 以上需要JDK 1.7+，最好安装 JDK 1.8

## Maven配置

### 基本配置

-   下载Maven之后解压即可使用。假设 解压到  `F:\apache-maven-3.3.3` 

-   找到配置文件  `F:\apache-maven-3.3.3\conf\settings.xml` 

-   找到 settings 下面的  localRepository，配置本地仓库，如

    ` <localRepository>D:\Maven_Repo</localRepository>`

-   >   本地仓库用于存放从远程仓库下载的 jar,这样所有用Maven管理的项目可以通用，无需每个项目都添加 lib

-   配置好Maven之后，还需要在IDE中配置。一般IDE如IDEA、MyEclipse等都内置了Maven插件。使用时只需将IDE 内置的Maven配置指向自定义的配置即可。

    -   注意：最好在` File | Other Settings | Settings for new projects`中设置，这样不用每次新建或导入maven项目都需要更改项目的maven配置

-   配置好之后，就可以在项目的 POM.xml 中添加 库 的Maven坐标，从而将相应的库添加在项目依赖中。一般从[Maven中央仓库](http://mvnrepository.com/)中查找库的坐标。

    >```xml
    ><!--google通用类库坐标-->
    > <dependency>
    >     	<groupId>com.google.guava</groupId>
    >     	<artifactId>guava</artifactId>
    >     	<version>19.0</version>
    ></dependency>
    >```

### Maven 私服配置

Maven 私服是指自己/其他公司搭建的私有或公有Maven仓库，区别于[Maven中央仓库](http://mvnrepository.com/)。我们可以在中央仓库中查找 jar 的坐标，添加到POM文件中。maven首先会从私服中查找，没有的话私服会从中央仓库下载并保存，然后maven再从私服中下载到本地仓库。私服可以加快 jar 的下载，同时我们也可以发布自己的 jar 到私服。

> nexus 不仅可以代理maven仓库，也可以代理 npm 等其他仓库

#### 配置中央仓库镜像

-   找到 ` <mirrors>` ，添加如下配置。URL 为 私服地址

```xml
<!-- 
	配置maven只使用私服时，第一个mirrorOf之间写*就行
	osgeo和geosolutions不能使用nexus，必须绕过
-->
<mirror>
    <id>nexus</id>
    <mirrorOf>external:*,!osgeo,!geosolutions</mirrorOf>
    <name>MyNexus</name>
    <!-- 	你的私服地址（此处为示例），私服配置好之后可以在浏览器中打开   -->
    <url>http://192.168.6.7:8080/nexus/content/groups/public</url>
</mirror>
<!-- 	
下面的三个都可以配置到nexus中做代理。
此时可以不在本地配置，并从上面的 mirrorOf 列表中删除
 -->
<!-- 	osgeo   -->
<mirror>
    <id>osgeo</id>
    <mirrorOf>osgeo</mirrorOf>
    <name>Open Source Geospatial Foundation Repository</name>
    <url>https://repo.osgeo.org/</url>
</mirror>
<mirror>
    <id>boundless</id>
    <mirrorOf>boundlessgeo</mirrorOf>
    <name>Boundlessgeo Repository</name>
    <url>https://repo.boundlessgeo.com/main/</url>
</mirror> 
<mirror>
     <id>n52-wps</id>
     <mirrorOf>n52-releases</mirrorOf>
     <name>52n Releases</name>
     <url>http://52north.org/maven/repo/releases</url>
 </mirror>
<!-- 	geosolutions   -->
<mirror>
    <id>geosolutions</id>
    <mirrorOf>geosolutions</mirrorOf>
    <name>geosolutions repository</name>
    <url>http://maven.geo-solutions.it/</url>
</mirror>
<!-- 阿里的Maven镜像。用阿里的Maven镜像取代中央仓库。若配置了私有仓库，需要把这部分注释掉   -->
<!-- <mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
</mirror>  -->
```

#### 配置 profiles

配置镜像之后，不能马上使用，还必须覆盖Maven中默认使用的profile并激活它。

-   找到 profiles，在里面配置如下内容

    ```xml
    <!-- id为central：覆盖超级POM中中央仓库位置 -->
    <profile>
        <id>nexus</id>
        <repositories>
            <repository>
                <id>central</id>
                <name>MyNexus</name>
                <url>http://central</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>central</id>
                <url>http://central</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
    </profile>
    ```

-   在 profiles 的结束标签` </profiles>` 之后，添加激活配置

    ```xml
    <activeProfiles>
       <activeProfile>nexus</activeProfile>
    </activeProfiles>
    ```

## 构建和部署配置

项目开发通常会有将自己的组件或项目通过maven构建，并自动化部署到私服/服务器的需求。此配置在`servers`下面。

-   为了部署构建到maven私有仓库，需要知道私服的用户名和密码；
-   为了部署项目war包到服务器实现项目自动发布，需要知道服务器（Tomcat）的管理员用户名和密码

```xml
<!-- 为部署构建至仓库配置认证信息 -->
<!-- 快照版本，如 <version>2.0-SNAPSHOT</version> -->
<server>
    <id>nexus-snapshots</id>
    <username>admin</username>
    <password>your-pwd</password>
</server>
<!-- 发布版本，如 <version>2.0-RELEASE</version> -->
<server>
    <id>nexus-releases</id>
    <username>admin</username>
    <password>your-pwd</password>
</server>
<!-- 部署到tomcat -->
<server>
    <id>tomcat7</id>
    <username>admin</username>
    <password>your-pwd</password>
</server>
```

---

## 常见问题

### 无法下载依赖

- 注释掉相应的构建，然后重新打开注释。IDE一般会自动`reimport`

- 检查 maven 配置文件 `settings.xml`, 查看仓库地址是否可访问。

    - 对于 EGC 项目，需要配置我们自己的仓库地址（nexus）

    - 一般的项目，推荐使用 阿里云的maven 镜像

        ```xml
        <!-- 不能使用nexus时才需要  -->
        <mirror>
          <id>alimaven</id>
          <mirrorOf>central</mirrorOf>            
          <name>aliyun maven</name>
          <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        </mirror> 
        ```

- 检查 IDE 中的maven 配置。尤其是新建的项目，其默认的maven是IDE 自带的，只会从 maven 中央仓库下载，速度慢，而且没有我们自己上传的包（例如 `gdal-gisinternals`）

- 修改配置后可能需要刷新maven，或者重启 IDE

### 刷新maven还是无法下载jar包

可能是在相应的目录下存在`.lastUpdated`文件，需要删掉这个文件之后再刷新

1、cleanLastUpdated.bat（windows版本）

```bash
 @echo off
    rem create by NettQun
      
    rem 这里写你的仓库路径
    set REPOSITORY_PATH=D:\maven-repository
    rem 正在搜索...
    for /f "delims=" %%i in ('dir /b /s "%REPOSITORY_PATH%\*lastUpdated*"') do (
        echo %%i
        del /s /q "%%i"
    )
    rem 搜索完毕
    pause
```

​    2、cleanLastUpdated.sh（linux版本）

```bash
    # 这里写你的仓库路径
    REPOSITORY_PATH=~/Documents/tools/repository
    echo 正在搜索...
    find $REPOSITORY_PATH -name "*lastUpdated*" | xargs rm -fr
    echo 搜索完
```

---

如果不行，删掉本地仓库，重新下载 `jar`。该操作可能需要比较长的时间，谨慎操作。

### 使用本地 JAR 包，而非repository中的

```XML
<dependency>
    <groupId>org.hamcrest</groupId>
    <artifactId>hamcrest-core</artifactId>
    <version>1.5</version>
    <!-- 下述二者缺一不可 -->
    <scope>system</scope>
    <systemPath>${basedir}/lib/hamcrest-core-1.3.jar</systemPath>
</dependency>
```

### 直接安装jar包到本地仓库

```shell
mvn install:install-file -Dfile=jar包的位置 -DgroupId=groupId 
-DartifactId=artifactId -Dversion=version -Dpackaging=jar 
```

---

参考资料：

>   [Maven日常 —— 你应该知道的一二三](http://www.cnblogs.com/xing901022/p/5024357.html)
>
>   [maven入门及使用myeclipse构建maven项目](http://blog.csdn.net/tonytfjing/article/details/39006087)
>
>   [Maven介绍，包括作用、核心概念、用法、常用命令、扩展及配置](http://www.cnblogs.com/maoIT/p/4604207.html)
>
>   [Maven教程](http://www.yiibai.com/maven/)
