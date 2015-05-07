<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Gradle for Android Part 2.md</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h1 id="gralde-for-android-part-2">Gradle for Android (Part 2)</h1>

<hr>

<p><em>Edit by Fooyou Email : <a href="mailto:foyou23@qq.com">foyou23@qq.com</a></em> </p>

<pre><code>10:56 2014-5-5
</code></pre>

<hr>

<p>在前一部分讲述了，Gralde for Android 的一些基本配置，可以满足基本的构建需求，但是有了一定规模的APP的需求却会越来越复杂的，所以，会用到更复杂的配置。接下来，就来看看稍微复杂一点点的咯。</p>

<h2 id="引用jni-so库作为lib的情景">引用JNI ，So库作为lib的情景</h2>

<p>如果你的项目有用到So库，要如何在<code>gradle</code>中，把so库也编译到apk中呢？现在，还是使用之前的<code>HelloWorld</code> 项目为例子。在<code>gralde.build</code>文件中，<code>Android</code> 大括号中添加如下脚本,编写2个task：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">        </span><span class="com">//这个任务的意思是，把libs下所有的`So`文件，打包成`zip`，并且复制到`buildDir` native-libs目录下面。</span><span class="pln">
        task nativeLibsToJar</span><span class="pun">(</span><span class="pln">type</span><span class="pun">:</span><span class="pln"> </span><span class="typ">Zip</span><span class="pun">,</span><span class="pln"> description</span><span class="pun">:</span><span class="pln"> </span><span class="str">'create a jar archive of     the native libs'</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        destinationDir file</span><span class="pun">(</span><span class="str">"$buildDir/native-libs"</span><span class="pun">)</span><span class="pln">
        baseName </span><span class="str">'native-libs'</span><span class="pln">
        extension </span><span class="str">'jar'</span><span class="pln">
        </span><span class="kwd">from</span><span class="pln"> fileTree</span><span class="pun">(</span><span class="pln">dir</span><span class="pun">:</span><span class="pln"> </span><span class="str">'libs'</span><span class="pun">,</span><span class="pln"> include</span><span class="pun">:</span><span class="pln"> </span><span class="str">'**/*.so'</span><span class="pun">)</span><span class="pln">
        </span><span class="kwd">into</span><span class="pln"> </span><span class="str">'lib/'</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="com">//这个任务则是把，上面任务打包好的so文件，加入参与编译。</span><span class="pln">
    tasks</span><span class="pun">.</span><span class="pln">withType</span><span class="pun">(</span><span class="typ">Compile</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        compileTask </span><span class="pun">-&gt;</span><span class="pln"> compileTask</span><span class="pun">.</span><span class="pln">dependsOn</span><span class="pun">(</span><span class="pln">nativeLibsToJar</span><span class="pun">)</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<p>配置是比较简单了，试试！看看能否运行。到目前好像Android stuio 有更好的办法，来做这件事情了，到时候在看咯。 <br>
<strong>记住，这里说的配置，都是Android的方法块中添加。</strong></p>

<h2 id="引用外部项目作为当前项目的lib的情景">引用外部项目，作为当前项目的lib的情景</h2>

<p>在项目开发中，有时候要用第三方库，（比如：<code>Android-PullToRefresh</code>，<code>ActionBarSherlock</code>）而这些库本身是一个Android项目，又不能直接打包成普通的Jar包，如果是Eclipse ADT开发，有直接的配置，比较简单，Android studio 配置也很简单，但是如果在Eclipse 使用Gradle 呢？</p>

<p>首先新建一个libProject，<strong>(现在,就有2个项目了libProject 和 HelloWorld)</strong></p>

<blockquote>
  <p><strong>tips</strong>：<code>project.properties</code> 配置文件，添加一行 <code>android.library=true</code>，这个项目就会被编译成<code>lib</code>项目了。本例子中<code>libProject</code> 就是如此。</p>
</blockquote>

<p>我们来看看新的文件目录结构：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">android
</span><span class="pun">├─</span><span class="typ">Hellowold</span><span class="pln">
</span><span class="pun">└─</span><span class="typ">LibProject</span></code></pre>

<p>现在我们需要新增配置文件，在根目录android 下新增3个配置文件，分别是：<code>build.gradle</code> <br>
  <code>local.properties</code>  <code>settings.gradle</code></p>

<pre class="prettyprint prettyprinted"><code><span class="pln">android
</span><span class="pun">├</span><span class="pln"> build</span><span class="pun">.</span><span class="pln">gradle
</span><span class="pun">├</span><span class="pln"> </span><span class="kwd">local</span><span class="pun">.</span><span class="pln">properties
</span><span class="pun">├</span><span class="pln"> settings</span><span class="pun">.</span><span class="pln">gradle
</span><span class="pun">├─</span><span class="typ">Hellowold</span><span class="pln">
</span><span class="pun">└─</span><span class="typ">LibProject</span><span class="pln">
    build</span><span class="pun">.</span><span class="pln">gradle</span></code></pre>

<p><code>build.gradle</code> 文件内容如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="com">//这个文件是整个项目（本文中的android）全局配置，每一个子模块中也有相应的`build.gradle`文件。Hellowold ，LibProject 下都有的。</span><span class="pln">

buildscript </span><span class="pun">{</span><span class="pln">
    repositories </span><span class="pun">{</span><span class="pln">
        mavenCentral</span><span class="pun">()</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">
    dependencies </span><span class="pun">{</span><span class="pln">
        classpath </span><span class="str">'com.android.tools.build:gradle:0.9.+'</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></code></pre>

<p><code>local.properties</code> 文件内容如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">sdk</span><span class="pun">.</span><span class="pln">dir</span><span class="pun">=</span><span class="pln">F</span><span class="pun">:</span><span class="pln">\\</span><span class="typ">Android</span><span class="pln">\\sdk </span><span class="com">// 跟Helloworld项目的一样，指定sdk路径</span></code></pre>

<p><code>settings.gradle</code> 文件内容如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="com">//这个文件内容是整个项目，模块配置，表明包含哪些模块。</span><span class="pln">
    include </span><span class="str">':LibProject'</span><span class="pln">      </span><span class="com">// 当前项目包含LibProject</span><span class="pln">
    include </span><span class="str">':Hellowold'</span><span class="pln">     </span><span class="com">// 当前项目包含Hellowold</span></code></pre>

<p><code>LibProject</code> 下的  <code>build.gradle</code> 文件内容如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="com">//你会发现，这个文件基本和Helloworld下的配置差不多，配置的参数意思是相同的，但有一点注意，也是最大的区别。 apply plugin: 'android-library' 这句。</span><span class="pln">


    apply plugin</span><span class="pun">:</span><span class="pln"> </span><span class="str">'android-library'</span><span class="pln">  </span><span class="com">//没错就是这里，要写成'android-library'</span><span class="pln">
                                     </span><span class="com">//表明这是一个library    </span><span class="pln">
        dependencies </span><span class="pun">{</span><span class="pln">
            compile fileTree</span><span class="pun">(</span><span class="pln">dir</span><span class="pun">:</span><span class="pln"> </span><span class="str">'libs'</span><span class="pun">,</span><span class="pln"> include</span><span class="pun">:</span><span class="pln"> </span><span class="str">'*.jar'</span><span class="pun">)</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    android </span><span class="pun">{</span><span class="pln">
        compileSdkVersion </span><span class="lit">19</span><span class="pln">
        buildToolsVersion </span><span class="str">"19.0.3"</span><span class="pln">


        lintOptions </span><span class="pun">{</span><span class="pln">
            abortOnError </span><span class="kwd">false</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<p>Ok ,有了上面的配置，应该可以正常运行，试试咯。</p>

<h2 id="指定文件目录配置">指定文件目录配置</h2>

<p>本文中的<code>HelloWorld</code>项目是在<code>Eclipse</code>中创建的，其目录结构是 ADT 模式的目录结构，如果要用gradle 编译的，可能还需要指定目录配置。同样在Android方法块中 新增一个任务，如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="com">//这是指定项目，模块，对应的文件路径，文件名字</span><span class="pln">
sourceSets </span><span class="pun">{</span><span class="pln">

       main </span><span class="pun">{</span><span class="pln">
           manifest</span><span class="pun">.</span><span class="pln">srcFile </span><span class="str">'src/main/AndroidManifest.xml'</span><span class="pln">
           java</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
           resources</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
           aidl</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
           renderscript</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
           res</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'res'</span><span class="pun">]</span><span class="pln">
           assets</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'assets'</span><span class="pun">]</span><span class="pln">
       </span><span class="pun">}</span><span class="pln">

       instrumentTest</span><span class="pun">.</span><span class="pln">setRoot</span><span class="pun">(</span><span class="str">'tests'</span><span class="pun">)</span><span class="pln">  </span><span class="com">/* 测试目录*/</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<p>如果是用 Android studio 创建的项目，就不需要这么配置了，因为它有默认约定的目录结构。</p>

<h2 id="指定独立的res资源文件的情景">指定独立的res，资源文件的情景</h2>

<p>如果，想要给不同渠道的apk包，指定不同的res资源文件呢？比如，一个App 使用不同的drawble，不同icon，不同的string 等等…</p>

<p>现假设，需要给GooglePlay这个版本的渠道，指定独立的res资源，可以如下操作：</p>

<ul>
<li><p>在项目跟目录新增一个新的目录GooglePlay，然后把对应的res文件放入其中，新的目录结构如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="pun">│</span><span class="pln">  build</span><span class="pun">.</span><span class="pln">gradle
</span><span class="pun">│</span><span class="pln">  </span><span class="kwd">local</span><span class="pun">.</span><span class="pln">properties
</span><span class="pun">│</span><span class="pln">  settings</span><span class="pun">.</span><span class="pln">gradle
</span><span class="pun">│</span><span class="pln">
</span><span class="pun">├─</span><span class="typ">Hellowold</span><span class="pln">
</span><span class="pun">│</span><span class="pln">  </span><span class="pun">└─</span><span class="typ">GooglePlay</span><span class="pln">
</span><span class="pun">│</span><span class="pln">      </span><span class="pun">└─</span><span class="pln">res
</span><span class="pun">└─</span><span class="typ">LibProject</span><span class="pln">
        build</span><span class="pun">.</span><span class="pln">gradle</span></code></pre></li>
<li><p>修改<code>sourceSets</code>任务，内容如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">sourceSets </span><span class="pun">{</span><span class="pln">

   main </span><span class="pun">{</span><span class="pln">
       manifest</span><span class="pun">.</span><span class="pln">srcFile </span><span class="str">'src/main/AndroidManifest.xml'</span><span class="pln">
       java</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
       resources</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
       aidl</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
       renderscript</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'src'</span><span class="pun">]</span><span class="pln">
       res</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'res'</span><span class="pun">]</span><span class="pln">
       assets</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'assets'</span><span class="pun">]</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">

   instrumentTest</span><span class="pun">.</span><span class="pln">setRoot</span><span class="pun">(</span><span class="str">'tests'</span><span class="pun">)</span><span class="pln">  </span><span class="com">/* 测试目录*/</span><span class="pln">

   </span><span class="com">//注意到这里，给GooglePlay指定特定的res资源。</span><span class="pln">
    </span><span class="typ">GooglePlay</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> 
        res</span><span class="pun">.</span><span class="pln">srcDirs </span><span class="pun">=</span><span class="pln"> </span><span class="pun">[</span><span class="str">'GooglePlay/res'</span><span class="pun">]</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></code></pre></li>
<li><p>以此同时，你可能还需要在productFlavors 任务中新增一个GooglePlay渠道。</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">productFlavors </span><span class="pun">{</span><span class="pln"> 
   </span><span class="com">// 同样可以在这里，配置GooglePlay的其他参数，</span><span class="pln">
    </span><span class="typ">GooglePlay</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> 

   </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></code></pre></li>
</ul>

<p>其他资源文件类似，src，assets 等待。</p>

<h2 id="指定独立的androidmanifest的情景">指定独立的AndroidManifest的情景</h2>

<p>这个部分，或许很有用处，尤其是在打多渠道包的时候。也稍微有点复杂。理论上可以跟res一样，指定特地的AndroidManifest配置文件，但是，gradle 做文件合并的时候，不够智能，总不能符合预定的效果。所以会采用更为复杂一点的办法。原理很简单的：就是新建一个task 把对应的 AndroidManifest 文件复制到编译目录下。例子如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">android</span><span class="pun">.</span><span class="pln">applicationVariants</span><span class="pun">.</span><span class="pln">all </span><span class="pun">{</span><span class="pln"> variant </span><span class="pun">-&gt;</span><span class="pln">
        </span><span class="kwd">def</span><span class="pln"> flavor </span><span class="pun">=</span><span class="pln"> variant</span><span class="pun">.</span><span class="pln">productFlavors</span><span class="pun">[</span><span class="lit">0</span><span class="pun">].</span><span class="pln">name

             variant</span><span class="pun">.</span><span class="pln">processManifest</span><span class="pun">.</span><span class="pln">doLast</span><span class="pun">{</span><span class="pln"> 
                copy</span><span class="pun">{</span><span class="pln">
                    </span><span class="kwd">from</span><span class="pun">(</span><span class="str">"${buildDir}/manifests"</span><span class="pun">){</span><span class="pln">
		                include </span><span class="str">"${variant.dirName}/AndroidManifest.xml"</span><span class="pln"> </span><span class="com">//把编译目录下的AndroidManifest复制一份</span><span class="pln">
		            </span><span class="pun">}</span><span class="pln">
		            </span><span class="kwd">into</span><span class="pun">(</span><span class="str">"${buildDir}/manifests/$flavor"</span><span class="pun">)</span><span class="com">//复制 AndroidManifest到对应的渠道目录下</span><span class="pln">

                    </span><span class="com">// 你想要做的操作，这里是更改AndroidManifest的某个字符。例子中是替换UMENG_CHANNEL_VALUE 的值为 对应的渠道名字</span><span class="pln">
                    filter</span><span class="pun">{</span><span class="pln">
                        </span><span class="typ">String</span><span class="pln"> line </span><span class="pun">-&gt;</span><span class="pln"> line</span><span class="pun">.</span><span class="pln">replaceAll</span><span class="pun">(</span><span class="str">"UMENG_CHANNEL_VALUE"</span><span class="pun">,</span><span class="pln"> </span><span class="str">"$flavor"</span><span class="pun">)</span><span class="com">//动态替换AndroidManifest的字符</span><span class="pln">
		            </span><span class="pun">}</span><span class="pln"> 
		            variant</span><span class="pun">.</span><span class="pln">processResources</span><span class="pun">.</span><span class="pln">manifestFile </span><span class="pun">=</span><span class="pln"> file</span><span class="pun">(</span><span class="str">"${buildDir}/manifests/$flavor/${variant.dirName}/AndroidManifest.xml"</span><span class="pun">)</span><span class="pln">
                  </span><span class="pun">}</span><span class="pln"> 
             </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<p>嗯，基本就是这样了。</p>

<h2 id="总结">总结</h2>

<p>先写的这里把，后续可能还会写这方面相关的内容。 <br>
Gradle的优势就是可以使用<code>groovy</code>脚本语言，来自定义各种<code>task</code>，这样就显得功能强大了，随心所欲了。</p></div></body>
</html>
