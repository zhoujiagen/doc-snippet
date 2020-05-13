# Gradle

##  资源
- Gradle Build Language Reference Version 4.6: https://docs.gradle.org/4.6/dsl/
- Gradle User Manual Version 4.6: https://docs.gradle.org/4.6/userguide/userguide.html
- Gradle Tutorials and Guides: https://gradle.org/guides/

##  Installation
https://gradle.org/install/

```
brew update
brew install gradle
brew info gradle # 查看安装信息
```

Eclipse Plugin
http://www.vogella.com/tutorials/EclipseGradle/article.html

##  Tutorial

[Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/):

```
> gradle init

├── build.gradle - 当前项目中配置任务的脚本
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── settings.gradle - 配置构建中参与构建的项目
```


[Building Java Libraries](https://guides.gradle.org/building-java-libraries/)


build.gradle中设置使用oschina的maven库

```
repositories {
    maven { url 'http://maven.oschina.net/content/groups/public/' }
}
```


[Dependency Management for Java Projects - java-library插件, 依赖, 仓库, 发布](https://docs.gradle.org/current/userguide/dependency_management_for_java_projects.html)

[Managing Dependency Configurations - 管理依赖配置](https://docs.gradle.org/4.6/userguide/managing_dependency_configurations.html)

Defining custom configurations - 自定义配置，例：[The Java Plugin](https://docs.gradle.org/4.6/userguide/java_plugin.html#sec:java_plugin_and_dependency_management)

[Publishing artifacts - original,  incubating(Ivy, Maven) - 发布制品](https://docs.gradle.org/4.6/userguide/artifact_management.html)

[Executing Multi-Project Builds - 多项目构建](https://docs.gradle.org/4.6/userguide/intro_multi_project_builds.html)


##  配置

- inheritance for projects - 项目继承
- configuration injection - 配置注入
- a common external script - 共用的外部脚本

配置依赖(项目间), 执行依赖(任务依赖)

- Configuration dependencies are very different from execution dependencies.
- Configuration dependencies are between projects whereas execution dependencies are always resolved to task dependencies.

##  Java, Maven插件

[Gradle Java Quickstart](https://docs.gradle.org/4.6/userguide/tutorial_java_projects.html)

- The Java Plugin
- The Java Library Plugin
- The Java Library Distribution Plugin - incubating
- Dependency Management for Java Projects - Java项目的依赖管理


[The Maven Plugin](https://docs.gradle.org/4.6/userguide/maven_plugin.html)

##  projects, tasks, properties
Gradle API

### org.gradle.api.Project

build script configuration
dependency management
properties getter/setter
file creation
task creation

### org.gradle.api.Task: stask actions, task dependencies
task dependencies
action definition
input/output data declaration
properties getter/setter

- 默认实现:　DefaultTask
属性: logger, group, description

- ProjectVersion 管理项目版本
- 声明Task action: doFirst(Closure), doLast(Closure)
可以向已有Task添加action
- 定义Task依赖: dependsOn
- Finalizer Task: a.finalizedBy b, 执行a会触发b执行
- 在构建脚本中添加通用的Groovy代码
- Task配置块: 总在Task action之前执行

Gradle构建的生命周期阶段

- (1) initialization:  创建Project实例
- (2) configuration: 构建Task表示的模型
- (3) execution: 按序执行Task

Task的输入和输出: TaskInputs, TaskOutputs

在配置阶段求值

- 自定义Task: enhanced task

```
task type, actual type
extends DefaultTask
@Input, @OutputFile, @TaskAction
```

- 内建的Task类型
Zip, Copy
Task依赖推断: 一个Task的输出作为另一个Task的输入

- Task规则
基于Task名称模式执行特定的逻辑
Project.getTasks(), TaskContainer.add(name, Closure), NamedDomainObjectCollection.addRule(description, Closure)

- 构建自定义代码的存放buildSrc
该目录作为Gradle Project处理

properties

- extra properties: extra namespace
- gradle properties: gradle.properties in ~/.gradle or project home
- command line: -P project-property, -D system-property
- environment: ORG_GRADLE_PROJECT_propertyName=someValue

##  Gradle build lifecycle

构建生命周期钩子(hook):<br>
生命周期事件可以在特定构建阶段的before, during, after出现.<br>
构建生命周期事件回调的实现: Closure, linstener接口.<br>
内部的Task图表示: TaskExecutionGraph, 一个DAG

- Task图钩子: gradle.taskGraph.whenRead {TaskExecutionGraph graph => ...}
- Task图listener: TaskExecutionGraphListener

```
Gradle#addListener(), TaskExecutionGraph#addTaskExecutionGraphListener()
```

- 初始化构建环境
~/.gradle/init.d/


##  依赖管理
### 依赖配置

```
Project#configurations(Closure)
ConfigurationContainer, Configuration
```

### 声明依赖

依赖的类型: 外部模块依赖, 项目依赖, 文件依赖, 客户端模块依赖, Gradle运行时依赖

```
Project#getDependencies() => DenpencyHandler, Dependency
```

依赖的属性: group, name, version, classifier

### repository

```
Project#repositories(Closure) => RepositoryHandlers, ArtifactRepository(FlatDirectory, Maven, Ivy)
```

### 本地依赖缓存
存储依赖的来源, 校验checksum, 比较依赖的大小
--offline 离线模式

### 依赖冲突
默认策略:　选择最新的版本

```
configuration.xxx.resolutionStrategy {
  failOnVersionConflict() // 存在冲突时失败
  force '...' // 强制使用依赖的一个版本
  cacheDynamicVersionFor 0, 'seconds' // 不缓存动态版本, 即group:name:1.+
  cacheChangingModulesFor 0, 'seconds' // 不缓存快照版本
}

// 查看冲突的来源
gradle dependencyInsight --confuguration xxx --dependency <group:name>

// 手动刷新本地缓存
gradle --refesh-dependencies
```


##  samples

sample `build.gradle`

```
    apply plugin: 'java' // Java插件
    apply plugin: 'war' // War插件
    apply plugin: 'jetty' // Jetty插件, 内嵌Web容器

    version = 0.1 // 项目版本号
    sourceCompatibility = 1.6 // Java版本

    jar {
      manifest { // 设置jar包中启动类
        attributes 'Main-Class': 'com.manning.gia.todo.ToDoApp'
      }
    }

    // 重新指定代码目录
    sourceSets {
      main {
        java {
          srcDirs = ['src']
        }
      }
      test {
        java {
          srcDirs = ['test']
        }
      }
    }
    // 重新指定构建输出目录
    buildDir = 'out'


    //代码仓库
    repositories {
      mavenCentral() // Maven: http://repo1.maven.org/maven2
    }

    // 指定依赖
    dependencies {
      compile group: 'org.apache.commons', name: 'commons-lang3', version: '3.1'
      providedCompile 'javax.servlet:servlet-api:2.5'
      runtime 'javax.servlet:jstl:1.1.2'
    }


    // Web应用目录
    webAppDirName = 'webfiles'
    // 配置war插件
    war {
      from 'static' // Web资源目录
    }

    // 配置Jetty插件
    jettyRun {
      httpPort = 9090
      contextPath = 'todo'
    }

    dependencies {
    compile group: 'org.apache.commons', name: 'commons-lang3', version: '3.1'
    }
```
