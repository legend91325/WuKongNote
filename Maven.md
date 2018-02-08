# Maven 笔记

标签（空格分隔）： maven

---
###关键点：
- 约定优于配置
- 插件：目标
- maven默认的生命周期 验证（validation），测试（testing），发布（deployment）。
- Maven坐标 groupId:artifactId:packaging:version，其中groupId:artifactId:version使其成为一个唯一性，packaging是项目类型，默认是jar。
- 通过坐标来定位一个项目，Maven库（公共，私有，本地）
- 第一次使用maven插件时，maven会从远端仓库中取相应的插件并下载下来。中央Maven仓库地址`http://repo1.maven.org/maven2/`。
- Maven仓库的标准是按照下面的目录格式来存储构件，相对于仓库的根目录：
`/<groupId>/<artifactId>/<version>/<artifactId>-<version>.<packaging>`
- mvn install 将项目构件安装到本地库
###Maven依赖管理 (Dependency Management)
- 依赖范围（dependency scope），如果scope为test，则在编译运行时不依赖，在测试时依赖。scope为provide，让它排除WAR文件中特定的依赖。
provided范围告诉Maven一个依赖在编译的时候需要，但是它不应该被捆绑在构建的输出中。
>则可以认为这个provided是目标容器已经provide这个artifact。换句话说，它只影响到编译，测试阶段。在编译测试阶段，我们需要这个artifact对应的jar包在classpath中，而在运行阶段，假定目标的容器（比如我们这里的liferay容器）已经提供了这个jar包，所以无需我们这个artifact对应的jar包了。如：servlet-api的jar在tomcat中已经存在，不用再捆绑输出，否则会冲突的。
compile

1. 默认的scope
    表示 dependency 都可以在生命周期中使用。而且，这些dependencies 会传递到依赖的项目中。适用于所有阶段，会随着项目一起发布
2. provided
跟compile相似，但是表明了dependency 由JDK或者容器提供，例如Servlet AP和一些Java EE APIs。这个scope 只能作用在编译和测试时，同时没有传递性。        
3. runtime
表示dependency不作用在编译时，但会作用在运行和测试时，如JDBC驱动，适用运行和测试阶段。
4. test
表示dependency作用在测试时，不作用在运行时。 只在测试时使用，用于编译和运行测试代码。不会随项目发布。
5. system
跟provided 相似，但是在系统中要以外部JAR包的形式提供，maven不会在repository查找它。
###站点生成和报告 (Site Generation and Reporting)
>mvn site

Site生命周期只关心处理在src/site目录下的site内容，还有生成
报告

##定制一个Maven项目
- `mvn archetype:create -DgroupId= -DartifactId= -DpackageName= -Dversion=`
- `mvn install`
- `mvn exec:java -Dexec.mainClass=   `
>Exec 插件让我们能够在不往classpath载入适当的依赖的情况下，运行这个程序。

    了解你项目的 classpath包含了哪些依赖是很方便也很有用的。
如果你需要找出 classpath 中有什么:

1. 你可以使用`mvn dependency:resolve`插件来打印出已解决依赖的列表。
2. 如果你想知道你项目的整个依赖树，你可以运行 `dependency:tree` 目标。
3. 想要查看完整的依赖踪迹，包含那些因为冲突或者其它原因而被
拒绝引入的构件，打开 Maven 的调试标记运行`mvn install -X`

###执行单元测试
- `mvn test`  运行到 test 阶段为止的所有生命周期阶段
>test 目标执行项目中所有能在 `src/test/java` 找到的并且文件名与 `**/Test*.java`， `**/*Test.java` 和 `**/*TestCase.java` 匹配的所有单元测试。
test 阶段是 Maven 生命周期中常规的一部分。 当你运行 mvn
package 或者 mvn install 的时候你也运行了测试。

Maven 遇到一个测试失败，它默认的行为是停止当前的构建。 如果你希望继续构
建项目，即使 Surefire 插件遇到了失败的单元测试，你就需要设置 Surefire 的
testFailureIgnore 这个配置属性为 true。
```maven
<project>
[...]
    <build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <testFailureIgnore>true</testFailureIgnore>
            </configuration>
        </plugin>
    </plugins>
    </build>
[...]
</project>
```
也可以命令行设置：
`mvn test -Dmaven.test.failure.ignore=true`

###跳过单元测试
`mvn install -Dmaven.test.skip=true`
另一种配置
```maven
<project>
[...]
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <skip>true</skip>
                </configuration>
            </plugin>
        </plugins>
    </build>
[...]
</project>
```

##一个简单的web应用
mvn `archetype:create -DgroupId= -DartifactId= -DpackageName= -DarchetypeArtifactId=maven-archetype-webapp`
与上面不同之处在于后面加上了`-DarchetypeArtifactId=maven-archetype-webapp`参数，用来创建web应用。
正常编译打包好的web应用需要放在web容器中部署运行的，但是maven有提供Jetty插件，可以在maven中运行你的web应用。需要添加插件配置：
```maven
<project>
[...]
    <build>
        <finalName>simple-webapp</finalName>
        <plugins>
            <plugin>
                <groupId>org.mortbay.jetty</groupId>
                <artifactId>maven-jetty-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
[...]
</project>
```
运行命令：`mvn jetty:run`

运行之前还需要编译：`mvn compile`
编译之前还需要添加servlet和jsp依赖项，同时记得添加`<scope>provided</scope>`，因为真正发布的时候，web容器中已经有这依赖项了，不用要打包出去。

##一个多模块项目
一个多模块项目通过一个父POM引用一个或多个子模块来定义
父模块中添加
```maven
<modules>
    <module></module>
    <module></module>
    ...
</modules>
```
同时可以再父模块中定义一些子模块都需要继承的设置。
子模块需要再拍张中加入：
```maven
<parent>
    <groupId></groupId>
    <artifactId></artifactId>
    <version></version>
</parent>
```
来指定相应的父模块。

构建时需要从父模块开始构建，因为有子模块所依赖的项：`mvn clean install`
>当Maven执行一个带有子模块的项目的时候，Maven首先载入父POM，然后定位所有的子
模块POM。Maven然后将所有这些项目的POM放入到一个称为Maven 反应堆（Reactor）的
东西中，由它负责分析模块之间的依赖关系。这个反应堆处理组件的排序，以确保相互
独立的模块能以适当的顺序被编译和安装。

对于`mvn clean install`的解释：
>当你在命令行运行Maven的时候你经常会在任何其它生命周期阶段前指
定clean生命周期阶段。当你指定clean，你就确认了在编译和打包一个应用
之前，Maven会移除旧的输出。运行clean不是必要的，但这是一个确保你正
执行“干净构建”的十分有用的预防措施。

##多模块企业级项目

注意事项：

>一个常见的错误是使用extensions配置添加一个插件需要的依赖。这是强烈
不推荐的因为extensions会在你的项目中造成classpath污染，以及其它令
人讨厌的副作用。此外，extensions行为正在2.1中被重做，最后你都会要
改变它。唯一的对extensions的正常使用是定义新的wagon实现。

**个人还不太理解这里，留下来后续深入了解**

`mvn assembly:assembly`
构建过程经过了生命周期中编译字节码，运行测试，然后最终为该项目构建一个JAR。
然后assembly:assembly目标创建一个带有依赖的JAR，它将所有依赖解压到一个临时
目录，然后将所有字节码收集到target/目录下一个名为simple-command-jar-withdependencies.
jar的JAR中。这个“超级的”JAR的体重为15MB。

##优化和重构POM

1. **上移共同的依赖至dependencyManagement**
如果多于一个项目依赖于一个特定的依赖，你可以在dependencyManagement中
列出这个依赖。父POM包含一个版本和一组排除配置，所有的子POM需要使
用groupId和artifactId引用这个依赖。如果依赖已经在dependencyManagement中列出，子项目可以忽略版本和排除配置。
2. **为兄弟项目使用内置的项目version和groupId**
使用{project.version}和org.sonatype.mavenbook来引用兄弟项目。兄弟项目
基本上一直共享同样的groupId，也基本上一直共享同样的发布版本。使用0.6-
SNAPSHOT可以帮你避免前面提到的兄弟版本不一致问题。

`mvn dependency:analyze`
Maven Dependency插件能够帮助你发现对于依赖的直接引用。使用我们之前
优化过的新的POM，让我们看一下是否会有错误突然出现,因为maven支持传递依赖，所有有些依赖没有直接引用但是在没部署之前依旧可以运行。该命令可以分析出有哪些需要直接引用，但没有直接声明的**以及**声明了没有使用
`dependency:tree`目标，该目标会列出项目中所有的直接和传递性依赖

##项目对象模型
Maven开始于超级POM，然后使用一个或多个父POM覆盖默认配置，最后使用当前
项目的POM来覆盖之前生成的配置结果。最后你得到了一个混合了各个POM配置的有效POM。如果你想要查看项目的有效POM，你需要运行这个命令来查看最终合并后有效的POM。
`mvn help:effective-pom`
###项目版本
标准格式：
`<major version>.<minor version>.<incremental version>-<qualifier>`
如果版本依据正确格式书写，会被正确比较，否则只会根据字符串比较。

如果一个版本包含字符串“SNAPSHOT”，Maven就会在安装或发布这个组件的时候将该符号展开为一个日期和时间值，转换为UTC（协调世界时）。换句话说，当你发布一个snapshot，你没有发布一个软件模块，你只是发布了一个特定时间的快照版本。  
###属性引用
一个POM可以通过一对大括弧和前面一个美元符号来包含 对属性的引用。
###依赖范围

1.  **compile（编译范围）**
compile是默认的范围；如果没有提供一个范围，那该依赖的范围就是编译范
围。编译范围依赖在所有的classpath中可用，同时它们也会被打包。
2. **provided（已提供范围）**
provided依赖只有在当JDK或者一个容器已提供该依赖之后才使用。例如，如果
你开发了一个web应用，你可能在编译classpath中需要可用的Servlet API来编
译一个servlet，但是你不会想要在打包好的WAR中包含这个Servlet API；这个
Servlet API JAR由你的应用服务器或者servlet容器提供。已提供范围的依赖在编译classpath（不是运行时）可用。它们不是传递性的，也不会被打包
3. **runtime（运行时范围）**
runtime依赖在运行和测试系统的时候需要，但在编译的时候不需要。比如，你
可能在编译的时候只需要JDBC API JAR 而只有在运行的时候才需要JDBC驱动实
现
4. **test（测试范围）**
test范围依赖 在一般的 编译和运行时都不需要，它们只有在测试编译和测试运行阶段可用。。
5. **system（系统范围）**
system范围依赖与provided类似，但是你必须显式的提供一个对于本地系统中
JAR文件的路径。这么做是为了允许基于本地对象编译，而这些对象是系统类库
的一部分。这样的构件应该是一直可用的，Maven也不会在仓库中去寻找它。如
果你将一个依赖范围设置成系统范围，你必须同时提供一个systemPath元素。注意该范围是不推荐使用的（你应该一直尽量去从公共或定制的Maven仓库中引用依赖）。

###依赖版本界限
你可以使用如下的字符来围绕一个或多个版本号，来实现版
本界限。
(, )
不包含量词
[, ]
包含量词
例如：<version>[3.8,4.0)</version> 为大于等于3.8版本小于4.0版本。

在逗号前面或者后面的版本不是必须的，这种空缺意味着正无穷或者负无穷。

###冲突解决
排除了一个传递性依赖
```maven
<dependency>
    <groupId>org.sonatype.mavenbook</groupId>
    <artifactId>project-a</artifactId>
    <version>1.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.sonatype.mavenbook</groupId>
            <artifactId>project-b</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```
###依赖管理
`<dependencyManagemen></dependencyManagemen>`用来在顶层配置依赖项的版本，子项目依旧配置依赖项但不指定版本，统一使用顶层`dependencyManagemen`中指定的版本。

###坐标详解
- groupId
- artifactId
- version
    三者构成一个唯一项目

- classifier 分类器
如果你要发布同样的代码，但是由于技术原因需要生成两个单独的构件，你就
要使用一个分类器（classifier）。

###依赖归类
如果你需要在项目间共享一组很多的依赖，你也可以建立在项目间建立
父子关系，然后将所有共同的依赖重构到父项目中，但是这种父子方式的缺点是一个项
目只能有一个父项目。有时候将类似的依赖归类在一起并且使用pom依赖是更明智的做
法。因为这样你的项目就可以根据需要引用很多巩固依赖POM。