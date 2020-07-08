# Scala和maven

**版权说明：** 
作者： Adrian Null

**原始链接：**
Scala官方文档站点：
https://docs.scala-lang.org/tutorials/scala-with-maven.html

**翻译作者：**
[@TopCaver](https://github.com/TopCaver)

**特别声明：**
限于译者的英语水平和技术能力，翻译中必然存在错误，请及时阅读上述链接原文。

| 翻译版本 | 日期       | 贡献者               | 更新说明 |
| -------- | ---------- | -------------------- | -------- |
| 1.0      | 2020-07-08 | ZhangBo（[@TopCaver](https://github.com/TopCaver)） | 新建     |

[TOC]
---

# 介绍

[Maven][1] 是一个管理/构建项目的工具。它遵循“约定优于配置”；可以非常简单的构建标准工程，而且Maven的用户，可以非常容易通过 `pom.xml`文件 (Project Object Model)立即另外一个Maven项目的结构。Maven是一个基于插件的架构，向现存的项目添加新的库或者模块都非常简单。例如，新增一个依赖，通常只要在`pom.xml`中添加5行代码。这些“组件”就会从仓库，例如 [The Central Repository][2] 上下载下来。

你也可以使用下面介绍的Scala插件，构建一个官方 [示例项目][36] 

# 跳过本文
如果你很熟悉Maven，可以直接访问Scala Maven 插件站点 [Scala Maven Plugin][5].

# Scala Maven Plugin
我们使用Scala Maven Plugin  ([GitHub repo][5], [website][22]) (之前知名的maven-scala-plugin，重命名是因为maven新的命名规则，只允许Maven核心插件使用“maven”插件。)， Scala项目的必选插件。*Note：插件从中央仓库引入了Scala，所以如果你使用Maven编译项目，并不需要自己安装Scala。*


# 安装 Maven

## Linux (Debian)
Debian 和派生的其他发行版，通常使用`apt-get`安装Maven。只需要运行 `(sudo) apt-get install maven` 就可以了。

## OSX
OSX在10.9 (Mavericks) 之前是自带Maven3的。
如果没有，你可以使用包管理[MacPorts][4]， [Homebrew][6]，或者 [Fink][7] 安装Maven。
Scala Maven插件需要依赖Mave 3.0以上版本。 


## 手动安装 (Red Hat Linux, OSX, Windows)
你可以从 [Apache官网][3]下载Maven。然后解压 (`tar -zxvf apache-maven-X.X.X-bin.tar.gz`, or use something like [7-zip][8]) 到自定义的目录（Linux或者OSX， [我喜欢放在 `/opt/`][9]。 Windows下面我习惯放在`C:/`）， 然后将Maven路径写入到Path环境变量中：

- Linux/OSX (方案1): 创建符号连接到 `/usr/bin`,，因为这个目录已经在path中了。
    - `ln -s /usr/bin/mvn /opt/apache-maven-X.X.X/bin/mvn`
- Linux/OSX (方案2): 将 `bin` 目录放到path变量中， 修改你的[shell 配置][10] 文件 (e.g. `~/.bash_profile`)
    - 添加 `export PATH=$PATH:/opt/apache-maven-X.X.X/bin` 到 `.bash_profile` (或者你用的shell的配置文件里面)
    - 例如: `echo "export PATH=$PATH:/opt/apache-maven-X.X.X/bin" >> ~/.bash_profile`
- Linux/OSX (方案 3): 把 `mvn` shell 脚本放到现存的path路径下
	- 例如: 你的path中包含了 `$HOME/bin` 
	- 把解压的maven目录放到 `$HOME/bin` 下面(`mv apache-maven-X.X.X "$HOME/bin/"`)
	- 在`$HOME/bin` 创建一个 `mvn`文件 
	- 添加 `"$HOME/bin/apache-maven-X.X.X/bin/mvn" $@` 到mvn文件，然后修改文件属性，给脚本执行权限：`chmod u+x mvn`。
	- 这个可能是个最少侵入的方法；`$HOME/bin`通常是默认加入到用户的path中的。shell脚本只是简单的调用Maven所在的路径并且把参数传递过去。
- Windows
    - 点击开始，右键 "我的电脑" 然后找到"属性"
    - 这会引导至 "控制面板 -> 系统和安全 -> 系统", 展示一个系统信息
    - 在左边栏中会出现几个选项；点击"高级系统设置" (通常是第四个)
    - 在 "高级"选项卡下面, 点击 "环境变量..."
    - 注意: 我建议是创建/修改自己的用户变量（上半部分）。也可修改系统变量（下半部分）I 
    - 创建一个新的变量，名叫 "MAVEN3_HOME"。指向你Maven的文件夹 (例如 `C:\apache-maven-X.X.X`)，使用反斜线，并且不需要结尾的斜线。 
    - 创建一个新的变量 "MAVEN3_BIN" 值为: `%MAVEN3_HOME%\bin`
    - 修改 Path 变量: 不要修改前面的任何配置，只是把maven的配置 `;%MAVEN3_BIN%` 添加到最后面。注意分号。
    - 重新打开命令行工具，这些配置就应该生效了。

# 创建第一个项目
最简单的建立新工程的方法是使用["archetype"][11]。一个archetype是一个结构股价，或者工程模板。回想一下“约定优于配置”，这里Scala Maven Plugin提供了一个Scala项目的archeytpe。

运行archetype插件：

```bash
mvn archetype:generate -DarchetypeGroupId=net.alchim31.maven -DarchetypeArtifactId=scala-archetype-simple
```

在第一次运行时， Maven会下载很多jar文件。Maven解析依赖并下载所需的文件（只会下载一次）。现在，Maven正在下载核心插件。

之后，Maven提示你给出groupid，artifactid，和package。详情可以阅读[guide to naming conventions][12]，简单说：

- groupId: 域名的翻转字符串  (例如： com.my-organization)
- artifactId: 项目名字 (例如： playground-project)
- version: 版本，可以随意填写，但是强烈建议遵循相关规范 [Semantic Versioning][13] (例如  0.0.1)
- package: 包名，默认是groupId，你也可以修改。

groupId 和 artifactId组合起来，应该是全球唯一的可以标识你的项目的标识。

填写完毕，你应该得到一个以artifactid命名的新的文件夹。 `cd`进入，并执行:

```bash
mvn package
```

你会看到Maven下载依赖，包括之前提到的Scala库文件，[JUnit][14], [ScalaTest][15], 和 [Specs2][16]（这三个是测试框架；archetype包括一个“Hello World”示例程序，以及每个测试框架的测试用例）。

## archetype解释
在项目的根目录，你可以看到`pom.xml`, `src` 文件夹,，和 `target` 文件夹（target文件夹只有在build之后才会出现）。*提示：archetype还包括 `.gitignore`*

在`src`文件夹，你会看到`main`和`test`；`main` 里面是你的应用代码， `test` 里面是你的测试用例。每个之中，都包括一个`scala` 文件夹，里面是你的包结构。(实际上, `test/scala` 包括了一个示例包，但是你应该用自己的包和测试用例替代它)。如果你需要混合Scala和Java的源代码，只需要在 `main` or `test`文件夹下面，创建`java`文件夹。

`target` 包括生产的文件，例如：`.class` 和 `.jar`文件。你可以从 [Maven page][18]更详细的了解 `pom.xml`。

项目示例结构:

- pom.xml
- src
    - main
        - scala
            - com/my-package/...
                *.scala
        - java
            - com/my-package/...
                *.java
    - test
        - scala
            - com/my-package/...
                *.scala
        - java
            - com/my-package/...
                *.java
- target
    ...

再次强调，你可以通过 [Scala Maven Plugin 网站][22] 进一步了解这个插件。

## 创建 Jar
Scala Maven Plugin默认创建的jar文件，manifest不包括`Main-Class` 属性。我使用了另外一个插件 [Maven Assembly Plugin][19] 到`pom.xml`，以便在manifest中指定自定义的属性。你可以从[项目介绍][20] 或者 [中央仓库][21]中查看插件当前最新的版本。

```xml
<project ...>
    <modelVersion>X.X.X</modelVersion>
    ...
    <licenses>
        ...
    </licenses>

    <properties>
        ...
    </properties>

    <dependencies>
        ...
    </dependencies>

    <build>
        ...
        <plugins>
            ...
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>2.4</version>
                <configuration>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <archive>
                        <manifest>
                            <mainClass>com.your-package.MainClass</mainClass>
                        </manifest>
                    </archive>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```
添加这个之后，`mvn package`会在`target`另外创建一个`[artifactId]-[version]-jar-with-dependencies.jar`。 *注意：这也会复制Scala库到jar里面。这是正常的。要注意你的依赖使用相同版本的Scala，否则你会得到一个巨大的jar包*

## 常用命令
- `mvn dependency:copy-dependencies`: 复制全部库和依赖到 `target/dependency` 目录
- `mvn clean`
- `mvn package`: 编译、测试、构建Jar包

## 与Eclipse集成 ([Scala IDE][24])
在[Scala Maven Plugin FAQs][23]里有介绍，这里再扩充一下。[maven-eclipse-plugin][33]是生成Eclipse配置文件的核心插件（所有maven开头的插件都是核心插件，而且默认可以用）。但是，这个插件无法获知新的Scala原文件。我们需要使用[build-helper-maven-plugin][34]插件添加新的源代码文件夹：

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>build-helper-maven-plugin</artifactId>
    <executions>
        <execution>
            <id>add-source</id>
            <phase>generate-sources</phase>
            <goals>
                <goal>add-source</goal>
            </goals>
            <configuration>
                <sources>
                    <source>src/main/scala</source>
                </sources>
            </configuration>
        </execution>
        <execution>
            <id>add-test-source</id>
            <phase>generate-sources</phase>
            <goals>
                <goal>add-test-source</goal>
            </goals>
            <configuration>
                <sources>
                    <source>src/test/scala</source>
                </sources>
            </configuration>
        </execution>
    </executions>
</plugin>
```

After adding that to your `pom.xml`:
添加到 `pom.xml`之后：
1. 运行 `mvn eclipse:eclipse` - 这会生成Eclipse的项目文件。 (这些文件也会被archetype的`.gitignore`忽略)
2. 运行 `mvn -Declipse.workspace="path/to/your/eclipse/workspace" eclipse:configure-workspace` - 添加 `M2_REPO` classpath 变量到Eclipse
3. 运行 `mvn package`确保所有依赖已经下载到本地Maven仓库

不幸的是，集成并不是十分完美。首先是 .classpath 文件（虽然 . 开头的文件默认会被隐藏，但是它应该在你项目的根目录，也是你运行`mvn eclipse:eclipse`的目录）。你可以看到类似这样的内容：

```xml
    <classpathentry kind="src" path="src/test/scala" output="target/test-classes" including="**/*.java"/>
    <classpathentry kind="src" path="src/main/scala" including="**/*.java"/>
```

修改 `*.java` 为 `*.scala` (或者复制一行，写上 `*.scala`，如果你同时需要Java和Scala的话)。

第二步，打开 `.project` eclipse文件 (在同一个目录下)。 修改 `<buildSpec>` 和 `<natures>` 成下面这样。这样Eclipse才知道如何使用Scala编辑器，才不会认为Scala代码全是语法错误。

```xml
    <buildSpec>
      <buildCommand>
        <name>org.scala-ide.sdt.core.scalabuilder</name>
      </buildCommand>
    </buildSpec>
    <natures>
      <nature>org.scala-ide.sdt.core.scalanature</nature>
      <nature>org.eclipse.jdt.core.javanature</nature>
    </natures>
```

最后， 在Eclipse中，选择"File" - "Import..." 导入你的工程。

### 使用 [m2eclipse-scala][35] 与 Eclipse 集成
m2eclipse-scala还在开发中，网站和仓库可能会有新的更新。它的目标是为了Eclipse更容易集成m2ecplise和Scala IDE。

在 "Help -> Install New Software", 输入 "https://alchim31.free.fr/m2e-scala/update-site" 回车。

你会看到 "Maven Integration for Eclipse -> Maven Integration for Scala IDE".

安装之后，点击 "New -> Project -> Other" 然后选择 "Maven Project"。
找到  "scala-archetype" ，选择"net.alchim31.maven" 组下面的一个。引导程序就会帮助你完成`mvn archetype:generate`过程，最后你会得到一个全新的Scala 工程！

如果要import已经存在的工程，只需要 "File -> Import -> Existing Maven Project" 然后找到包含你的项目的目录。

# 添加依赖

添加依赖的第一件事，就是在项目首页查找“Maven”。例如， 在Google的 Guava项目页面包含了 [Maven Central links][28]的连接。这里包含了一段代码片段，是你需要加入到`pom.xml`文件中的部分。

如果你在项目主页找不到Maven信息，尝试去google搜索一下“项目名称 maven”。有时候可能Google也搜不到。比如[scopt][29] (Scala命令行参数解析器)，我就搜不到新版本。但是可以[在中央仓库手动查找][30]。

之后，运行

```bash
    mvn package
```

Maven会在打包之前自动下载需要的依赖。

# 其他参考资料
本篇教程没有介绍全部的Maven（虽然未来可以扩充一下，因为我发现网上的资料有些零散），这里有一些有用的文章：
- [Maven Lifecycle][17] (解释了Maven的一些过程，比如： clean, package, install)


[1]: https://maven.apache.org
[2]: https://maven.org
[3]: https://maven.apache.org/download.html
[4]: https://macports.org
[5]: https://github.com/davidB/scala-maven-plugin
[6]: https://brew.sh
[7]: https://fink.sf.net
[8]: https://7-zip.org
[9]: https://unix.stackexchange.com/questions/63531/
[10]: https://wikipedia.org/wiki/Unix_shell#Configuration_files_for_shells
[11]: https://maven.apache.org/archetype/maven-archetype-plugin/
[12]: https://maven.apache.org/guides/mini/guide-naming-conventions.html
[13]: https://semver.org
[14]: https://junit.org
[15]: https://scalatest.org
[16]: https://specs2.org
[17]: https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html
[18]: https://maven.apache.org/pom.html
[19]: https://maven.apache.org/plugins/maven-assembly-plugin/
[20]: https://maven.apache.org/plugins/maven-assembly-plugin/summary.html
[21]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22maven-assembly-plugin%22
[22]: https://davidb.github.io/scala-maven-plugin
[23]: https://davidb.github.io/scala-maven-plugin/faq.html
[24]: https://scala-ide.org
[25]: https://scala-lang.org/api/current/index.html#scala.App
[26]: https://docs.scala-lang.org/tutorials/tour/polymorphic-methods.html
[27]: https://code.google.com/p/guava-libraries/
[28]: https://search.maven.org/#artifactdetails%7Ccom.google.guava%7Cguava%7C14.0.1%7Cbundle
[29]: https://github.com/scopt/scopt
[30]: https://search.maven.org/#search%7Cga%7C1%7Cscopt
[31]: https://mvnrepository.com
[32]: https://docs.scala-lang.org/contribute.html
[33]: https://maven.apache.org/plugins/maven-eclipse-plugin/
[34]: https://www.mojohaus.org/build-helper-maven-plugin/
[35]: https://github.com/sonatype/m2eclipse-scala
[36]: https://github.com/scala/scala-module-dependency-sample
