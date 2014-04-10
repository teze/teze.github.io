
#Gralde for Android

 _Written by Fooyou <foyou23@qq.com>_
 
    2014/3/27 

----
目录
----
[TOC]

------------------------
（What？） 什么是gradle？
------------------------

>**`gradle`**是一个自动化构建系统，可以集成自动编译，测试，打包，发布，部署功能，还有其他一些软件工程管理功能（比如：生成静态网站，生成文档啊，等等）。

--------------------------
（Why？）为什么要有gradle？
--------------------------

### 1. 自动打包测试部署
> 做为一个项目开发人员，你是不是特别希望代码敲好了，有这样一个机器人，只要你一条指令，能够自动帮你编译，测试bug啊，打包啊 一系列有的没的。。自个可以泡一杯茶，刷刷微博，看看美女图片啊。。。有木有？反正我是有！

### 2. 打渠道包
> * 做为Android开发的你，如果你的打包发布流程中，没有 “打渠道包” 这一个奇葩需求的话，那我就想偷偷笑你一下，你的项目太烂 太小了！对，没错，应用发布，不可能只发布到一个应用市场（在国内有n多乱七八糟的应用市场），你的营运需要统计各个市场的下载量，用户数量。。。等等一系列数据，这个时候区分不同的市场就很有统计意义了（我们称其为“不同渠道”）。
* Android开发中，通常我们打渠道包都是在manifest文件中配置不同的渠道参数，虽然在市面上有不少现成的工具比如：CNZZ的打包工具，友盟的打包工具，这些工具都能帮你打出渠道包，但是也只能局限他们的工具，换一个字符，换一个参数，就搞不了了，如果不嫌累，你可以手动修改manifest文件，然后重新编译，我想没几个人愿意这么干，现在有了 **`gradle`** 能够帮你解决上面描述的问题。

### 3. 定制版本发布，特定需求版本发布
> 有时候会这样的需求：我想针对某个平台，或者某个厂商发布一个定制版本的软件，而这些特定的功能，不需出现在普通版中。**比如：我想同时发布一个收费版本和免费版本**，**再比如：我想发布一个单独设备上面跑的版本** 这个时候**`gradle`**能够很方便的管理不同的源码，发布不同的版本。

### 4. 还有更多其他需求 ？....

-------------------------------------
（Features！）gradle有哪些特点和优势？
-------------------------------------

- **`gradle`** 继承了强大、灵活的**`Ant`** 和 **`Maven`** 丰富的依赖管理（ 过去大多采用Ant和Maven来构建系统）。
- 基于 **`Groovy DSL`** 编写构建脚本（**`Groovy`** 是一种基于`JVM`的动态语言，可以嵌入`Java`语言哦）。
- 配置管理简单，脚本编写方便灵活。（如果你用过`Ant+Maven`，就能体会它们是有多么繁琐复杂。）
- 插件模块化（这里的插件不是指IDE插件哦，是只针对不同平台优化过的`gradle`版本）  ，目前 **`gradle`** 有 ：
 -  plugin for Maven 
 -  plugin for Eclipse
 -  plugin for Java
 -  plugin for Android
 -  plugin for C++ 
 - .... 
 
-  IDE工具集成插件支持丰富（好吧！这里指的才是IDE工具插件）
   什么Eclipse，IDEA ...什么的 必须都支持了。

-------------------------------------
（Training！walk with me ）实践手把手！
-------------------------------------

### 1. 环境要求  
> - 你的15分钟时间 
> - Java/Android运行环境（JDK 1.7+ , Android SDK）
> - 一个你喜欢的文本编辑器 / IDE
> - 新建一个HelloWord 项目
> - 下载 [gradle](http://www.gradle.org/) 最新版（本文将采用gradle 1.11）
> - OK ! Let us go !

### 2. 环境安装和配置
  
> **注意**：为了避免各种奇怪问题，尽可能保证各项环境版本号相互匹配，按下指定的版本。

- Java 1.7+ （安装略过）
- Android SDK Tool >> 打开你的 Android SDK Manager 更新以下2 个工具到指定的版本
    > Android SDK Platform Tool >> 19.0.1+
    > Android SDK build_tools >> 19.0.1+
- 下载 [gradle 1.11](http://www.gradle.org/) ，解压到指定目录，并且在你的系统环境变量`path`中指定 `gradle bin` 路径
        
    > 例如：`set PATH=%PATH%;F:\xx\gradle-1.11\bin` 
      或者 你也可以右击我的电脑——高级——环境变量——在系统变量里有`path`选项中配置
    
    > 接下来你可以验证一下安装成功了没有：
    打开 `cmd` 在你的命令行中 敲入以下命令：`C:\Documents and Settings\Administrator>gradle`

    如果你看到下面类似内容，说明你的`gradle`安装成功了：    
    ```
    C:\Documents and Settings\Administrator>gradle
    :help
    
    Welcome to Gradle 1.11.
    
    To run a build, run gradle <task> ...
    
    To see a list of available tasks, run gradle tasks
    
    To see a list of command-line options, run gradle --help
    
    BUILD SUCCESSFUL
    
    Total time: 14.235 secs
    ```
        
### 3. 接下来，在项目中集成gradle    
- 切换到你新建的 **helloword** `Android` 项目，在你的项目跟目录新建一个文件命名为 `build.gradle` ，这个文件是主角，它就是整个项目的构建脚本。
- 接着再新建一个文件命名为 `local.properties` 这个文件是用来指定你的Android SDK路径的。

 ```
 最后看一下你的文件目录结构：//（这是一个由 Eclipse IDE 创建的项目）
 > \Hello>
           .classpath
           .project
           AndroidManifest.xml
           assets
           bin
           build.gradle      //你新建文件，在这里
           gen
           ic_launcher-web.png
           libs
           local.properties  //你新建文件，在这里
           proguard-project.txt
           project.properties
           res
           src
```
- 先编写`local.properties` 这个文件，这个文件指定Android SDK路径的，根据你的安装的具体路径书写。
    > 例如：
    > `sdk.dir=F\:\\Android\\sdk` 写上这么一句就够了。
- 重点编写 `build.gradle` , 先来一个最简单列子 ，混个眼熟。。。  

    这是一个最简单的 针对 普通`Java` 项目的配置文件，就一行, 简单哈!
    ```
    apply plugin: 'java'   
    
    //告诉 编译脚本 “这是一个Java项目，我要使用Java 版插件 ” 
    //这个插件包含了大部分的Java applications 编译构建，测试，所需的功能。
    ```

    这是一个最简单的 针对 `Android` 项目的配置文件，稍微多了几行，将会在代码注释每一行的意思。
    ```
    // 下面这个配置包含3个模块：
            buildscript {  // 1.定义构建脚本
                repositories {  //指定script仓库
                    mavenCentral()//maven仓库中心
                }
                dependencies {// 这是指 gradle for Android 插件版本为0.9.+'
                    classpath 'com.android.tools.build:gradle:0.9.+'
                }
            }
            
            allprojects { //指定全局仓库
                repositories {
                    mavenCentral()
                }
            }
        
            apply plugin: 'android'// 这里说明 当前项目应用为Android 插件，因为这是一个Android项目
            
            android {// 这里是重点，针对Android所有的配置都将要这个模块配置。
                compileSdkVersion 19 // 编译sdk版本
                buildToolsVersion "19.0.0" //构建工具的版本
            }
    ```
    
    以上就是`gradle`最小化配置。说明一下，仓库是啥? 看右上角注解 **[^仓库是啥]**
    
    ok！跑一个！ 瞧瞧 ！ 打开cmd 命令行，切换路径到项目所在的根目录，敲入 **`gradle build`** 。 
      
    ```     
    F:\workspace\Hello>gradle build
       :compileDebugNdk
       :preBuild
       :preDebugBuild
    ```
    通常如果能看到success的字样，说明正常编译通过，项目的根目录会有生成**`build`**的文件夹。里面就是编译的结果，你能从中找到你的apk文件 。
    
-  这只是一个最基本的配置，接下来我们需要不断的完善它，丰富它，功能变得强大。
-  依赖配置(项目中用到的**`Jar Lib`** 包):
    ```
        /**
         * 依赖配置，即jar文件的引入，有3种方式
         * 注意：lib不要重复引入
         */
        
        dependencies {
            compile 'com.android.support:appcompat-v7:+' /*第一种方式：网络maven仓库下载*/
            compile project(':MyLibrary')  /*第二种方式：多项目文件，把另一个项目作为lib使用*/
            compile files('/libs/xx.jar')  /*第三种方式：指定本地某个目录下的jar文件*/
            compile fileTree(dir: 'libs', include: '*.jar')//多个文件一起导入
        }
    ```

- 默认设置 , 全局的默认配置

    ```
        defaultConfig {
           buildminSdkVersion 7 
           targetSdkVersion 18
           packageName "com.teze.teze"
        }
    ```

- 多渠道配置，例如：新增一个针对googlePlay渠道的包，并且包名也做相应的更改。

    ```
        /**
         * 渠道配置，例如：配置不同包名的apk
         */
        productFlavors {
            googlePlay {
                packageName "com.teze.googlePlay"
                versionCode 20
                signingConfig signingConfigs.myConfig //这里指定当前这个包，所所用的“签名”配置（下面会介绍签名配置）
            }
        }
    ```
- 签名配置：

    ```
        /**
             * 签名配置
             */
        
            signingConfigs {
                myConfig {
                    storeFile file("teze.qq.keystore")// 签名文件
                    storePassword "qq"//密码（对应创建签名文件时候的密码）
                    keyAlias "qq"// key  别名（对应创建签名文件时候的别名）
                    keyPassword "qq"// key 密码（对应创建签名文件时候的密码）
                }
            }
    ```
- 构建类型配置：

    ```
        /**
             * 构建类型，例如 beta ,release,版本的配置
             */
        
            buildTypes {
                zhCN {  //例如：针对中文版构建名为：zhCN的版本
                    debuggable false
                    jniDebugBuild true
                    signingConfig signingConfigs.myConfig
                    runProguard true       //混淆代码控制 :混淆代码配置，默认在SDK目录下
                    proguardFile getDefaultProguardFile('proguard-android.txt')
                }
            }
    ```


- 以上是一个基础配置，还有更多更复杂的功能。
将在下一篇中讨论。。。。。
    - 如何配置 引用**`JNI So`**库的作为**`lib`**情景。
    - 如何配置 引用外部项目 作为**`lib`**的情景。
    - 如何配置 指定不同**`res`**，不同**`AndroidManifest`**的情景。
    - .....
[^仓库是啥]:仓库 , 指的是代码仓库，当前项目所使用到的Jar，lib包 都到这个仓库寻找下载。 

