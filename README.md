# mavenProject
自学maven实战——最后没看完，当作工具书使用

#### 作用

1. 通过坐标系统定位每一个构件，可以通过一组坐标找到任何一个java库
2. 管理原本分散在项目中的各个角落的项目信息
3. 提供一个中央仓库，可以找到任何流行开源库
4. 对目录结构，测试用例命名方法等都具有既定的规则

#### 极限编程与XP

核心价值

1. 简单
2. 交流与反馈
3. 测试驱动开发
4. 十分钟构建
5. 持续集成
6. 富有信息的工作区

### Maven基础信息

#### M2_HOME

作用：指向maven的安装目录

目录结构：

- bin:包含了mvn实际运行时调用的脚本，以及m2.conf文件
- boot:只包含一个文件，作为类加载器框架
- conf:包含重要的settings.xml,修改该文件是现在机器上全局地定制Maven行为，一般情况下，会复制该文件到~/.m2/目录下实现在用户范围内定制Maven行为
- lib:包含Maven运行时需要的java类库
- LICENSE.txt:记录使用许可
- NOTICE.txt:记录了包含的第三方软件
- README.txt:介绍

#### ~/.m2

在该文件下存有repository，所有的maven构件都被储存在该仓库中，

### maven使用入门

#### 编写POM

maven项目的核心是pom.xml文件，定义了项目的基本信息，描述项目的构建以及项目依赖等等

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

这一行是xml头，指定了xml文档的版本和编码方式

```xml
<project>
```

文件的根元素，声明了pom相关的命名空间和xsd元素，

```xml
<modelVersion>4.0.0</modelVersion>
```

制定了当前POM模型的版本

```xml
<groupId>com.jiedong</groupId>
<artifactId>helloworld</artifactId>
<version>1.0-SNAPSHOT</version>
<name>helloworld</name>
```

这三段元素定义了一个项目的基本坐标，groupId定义项目所属的组，artifactId定义了当前maven项目在族中唯一的ID，version指定了当前的版本，name为非必要字段，指定一个对用户友好的名称

#### 编写主代码

项目主代码和测试代码不同，项目主代码会被打包到最终的构件中，二测试代码只在测试时运行，不会被打包。

默认情况下，项目的主代码位于src/main/java目录中，在其中创建文件夹，并编写java文件，之后执行`mvn clean compile`对项目进行编译，clean清除target目录，compile编译项目主代码，最后将代码编译到target/classes目录下。

#### 编写测试代码

maven项目中默认的测试代码为与src/test/java中，为编写测试代码需要使用junit，因此需要一个junit依赖，在pom代码中添加dependencies元素，在该元素下可以包含多个元素以声明项目的依赖，依赖形式如下：

```xml
<dependencies>  
    <dependency>    
        <groupId>junit</groupId>    
        <artifactId>junit</artifactId>    
        <version>4.11</version>    
        <scope>test</scope>  
    </dependency>
</dependencies>
```

以来中包含groupId，artifactId和version等声明，scope限定了依赖的范围，在测试代码中`import JUnit`没有问题，但是在主代码中执行`import JUnit`就是错误的，不配置scope就是对主代码以及测试代码都生效。

##### 测试单元构成

一个测试单元包含三个步骤：

1. 准备测试类及数据
2. 执行要测试的行为
3. 检查结果

对于所有的测试都需要用@Test进行标注，具体可以参考onjava中测试那一节，编写测试用例完成之后，可以用`mvn clean test`执行测试

如果出现`compile failure`那么可能是maven compile插件的支持问题，需要进行配置，配置如下：

```xml
<plugin>  
    <artifactId>maven-compiler-plugin</artifactId>  
    <version>3.8.0</version>
</plugin>
```

#### 项目打包

执行`mvn ckean package`可以打包文件并置于target文件夹下

执行`mvn clean install`将项目输出的java安装到了Maven本地仓库中，可以看到相应的Hello World项目的pom和jar。

注意默认生成的jar包是不可以运行的，需要配置pom文件以提供可执行的jar包，文件的配置书中采用的是maven-shade-plugin，但是最终执行时仍然是不可执行的jar文件，因此改用了另一种方式：配置方法参考这个博客——[maven打包 jar中没有主清单属性]( https://blog.csdn.net/persistencegoing/article/details/97893872?utm_medium=distribute.pc_relevant.none-task-blog-baidujs-1 )

#### 使用Archetype生成项目骨架

maven的约定：

项目的根目录中放置pom.xml文件，在src/main/java目录中放项目主代码，在src/test/java中放置测试代码

用mvn archetype:generate

或者使用idea自带的配置方式——csdn上查找就可以了

### 坐标与依赖

#### maven坐标

maven坐标的元素包括：groupId,artifactId,version,packaging,classfier

1. groupId：定义了当前maven项目隶属的实际项目，
2. artifactId：定义了实际项目中的一个maven项目（模块），推荐使用实际项目名称作为其前缀
3. version：定义了maven项目所处的版本
4. packaging：定义了maven项目的打包方式，与扩展名相对应
5. classfier：该元素用于定义构造输出的一些附属构件，这个不能够之间定义，而是通过附件帮助生成的

#### 依赖范围：

1. compile：编译依赖范围，没有指定时，默认使用该依赖范围，使用该依赖的在编译，测试，运行三种模式下都有效
2. test：测试依赖范围，只对于测试有效，在运行或者编译时无法使用
3. provided：已提供依赖范围。在编译时以及测试时有效，而运行时无效
4. runtime：运行时范围依赖。在测试以及运行时有效，但在编译主代码时无效。
5. system：系统依赖范围和provided一致，但是需要显式地指定，且不能够移植
6. import：导入依赖范围。

#### 依赖配置

groupId,artifactId,version:依赖基本坐标

type:依赖类型，对应项目坐标中的packaging

scope:依赖范围

optional:标记是否可选

exclusions:排除传递性依赖

#### 依赖传递性

maven会解析各个直接依赖的POM，将必要的间接依赖以传递性依赖的方式引入到当前的项目中。

|          | compile  | test | provided | runtime  |
| :------: | :------: | :--: | :------: | :------: |
| compile  | compile  |  -   |    -     | runtime  |
|   test   |   test   |  -   |    -     |   test   |
| provided | provided |  -   | provided | provided |
| runtime  | runtime  |  -   |    -     | runtime  |

上表的左边为第一依赖，上面为第二依赖，表中的结果为传递依赖的最后结果，-表示依赖不传递

#### 依赖调解

对于这样的依赖A>B>C>X(1.0),A>D>X(2.0)，此时D是A的传递性依赖，但是依赖具有两个版本，此时不可以两个版本都解析，这会造成依赖重复，解析的原则如下：

1. 路径近的优先——上面的会优先解析X(1.0)
2. 第一声明者优先——对于路径相同的传递性依赖，A-B-X(1.0)，A-C-X(2.0)，此时B路径的优先声明，因此X(1.0)优先解析
3. 可选依赖是不会传递的

#### 依赖排除，归类，优化

1. exclusions可以排除路径上的某个依赖
2. 如果路径依赖中有同一项目不同模块的依赖，且这部分模块对应的版本相同，那么应该在同一个地方定义这些模块的版本，方便将来升级的时候使用
3. mvn dependency:list可参看已解析的依赖
4. mvn dependency:tree可以查看结构化后的依赖路径
5. mvn dependemcy:analyze分析当前项目的依赖
   1. Used  undeclared dependencies指项目中实际使用到但没有显式声明的，例如很多的java import语句相关的包
   2. Unused declared dependencies 指项目中未使用的，但显式声明的依赖（但不能够直接删除这部分声明，因为analyze只能分析出编译主代码和测试代码需要的依赖）

### 仓库

对于maven，任何一个依赖，插件或者项目构件的输出，都可以称之为构件。得益于坐标机制，maven项目使用任何一个构件的方式都相同，，因此可以在某个位置上统一存储所有Maven项目共享的构件，称该位置为仓库。因此实际的maven项目可以不再各自存储其依赖文件，而通过声明依赖的坐标就可以在仓库中找到并使用这部分构件。

坐标与仓库的坐标的形式具有对应关系：

```
groupId/artifactId/version/artifactId-version.packaging
```

由坐标可以得出文件在仓库中的路径

#### 仓库分类

仓库共分为两类，本地仓库和远程仓库，maven根据坐标查找构件，如果本地有就直接使用本地的构件，如果没有那么就从远端仓库下载到本地仓库再使用。远程仓库又分为中央仓库，私服以及其他公共库。

##### 修改本地仓库

修改.m2/setting.xml文件，添加：

```xml
<localRepository>D:\java\repository\</localRepository>
```

##### 中央仓库

中央仓库是一个远程仓库，一般安装后就会有默认值，处再org/apache/maven/pom-4.0.0.xml中

###### 镜像

镜像可以把对与某一个库的请求全部转发到该镜像上，`<mirrorOf></mirrorOf>`表示镜像某一个库，有以下的方式

`*`表示全部的库

`external:*`匹配除了localhost以外的所有远程库

`repo1,repo2`匹配两个库，用，号隔开

`*,!repo1`匹配所有库，除了repo1

添加阿里云镜像的方式

```xml
<mirror>
      <id>nexus</id>
      <mirrorOf>*</mirrorOf>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
    <mirror>
      <id>nexus-public-snapshots</id>
      <mirrorOf>public-snapshots</mirrorOf>
      <url>http://maven.aliyun.com/nexus/content/repositories/snapshots/</url>
    </mirror>
```

###### 在repository中配置远程仓库

```xml
<repositories>
	<repository>
		<id>jboss</id>
		<name>JBoss Repository</name>
		<url>http://repository.jboss.com/maven2/</url>
		<releases>
			<enabled>true<enabled>
		</releases>
		<snapshots>
			<enabled>false</enabled>
            <updatePolicy>daily</updatePolicy>
            <checksumPolicy>ignore</checksumPolicy>
		</snapshots>
		<layout>default</layout>
	</repository>
</repositories>
```

releases中的enable表示开启发布版本下载支持，snapshot中的enable表示关闭快照版本下载支持，updatePolicy表示更新策略，checksumPolicy表示校验文件

##### 远程仓库认证信息

必须将其写在settings.xml文件夹中

```xml
<servers>
    <server>
        <id>my-proj</id>
        <username>repo-user</username>
        <password>repo-pwd</password>
    </server>
</servers>
```

##### 部署到远程仓库

```xml
<distributionManagement>
    <repository>
        <id>proj-releases</id>
        <name>proj release repository</name>
        <url>http://remote-repository-release</url>
    </repository>
    <snapshotRepository>
        <id>proj-snapshots</id>
        <name>proj snapshots repository</name>
        <url>http://remote-repository-snapshot</url>
    </snapshotRepository>
</distributionManagement>
```

使用命令`mvn clean deploy`可以将构建的输出构建部署到对应的远程仓库，snapshot表示快照版本，在远程快照仓库中其实又多个快照版本，maven会自动为这些版本打上时间戳，其他人在使用该快照时会自动选用最新的版本。

### maven生命周期

clean：清理项目，包含pre-clean,clean和post-clean三个阶段，且三个阶段是前后依赖的，clean执行前pre-clean必执行，而post-clean不执行

default：构建项目--定义了真正执行构建所需的步骤，包括compile,test,install,deploy等步骤都属于这个生命周期

site：建立项目站点

