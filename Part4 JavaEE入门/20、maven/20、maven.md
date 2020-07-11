## 1、maven概述

> Maven 是一个项目管理工具，它包含了一个项目对象模型 (POM：Project Object Model)，一组标准集合，一个项目生命周期(Project Lifecycle)，一个依赖管理系统(Dependency Management System)，和用来运行定义在生命周期阶段(phase)中插件(plugin)目标(goal)的逻辑。

- 依赖管理：maven 对 jar 包的管理。
  - maven 开发的 web 项目， jar 包不在项目组，而是在 jar 包仓库中。maven 通过 jar 包的坐标（在 pom.xml中）索引到 jar 包。
- 一键构建：
  - 指的是项目从编译、测试、运行、打包、安装 ，部署整个过程都交给maven进行管理。
- 环境变量：
  - 配置系统变量：`MAVEN_HOME`为maven的安装目录。
  - 在 path 中添加：`%MAVEN_HOME%\bin`。
  - 测试：命令行输入 `mvn -v`。
- maven 仓库：
  - 仓库分为：本地仓库、远程仓库、中央仓库。
  - 本地仓库路径：在 conf / settings.xml 文件中的 localRepository 标签。
  - 如果本地仓库没有，会从 maven 的中央仓库下载，其包含了几乎所有开源的 jar 包。
  - 在项目协作时，有时会建立远程仓库，用于局域网内的共享。远程仓库可以由本地仓库上传，也可以由中央仓库下载。
- maven 标准目录结构：
  - Java 项目主要分为：核心代码部分、配置文件部分、测试代码部分、测试配置文件部分。对于 web 项目还有前端资源部分。
  - src / main / java 目录下：核心代码部分。
  - src / main / resources 目录下：配置文件部分。
  - src / test / java 目录下：测试代码部分。
  - src / test / resources 目录下：测试配置文件部分。
  - src / main / webapp 目录下：页面前端资源。
- maven 常用命令（命令行下）：
  - `mvn compile`：生成 target 目录。
    - 编译了项目的核心代码部分，在 target / classes 下。
  - `mvn clean`：删除 target 目录。
    - 删除之前编译的信息。
  - `mvn test`：编译测试代码部分，在 target / test-classes 下。
    - 同时也编译了项目的核心代码部分。
  - `mvn package`：将项目打包。
    - 在打包前先编译了核心和测试代码部分。
    - 打包的文件格式，定义在 pom.xml 文件下的 packaging 标签下。
  - `mvn install`：将项目安装到了 maven 的本地仓库。
    - 先将项目打包并编译了核心和测试代码部分。
  - `mvn deploy`：发布项目，需要先进行配置。
- maven 生命周期：
  - 默认生命周期：编译（compile）-> 测试（test）-> 打包（package）-> 安装（install）-> 发布（deploy）。
  - 清理生命周期：清除项目编译信息（clean）。
  - 站点生命周期。
- maven 概念模型：
  - 项目对象模型（POM）：pom.xml，包含了项目自身信息、项目运行所依赖的 jar 包信息、项目运行环境信息（JDK、Tomcat）。
  - 依赖管理模型：项目运行所依赖的 jar 包的坐标，至少包括 jar 包组织的名称、项目名和版本号。
  - 默认生命周期，每一个构造项目的命令都对应一个插件。

## 2、IDEA集成

- 集成 maven 插件：

  - Maven 设置中：Maven 安装目录、settings 文件位置和本地仓库位置。
  - Runner 下 VM-Options配置：-DarchetypeCatalog=internal。可以从本地找到之前下载过的骨架。

- 使用骨架创建 maven 工程：

  - 选择骨架，例如  maven-archetype-quickstart 。
  - 填写当前项目的坐标。
  - 需要手动补全目录结构。
  - 设置目录类型，将核心代码文件设置为 Sources Root，将测试代码设置为 Test Sources Root，将核心代码资源文件设置为 Resources Root，将测试代码资源文件设置为 Test Resources Root。

- 不使用骨架创建 maven 工程：不选择骨架，其他相同。

- 使用骨架创建 maven 的 web 工程：

  - 选择骨架 maven-archetype-webapp 。

- 指定 web 资源包：

  - 只有 web 资源下才能创建 web 资源如 jsp。
  - 在 project Structure -> Modules 的左侧， + 号添加 web 框架。然后在下方设置 web 资源路径为 webapp。

- 导入项目依赖的 jar 包：在 pom.xml 中。

  - 在 dependencies 标签下的 dependency 标签内设置。

  - 例如导入 servlet 包：

    ```xml
    <dependencies>
      <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
      </dependency>
    </dependencies>
    ```

  - 从中央仓库下载 jar 包：

    - 在中央仓库中搜索所需要的 jar 包。
    - 复制配置信息到 pom.xml 中。

  - jar 包冲突：

    - 因为导入的 jar 包与 tomcat 依赖的 jar 包同名导致冲突。
    - 在 dependency 标签内设置作用域为`<scope>provided</scope>` 。

- 运行环境修改：

  - maven 默认 tomcat6 插件，如果要配置 tomcat7 插件：

    - 配置插件版本号和端口号。

    ```xml
    <build>
        <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.2</version>
            <configuration>
                <port>8888</port>
            </configuration>
        </plugin>
        </plugins>
    </build>
    ```

  - 运行时不再输入 `tomcat:run`，而是输入 `tomcat7:run`。

  - 配置 JDK ：

    -  配置插件版本号。配置编译和运行时的JDK版本以及编码。

    ```xml
    <build>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.7.0</version>
              <configuration>
                <target>1.8</target>
                <source>1.8</source>
                <encoding>UTF-8</encoding>
              </configuration>
        </plugin>
        </plugins>
    </build>
    ```

  - 配置动态模板：

    - settings -> live Templates 下。创建模板组。
    - 在 abbreviation 中填写缩写，在下方写入模板内容。

- maven 依赖范围：

  - 也就是 jar 包的作用域，在在 dependency 标签内的 scope 标签内。
  
  | 依赖范围 | 编译有效 | 测试有效 | 运行有效 | 例                 |
  | -------- | -------- | -------- | -------- | ------------------ |
  | compile  | Y        | Y        | Y        | spring-core        |
  | test     | -        | Y        | -        | Junit              |
  | provided | Y        | Y        | -        | servlet-api        |
  | runtime  | -        | Y        | Y        | JDBC驱动           |
  | system   | Y        | Y        | -        | 本地仓库以外的类库 |
  
  - compile 为默认范围。provided 是指该依赖在运行时由 JDK 或其他容器（如Tomcat）提供，只在编译时防止报错。runtime 是指编译时不需要，运行时需要。system 需要提供显式的文件路径。




