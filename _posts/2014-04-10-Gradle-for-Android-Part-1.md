<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Gradle for Android.md</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h1 id="gralde-for-android">Gradle for Android</h1>

<p><em>Written by Fooyou <a href="mailto:foyou23@qq.com">foyou23@qq.com</a></em></p>

<pre><code>2014/3/27 
</code></pre>

<hr>

<h2 id="目录">目录</h2>

<p><div class="toc">
<ul>
<li><a href="#gralde-for-android">Gralde for Android</a><ul>
<li><a href="#目录">目录</a></li>
<li><a href="#what-什么是gradle">（What？） 什么是gradle？</a></li>
<li><a href="#why为什么要有gradle">（Why？）为什么要有gradle？</a><ul>
<li><a href="#1-自动打包测试部署">1. 自动打包测试部署</a></li>
<li><a href="#2-打渠道包">2. 打渠道包</a></li>
<li><a href="#3-定制版本发布特定需求版本发布">3. 定制版本发布，特定需求版本发布</a></li>
<li><a href="#4-还有更多其他需求">4. 还有更多其他需求 ？….</a></li>
</ul>
</li>
<li><a href="#featuresgradle有哪些特点和优势">（Features！）gradle有哪些特点和优势？</a></li>
<li><a href="#trainingwalk-with-me-实践手把手">（Training！walk with me ）实践手把手！</a><ul>
<li><a href="#1-环境要求">1. 环境要求</a></li>
<li><a href="#2-环境安装和配置">2. 环境安装和配置</a></li>
<li><a href="#3-接下来在项目中集成gradle">3. 接下来，在项目中集成gradle</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</p>

<hr>

<h2 id="what-什么是gradle">（What？） 什么是gradle？</h2>

<blockquote>
  <p><strong><code>gradle</code></strong>是一个自动化构建系统，可以集成自动编译，测试，打包，发布，部署功能，还有其他一些软件工程管理功能（比如：生成静态网站，生成文档啊，等等）。</p>
</blockquote>

<hr>

<h2 id="why为什么要有gradle">（Why？）为什么要有gradle？</h2>

<h3 id="1-自动打包测试部署">1. 自动打包测试部署</h3>

<blockquote>
  <p>做为一个项目开发人员，你是不是特别希望代码敲好了，有这样一个机器人，只要你一条指令，能够自动帮你编译，测试bug啊，打包啊 一系列有的没的。。自个可以泡一杯茶，刷刷微博，看看美女图片啊。。。有木有？反正我是有！</p>
</blockquote>

<h3 id="2-打渠道包">2. 打渠道包</h3>

<blockquote>
  <ul>
  <li>做为Android开发的你，如果你的打包发布流程中，没有 “打渠道包” 这一个奇葩需求的话，那我就想偷偷笑你一下，你的项目太烂 太小了！对，没错，应用发布，不可能只发布到一个应用市场（在国内有n多乱七八糟的应用市场），你的营运需要统计各个市场的下载量，用户数量。。。等等一系列数据，这个时候区分不同的市场就很有统计意义了（我们称其为“不同渠道”）。</li>
  <li>Android开发中，通常我们打渠道包都是在manifest文件中配置不同的渠道参数，虽然在市面上有不少现成的工具比如：CNZZ的打包工具，友盟的打包工具，这些工具都能帮你打出渠道包，但是也只能局限他们的工具，换一个字符，换一个参数，就搞不了了，如果不嫌累，你可以手动修改manifest文件，然后重新编译，我想没几个人愿意这么干，现在有了 <strong><code>gradle</code></strong> 能够帮你解决上面描述的问题。</li>
  </ul>
</blockquote>

<h3 id="3-定制版本发布特定需求版本发布">3. 定制版本发布，特定需求版本发布</h3>

<blockquote>
  <p>有时候会这样的需求：我想针对某个平台，或者某个厂商发布一个定制版本的软件，而这些特定的功能，不需出现在普通版中。<strong>比如：我想同时发布一个收费版本和免费版本</strong>，<strong>再比如：我想发布一个单独设备上面跑的版本</strong> 这个时候<strong><code>gradle</code></strong>能够很方便的管理不同的源码，发布不同的版本。</p>
</blockquote>

<h3 id="4-还有更多其他需求">4. 还有更多其他需求 ？….</h3>

<hr>

<h2 id="featuresgradle有哪些特点和优势">（Features！）gradle有哪些特点和优势？</h2>

<ul>
<li><strong><code>gradle</code></strong> 继承了强大、灵活的<strong><code>Ant</code></strong> 和 <strong><code>Maven</code></strong> 丰富的依赖管理（ 过去大多采用Ant和Maven来构建系统）。</li>
<li>基于 <strong><code>Groovy DSL</code></strong> 编写构建脚本（<strong><code>Groovy</code></strong> 是一种基于<code>JVM</code>的动态语言，可以嵌入<code>Java</code>语言哦）。</li>
<li>配置管理简单，脚本编写方便灵活。（如果你用过<code>Ant+Maven</code>，就能体会它们是有多么繁琐复杂。）</li>
<li><p>插件模块化（这里的插件不是指IDE插件哦，是只针对不同平台优化过的<code>gradle</code>版本）  ，目前 <strong><code>gradle</code></strong> 有 ：</p>

<ul><li>plugin for Maven </li>
<li>plugin for Eclipse</li>
<li>plugin for Java</li>
<li>plugin for Android</li>
<li>plugin for C++ </li>
<li>…. </li></ul></li>
<li><p>IDE工具集成插件支持丰富（好吧！这里指的才是IDE工具插件） <br>
什么Eclipse，IDEA …什么的 必须都支持了。</p></li>
</ul>

<hr>

<h2 id="trainingwalk-with-me-实践手把手">（Training！walk with me ）实践手把手！</h2>

<h3 id="1-环境要求">1. 环境要求</h3>

<blockquote>
  <ul>
  <li>你的15分钟时间 </li>
  <li>Java/Android运行环境（JDK 1.7+ , Android SDK）</li>
  <li>一个你喜欢的文本编辑器 / IDE</li>
  <li>新建一个HelloWord 项目</li>
  <li>下载 <a href="http://www.gradle.org/">gradle</a> 最新版（本文将采用gradle 1.11）</li>
  <li>OK ! Let us go !</li>
  </ul>
</blockquote>

<h3 id="2-环境安装和配置">2. 环境安装和配置</h3>

<blockquote>
  <p><strong>注意</strong>：为了避免各种奇怪问题，尽可能保证各项环境版本号相互匹配，按下指定的版本。</p>
</blockquote>

<ul>
<li>Java 1.7+ （安装略过）</li>
<li>Android SDK Tool &gt;&gt; 打开你的 Android SDK Manager 更新以下2 个工具到指定的版本 <br>


<blockquote>
  <p>Android SDK Platform Tool &gt;&gt; 19.0.1+ <br>
  Android SDK build_tools &gt;&gt; 19.0.1+</p></blockquote></li>
  <li><p>下载 <a href="http://www.gradle.org/">gradle 1.11</a> ，解压到指定目录，并且在你的系统环境变量<code>path</code>中指定 <code>gradle bin</code> 路径</p><p></p>


<blockquote>
  <p>例如：<code>set PATH=%PATH%;F:\xx\gradle-1.11\bin</code>  <br>
    或者 你也可以右击我的电脑——高级——环境变量——在系统变量里有<code>path</code>选项中配置</p>
  
  <p>接下来你可以验证一下安装成功了没有： <br>
  打开 <code>cmd</code> 在你的命令行中 敲入以下命令：<code>C:\Documents and Settings\Administrator&gt;gradle</code></p>
</blockquote>

<p>如果你看到下面类似内容，说明你的<code>gradle</code>安装成功了：    </p>

<pre class="prettyprint prettyprinted"><code><span class="pln">C</span><span class="pun">:</span><span class="pln">\Documents </span><span class="kwd">and</span><span class="pln"> </span><span class="typ">Settings</span><span class="pln">\Administrator</span><span class="pun">&gt;</span><span class="pln">gradle
</span><span class="pun">:</span><span class="pln">help

</span><span class="typ">Welcome</span><span class="pln"> to </span><span class="typ">Gradle</span><span class="pln"> </span><span class="lit">1.11</span><span class="pun">.</span><span class="pln">

</span><span class="typ">To</span><span class="pln"> run a build</span><span class="pun">,</span><span class="pln"> run gradle </span><span class="str">&lt;task&gt;</span><span class="pln"> </span><span class="pun">...</span><span class="pln">

</span><span class="typ">To</span><span class="pln"> see a list of available tasks</span><span class="pun">,</span><span class="pln"> run gradle tasks

</span><span class="typ">To</span><span class="pln"> see a list of command</span><span class="pun">-</span><span class="pln">line options</span><span class="pun">,</span><span class="pln"> run gradle </span><span class="pun">--</span><span class="pln">help

BUILD SUCCESSFUL

</span><span class="typ">Total</span><span class="pln"> time</span><span class="pun">:</span><span class="pln"> </span><span class="lit">14.235</span><span class="pln"> secs</span></code></pre></li>
</ul>

<h3 id="3-接下来在项目中集成gradle">3. 接下来，在项目中集成gradle</h3>

<ul>
<li>切换到你新建的 <strong>helloword</strong> <code>Android</code> 项目，在你的项目跟目录新建一个文件命名为 <code>build.gradle</code> ，这个文件是主角，它就是整个项目的构建脚本。</li>
<li><p>接着再新建一个文件命名为 <code>local.properties</code> 这个文件是用来指定你的Android SDK路径的。</p>

<pre class="prettyprint prettyprinted"><code><span class="pun">最后看一下你的文件目录结构：</span><span class="com">//（这是一个由 Eclipse IDE 创建的项目）</span><span class="pln">
</span><span class="pun">&gt;</span><span class="pln"> \Hello</span><span class="pun">&gt;</span><span class="pln">
       </span><span class="pun">.</span><span class="pln">classpath
       </span><span class="pun">.</span><span class="pln">project
       </span><span class="typ">AndroidManifest</span><span class="pun">.</span><span class="pln">xml
       assets
       bin
       build</span><span class="pun">.</span><span class="pln">gradle      </span><span class="com">//你新建文件，在这里</span><span class="pln">
       gen
       ic_launcher</span><span class="pun">-</span><span class="pln">web</span><span class="pun">.</span><span class="pln">png
       libs
       </span><span class="kwd">local</span><span class="pun">.</span><span class="pln">properties  </span><span class="com">//你新建文件，在这里</span><span class="pln">
       proguard</span><span class="pun">-</span><span class="pln">project</span><span class="pun">.</span><span class="pln">txt
       project</span><span class="pun">.</span><span class="pln">properties
       res
       src</span></code></pre></li>
<li>先编写<code>local.properties</code> 这个文件，这个文件指定Android SDK路径的，根据你的安装的具体路径书写。 <br>


<blockquote>
  <p>例如： <br>
  <code>sdk.dir=F:\\Android\\sdk</code> 写上这么一句就够了。</p></blockquote></li>
  <li><p>重点编写 <code>build.gradle</code> , 先来一个最简单列子 ，混个眼熟。。。  </p><p></p>


<p>这是一个最简单的 针对 普通<code>Java</code> 项目的配置文件，就一行, 简单哈!</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">apply plugin</span><span class="pun">:</span><span class="pln"> </span><span class="str">'java'</span><span class="pln">   

</span><span class="com">//告诉 编译脚本 “这是一个Java项目，我要使用Java 版插件 ” </span><span class="pln">
</span><span class="com">//这个插件包含了大部分的Java applications 编译构建，测试，所需的功能。</span></code></pre>

<p>这是一个最简单的 针对 <code>Android</code> 项目的配置文件，稍微多了几行，将会在代码注释每一行的意思。</p>

<pre class="prettyprint prettyprinted"><code><span class="com">// 下面这个配置包含3个模块：</span><span class="pln">
        buildscript </span><span class="pun">{</span><span class="pln">  </span><span class="com">// 1.定义构建脚本</span><span class="pln">
            repositories </span><span class="pun">{</span><span class="pln">  </span><span class="com">//指定script仓库</span><span class="pln">
                mavenCentral</span><span class="pun">()</span><span class="com">//maven仓库中心</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
            dependencies </span><span class="pun">{</span><span class="com">// 这是指 gradle for Android 插件版本为0.9.+'</span><span class="pln">
                classpath </span><span class="str">'com.android.tools.build:gradle:0.9.+'</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">

        allprojects </span><span class="pun">{</span><span class="pln"> </span><span class="com">//指定全局仓库</span><span class="pln">
            repositories </span><span class="pun">{</span><span class="pln">
                mavenCentral</span><span class="pun">()</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">

        apply plugin</span><span class="pun">:</span><span class="pln"> </span><span class="str">'android'</span><span class="com">// 这里说明 当前项目应用为Android 插件，因为这是一个Android项目</span><span class="pln">

        android </span><span class="pun">{</span><span class="com">// 这里是重点，针对Android所有的配置都将要这个模块配置。</span><span class="pln">
            compileSdkVersion </span><span class="lit">19</span><span class="pln"> </span><span class="com">// 编译sdk版本</span><span class="pln">
            buildToolsVersion </span><span class="str">"19.0.0"</span><span class="pln"> </span><span class="com">//构建工具的版本</span><span class="pln">
        </span><span class="pun">}</span></code></pre>

<p>以上就是<code>gradle</code>最小化配置。说明一下，仓库是啥? 看右上角注解 <strong><a href="#fn:" id="fnref:" title="See footnote" class="footnote">1</a></strong></p>

<p>ok！跑一个！ 瞧瞧 ！ 打开cmd 命令行，切换路径到项目所在的根目录，敲入 <strong><code>gradle build</code></strong> 。 </p>

<pre class="prettyprint prettyprinted"><code><span class="pln">F</span><span class="pun">:</span><span class="pln">\workspace\Hello</span><span class="pun">&gt;</span><span class="pln">gradle build
   </span><span class="pun">:</span><span class="pln">compileDebugNdk
   </span><span class="pun">:</span><span class="pln">preBuild
   </span><span class="pun">:</span><span class="pln">preDebugBuild</span></code></pre>

<p>通常如果能看到success的字样，说明正常编译通过，项目的根目录会有生成<strong><code>build</code></strong>的文件夹。里面就是编译的结果，你能从中找到你的apk文件 。</p></li>
<li><p>这只是一个最基本的配置，接下来我们需要不断的完善它，丰富它，功能变得强大。</p></li>
<li><p>依赖配置(项目中用到的<strong><code>Jar Lib</code></strong> 包):</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="com">/**
     * 依赖配置，即jar文件的引入，有3种方式
     * 注意：lib不要重复引入
     */</span><span class="pln">

    dependencies </span><span class="pun">{</span><span class="pln">
        compile </span><span class="str">'com.android.support:appcompat-v7:+'</span><span class="pln"> </span><span class="com">/*第一种方式：网络maven仓库下载*/</span><span class="pln">
        compile project</span><span class="pun">(</span><span class="str">':MyLibrary'</span><span class="pun">)</span><span class="pln">  </span><span class="com">/*第二种方式：多项目文件，把另一个项目作为lib使用*/</span><span class="pln">
        compile files</span><span class="pun">(</span><span class="str">'/libs/xx.jar'</span><span class="pun">)</span><span class="pln">  </span><span class="com">/*第三种方式：指定本地某个目录下的jar文件*/</span><span class="pln">
        compile fileTree</span><span class="pun">(</span><span class="pln">dir</span><span class="pun">:</span><span class="pln"> </span><span class="str">'libs'</span><span class="pun">,</span><span class="pln"> include</span><span class="pun">:</span><span class="pln"> </span><span class="str">'*.jar'</span><span class="pun">)</span><span class="com">//多个文件一起导入</span><span class="pln">
    </span><span class="pun">}</span></code></pre></li>
<li><p>默认设置 , 全局的默认配置</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    defaultConfig </span><span class="pun">{</span><span class="pln">
       buildminSdkVersion </span><span class="lit">7</span><span class="pln"> 
       targetSdkVersion </span><span class="lit">18</span><span class="pln">
       packageName </span><span class="str">"com.teze.teze"</span><span class="pln">
    </span><span class="pun">}</span></code></pre></li>
<li><p>多渠道配置，例如：新增一个针对googlePlay渠道的包，并且包名也做相应的更改。</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="com">/**
     * 渠道配置，例如：配置不同包名的apk
     */</span><span class="pln">
    productFlavors </span><span class="pun">{</span><span class="pln">
        googlePlay </span><span class="pun">{</span><span class="pln">
            packageName </span><span class="str">"com.teze.googlePlay"</span><span class="pln">
            versionCode </span><span class="lit">20</span><span class="pln">
            signingConfig signingConfigs</span><span class="pun">.</span><span class="pln">myConfig </span><span class="com">//这里指定当前这个包，所所用的“签名”配置（下面会介绍签名配置）</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span></code></pre></li>
<li><p>签名配置：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="com">/**
         * 签名配置
         */</span><span class="pln">

        signingConfigs </span><span class="pun">{</span><span class="pln">
            myConfig </span><span class="pun">{</span><span class="pln">
                storeFile file</span><span class="pun">(</span><span class="str">"teze.qq.keystore"</span><span class="pun">)</span><span class="com">// 签名文件</span><span class="pln">
                storePassword </span><span class="str">"qq"</span><span class="com">//密码（对应创建签名文件时候的密码）</span><span class="pln">
                keyAlias </span><span class="str">"qq"</span><span class="com">// key  别名（对应创建签名文件时候的别名）</span><span class="pln">
                keyPassword </span><span class="str">"qq"</span><span class="com">// key 密码（对应创建签名文件时候的密码）</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
        </span><span class="pun">}</span></code></pre></li>
<li><p>构建类型配置：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="com">/**
         * 构建类型，例如 beta ,release,版本的配置
         */</span><span class="pln">

        buildTypes </span><span class="pun">{</span><span class="pln">
            zhCN </span><span class="pun">{</span><span class="pln">  </span><span class="com">//例如：针对中文版构建名为：zhCN的版本</span><span class="pln">
                debuggable </span><span class="kwd">false</span><span class="pln">
                jniDebugBuild </span><span class="kwd">true</span><span class="pln">
                signingConfig signingConfigs</span><span class="pun">.</span><span class="pln">myConfig
                runProguard </span><span class="kwd">true</span><span class="pln">       </span><span class="com">//混淆代码控制 :混淆代码配置，默认在SDK目录下</span><span class="pln">
                proguardFile getDefaultProguardFile</span><span class="pun">(</span><span class="str">'proguard-android.txt'</span><span class="pun">)</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
        </span><span class="pun">}</span></code></pre></li>
<li><p>以上是一个基础配置，还有更多更复杂的功能。 <br>
将在下一篇中讨论。。。。。</p>

<ul><li>如何配置 引用<strong><code>JNI So</code></strong>库的作为<strong><code>lib</code></strong>情景。</li>
<li>如何配置 引用外部项目 作为<strong><code>lib</code></strong>的情景。</li>
<li>如何配置 指定不同<strong><code>res</code></strong>，不同<strong><code>AndroidManifest</code></strong>的情景。</li>
<li>…..</li></ul></li>
</ul>

<div class="footnotes"><hr><ol><li id="fn:">仓库 , 指的是代码仓库，当前项目所使用到的Jar，lib包 都到这个仓库寻找下载。  <a href="#fnref:" title="Return to article" class="reversefootnote">↩</a></li></ol></div></div></body>
</html>
