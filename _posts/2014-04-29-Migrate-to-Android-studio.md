<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>2014-04-29-Migrate-to-Android-studio.md</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h1 id="android-studio-从intellij项目的迁移到gradle-构建环境">Android studio 从IntelliJ项目的迁移到Gradle 构建环境</h1>

<hr>

<p><em>Edit by Fooyou Email : <a href="mailto:foyou23@qq.com">foyou23@qq.com</a></em> </p>

<pre><code>2014/04/29
</code></pre>

<hr>

<h3 id="migrating-from-intellij-projects">Migrating from IntelliJ Projects</h3>

<pre><code>We might provide an automatic migration option in Android Studio in the future.
</code></pre>

<p>我们可能会在后续的Android Studio 的发布版本中加入自动转换功能。</p>

<pre><code>For now, to migrate your IntelliJ project to an Android Gradle project (which can then be imported into IntelliJ and supported directly in IntelliJ), follow the following steps:
Create a basic "build.gradle" file. Unlike the default Gradle file created by Android Studio when you create a new project, the following gradle file will point the source code directories to the existing folders (e.g. res/, src/) instead of the default new directory structure used for Gradle projects (src/main/java/, src/main/res/, etc). A sample gradle file is given below.
</code></pre>

<p>如果你之前一直是使用IntelliJ 开发项目的，而现在想要把你的项目迁移到Android Studio 变成Android Gradle 项目的话，你可以直接导入你的项目到Android Studio中，然后按照如下步骤哦：</p>

<ol>
<li><p>创建一个最小化的配置文件 “build.gradle”(如果是Android Studio创建的项目，这个文件工具已经帮你创建了好！)</p></li>
<li><p>build.gradle包含以下内容，定义源代码的指向目录的位置。比如（res ,src 目录），而Android Studio                      创建的项目，默认的目录结构是这样的(src/main/java/, src/main/res/, etc)</p></li>
<li><p>这里有个简单的样本文件啦：</p></li>
</ol>

<pre class="prettyprint prettyprinted"><code><span class="pln">    build</span><span class="pun">.</span><span class="pln">gradle</span><span class="pun">:</span><span class="pln">
    buildscript </span><span class="pun">{</span><span class="pln">
        repositories </span><span class="pun">{</span><span class="pln">
            mavenCentral</span><span class="pun">()</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">
        dependencies </span><span class="pun">{</span><span class="pln">
            classpath </span><span class="str">'com.android.tools.build:gradle:0.5.+'</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">
    apply plugin</span><span class="pun">:</span><span class="pln"> </span><span class="str">'android'</span><span class="pln">

    dependencies </span><span class="pun">{</span><span class="pln">
        compile fileTree</span><span class="pun">(</span><span class="pln">dir</span><span class="pun">:</span><span class="pln"> </span><span class="str">'libs'</span><span class="pun">,</span><span class="pln"> include</span><span class="pun">:</span><span class="pln"> </span><span class="str">'*.jar'</span><span class="pun">)</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    android </span><span class="pun">{</span><span class="pln">
        compileSdkVersion </span><span class="lit">18</span><span class="pln">
        buildToolsVersion </span><span class="str">"18.0.1"</span><span class="pln">

        sourceSets </span><span class="pun">{</span><span class="pln">
            main </span><span class="pun">{</span><span class="pln">
                manifest</span><span class="pun">.</span><span class="pln">srcFile </span><span class="str">'AndroidManifest.xml'</span><span class="pln">
                java</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
                resources</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
                aidl</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
                renderscript</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
                res</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'res'</span><span class="pun">]</span><span class="pln">
                assets</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'assets'</span><span class="pun">]</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">

            </span><span class="com">// Move the tests to tests/java, tests/res, etc...</span><span class="pln">
            instrumentTest</span><span class="pun">.</span><span class="pln">setRoot</span><span class="pun">(</span><span class="str">'tests'</span><span class="pun">)</span><span class="pln">

            </span><span class="com">// Move the build types to build-types/&lt;type&gt;</span><span class="pln">
            </span><span class="com">// For instance, build-types/debug/java, build-types/debug/AndroidManifest.xml, ...</span><span class="pln">
            </span><span class="com">// This moves them out of them default location under src/&lt;type&gt;/... which would</span><span class="pln">
            </span><span class="com">// conflict with src/ being used by the main source set.</span><span class="pln">
            </span><span class="com">// Adding new build types or product flavors should be accompanied</span><span class="pln">
            </span><span class="com">// by a similar customization.</span><span class="pln">
            debug</span><span class="pun">.</span><span class="pln">setRoot</span><span class="pun">(</span><span class="str">'build-types/debug'</span><span class="pun">)</span><span class="pln">
            release</span><span class="pun">.</span><span class="pln">setRoot</span><span class="pun">(</span><span class="str">'build-types/release'</span><span class="pun">)</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<p>简单解释一下：</p>

<p>1.依赖配置外部类库：在这个配置文件中，可以指定你的项目引用到的外部项目源码，通过依赖配置的方式，应该不少开发者，遇到过在引用外部项目的时候，比如 使用ActionBarSherlock的时候，我们通常是需要把源码导入到开发环境中，然后需要在projects文件中配置指定这个路径，有了Gradle以后，你就不需要这样了，这里你只要简单dependencies 指定就好了。Gradle构建系统，会帮你自动边缘，打包到项目中。</p>

<pre><code>Identify which library projects you are using (such as ActionBarSherlock). In Gradle you no longer need to add in these libraries as source code projects; you can simply refer to them as dependencies, and the build system will handle the rest; downloading, merging in resources and manifest entries, etc. For each library, look up the corresponding AAR library dependency name (provided the library in question has been updated as a android library artifact), and add these to the dependency section.
</code></pre>

<p>想要了解更多的依赖配置说明，可以上这个网站查看<a href="http://gradleplease.appspot.com/">http://gradleplease.appspot.com/</a></p>

<pre><code>To find the right declaration statements for your libraries, you might find the following useful: http://gradleplease.appspot.com/
</code></pre>

<p>2.如果想测试你的依赖库配置是否正确以否，可以允许这个 assembleDebug 脚步，（当你配置好以后，在Gradle Task 会找到这个脚本的）</p>

<pre><code>Test your build by running gradle assembleDebug in your project. If you have not built with Gradle before, install it from http://www.gradle.org/downloads. Note that when you create new projects with Studio, we create a gradle wrapper script ("gradlew" and "gradlew.bat") in the project root directory, so any users of the project can just run "gradlew assembleDebug" etc in your project and gradle is automatically installed and downloaded. However, your existing IntelliJ projects presumably does not already have the gradle scripts.)
Note that IntelliJ projects for Android generally follow the same structure as Eclipse ADT projects, so the instructions in the Eclipse migration guide might be helpful.

For more information on customizing your build once you have the basics set up, see the new build system user guide. For additional information, see the build system overview page.
</code></pre></div></body>
</html>