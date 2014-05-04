<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>2014-5-4-Architecture-for-Android.md</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h1 id="robust-and-readable-a-app健壮的可读性高的框架-for-android-app">Robust and readable a App(健壮的，可读性高的框架 for  Android App)</h1>

<p>(翻译)<a href="http://joanzap.ghost.io/robust-architecture-for-an-android-app/">原文</a></p>

<hr>

<p><em>Edit by Fooyou Email : foyou23#qq.com</em></p>

<pre><code>10:03 2014-5-4
</code></pre>

<hr>

<p>很早以前，我就一直在寻找一个健壮的方式构建Android应用，它能够做到，保持IO 操作在UI线程之外，避免重复冗余的网络请求，合理的缓存资源，合理的更新缓存，等等…能够变得更有条理，结构清晰。</p>

<p><strong>这篇博客，不会提供一个精确的实现方式，只是给出一个可行的参考方案，来构建一个具有协调的，良好可读性，健壮，可扩展的应用。</strong></p>

<h2 id="一些现有的解决方案some-existing-solutions">一些现有的解决方案，Some existing solutions</h2>

<p>Android一开始的时候，大部分的人都依靠<a href="http://developer.android.com/reference/android/os/AsyncTask.html">AsyncTasks</a>来跑一个较长时间的处理任务，事实上，这个很烂的，已经有大量的文章关于这个部分的方案的。比如，后来的<code>Honeycomb</code> 引入了<a href="http://developer.android.com/guide/components/loaders.html">Loaders</a>，它能够更好的支持配置变化，在2012年的时候，<a href="https://github.com/stephanenicolas/robospice">Robospice</a>出现,基于一个后台<code>Android</code> <code>Service</code> ，这里有一张信息<a href="https://raw.github.com/stephanenicolas/robospice/master/gfx/RoboSpice-InfoGraphics.png">图表</a>，上面展示了工作原理。</p>

<p>它比<code>AsyncTask</code> 强多了，不过呢，对于它，我还是发现有些问题，下面有一段使用<code>Robospice</code>的常规写法的代码，写在一个Acitivity中。你没有必要仔细阅读具体内容，我只是引用说明一下观点。</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">  </span><span class="typ">FollowersRequest</span><span class="pln"> request </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">FollowersRequest</span><span class="pun">(</span><span class="pln">user</span><span class="pun">);</span><span class="pln">  
  lastRequestCacheKey </span><span class="pun">=</span><span class="pln"> request</span><span class="pun">.</span><span class="pln">createCacheKey</span><span class="pun">();</span><span class="pln">  
  spiceManager</span><span class="pun">.</span><span class="pln">execute</span><span class="pun">(</span><span class="pln">request</span><span class="pun">,</span><span class="pln"> lastRequestCacheKey</span><span class="pun">,</span><span class="pln">  
    </span><span class="typ">DurationInMillis</span><span class="pun">.</span><span class="pln">ONE_MINUTE</span><span class="pun">,</span><span class="pln"> 
    </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">RequestListener</span><span class="pun">&lt;</span><span class="typ">FollowerList</span><span class="pun">&gt;</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="lit">@Override</span><span class="pln">
      </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onRequestSuccess</span><span class="pun">(</span><span class="typ">FollowerList</span><span class="pln"> listFollowers</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="com">// On failure</span><span class="pln">
      </span><span class="pun">}</span><span class="pln">

      </span><span class="lit">@Override</span><span class="pln">
      </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onRequestFailure</span><span class="pun">(</span><span class="typ">SpiceException</span><span class="pln"> e</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
          </span><span class="com">// On success</span><span class="pln">
      </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">});</span></code></pre>

<p><code>Request</code>请求方式如下：</p>

<pre class="prettyprint prettyprinted"><code><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">FollowersRequest</span><span class="pln"> </span><span class="kwd">extends</span><span class="pln"> </span><span class="typ">SpringAndroidSpiceRequest</span><span class="pun">&lt;</span><span class="typ">FollowerList</span><span class="pun">&gt;</span><span class="pln"> </span><span class="pun">{</span><span class="pln">  
  </span><span class="kwd">private</span><span class="pln"> </span><span class="typ">String</span><span class="pln"> user</span><span class="pun">;</span><span class="pln">

  </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">FollowersRequest</span><span class="pun">(</span><span class="typ">String</span><span class="pln"> user</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
    </span><span class="kwd">super</span><span class="pun">(</span><span class="typ">FollowerList</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">);</span><span class="pln">
    </span><span class="kwd">this</span><span class="pun">.</span><span class="pln">user </span><span class="pun">=</span><span class="pln"> user</span><span class="pun">;</span><span class="pln">
  </span><span class="pun">}</span><span class="pln">

  </span><span class="lit">@Override</span><span class="pln">
  </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">FollowerList</span><span class="pln"> loadDataFromNetwork</span><span class="pun">()</span><span class="pln"> </span><span class="kwd">throws</span><span class="pln"> </span><span class="typ">Exception</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
    </span><span class="typ">String</span><span class="pln"> url </span><span class="pun">=</span><span class="pln"> format</span><span class="pun">(</span><span class="str">"https://api.github.com/users/%s/followers"</span><span class="pun">,</span><span class="pln"> user</span><span class="pun">);</span><span class="pln">
    </span><span class="kwd">return</span><span class="pln"> getRestTemplate</span><span class="pun">().</span><span class="pln">getForObject</span><span class="pun">(</span><span class="pln">url</span><span class="pun">,</span><span class="pln"> </span><span class="typ">FollowerList</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">);</span><span class="pln">
  </span><span class="pun">}</span><span class="pln">

  </span><span class="kwd">public</span><span class="pln"> </span><span class="typ">String</span><span class="pln"> createCacheKey</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
      </span><span class="kwd">return</span><span class="pln"> </span><span class="str">"followers."</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> user</span><span class="pun">;</span><span class="pln">
  </span><span class="pun">}</span><span class="pln">
</span><span class="pun">}</span></code></pre>

<h1 id="问题点上面实现代码有什么问题呢">问题点（上面实现代码有什么问题呢）：</h1>

<ol>
<li>这段代码看起来挺恐怖的，<strong>每一个</strong> <code>request</code> 请求，你都得这么写（太麻烦了）。</li>
<li>你还得为每一个<code>request</code>请求，创建一个<code>SpiceRequest</code>内部类。</li>
<li>你还得为每一个<code>request</code>，创建一个<code>RequestListener</code> </li>
<li>如果，缓存有效期太短，每次请求，用户就要等待很久。</li>
<li>如果，缓存有效期太长，用户每次看到数据都是过期的数据。</li>
<li><code>RequestListener</code> 持有一个<code>activity</code>的引用，会不会引起内存泄漏呢？</li>
</ol>

<p>所以说，没有那么完美的。</p>

<h1 id="5招搞定-简洁稳定-concise-and-robust-in-five-steps">5招搞定 ，简洁，稳定 (Concise and robust in five steps)</h1>

<p>在我搞<a href="https://play.google.com/store/apps/details?id=com.candyshop">Candyshop</a>的时候，我尝试了一些别的，我把一些不同类库中不错的特性，综合起来，并且，尽可能的保持简单稳定。</p>

<ul>
<li><a href="https://github.com/excilys/androidannotations">AndroidAnnotations</a> 负责<a href="https://github.com/excilys/androidannotations/wiki/WorkingWithThreads">@Background</a>注解(采用<strong>注解</strong>方式，标识为<strong>后台线程</strong>) ; <a href="https://github.com/excilys/androidannotations/wiki/Enhance-custom-classes">@EBean</a> (标识为<strong>模型类</strong>)等等…</li>
<li><a href="http://projects.spring.io/spring-android/">Spring RestTemplate</a> 负责 REST<a href="#fn:rest" id="fnref:rest" title="See footnote" class="footnote">1</a> 网络请求，可以跟<code>AndroidAnnotations</code> 很好的集成一起。</li>
<li><a href="https://github.com/nhachicha/SnappyDB">SnappyDB</a> 负责 <code>Java</code>对象快速磁盘缓存。</li>
<li><a href="https://github.com/greenrobot/EventBus">EventBus</a> 负责事件驱动。</li>
</ul>

<p>下面是整个大体结构，接下来会详细解释。</p>

<p><img src="http://joanzap.ghost.io/content/images/2014/Apr/article1_global_schema--2-.png" alt="global schema" title=""></p>

<h2 id="步骤一简单容易的使用缓存系统step-1-an-easy-to-use-cache-system">步骤一：简单容易的使用缓存系统（Step 1 — An easy to use cache system）</h2>

<p>你需要一个持久化的缓存机制，简单就好。</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">      </span><span class="lit">@EBean</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">Cache</span><span class="pln"> </span><span class="pun">{</span><span class="pln">  
        </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">static</span><span class="pln"> </span><span class="kwd">enum</span><span class="pln"> </span><span class="typ">CacheKey</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> USER</span><span class="pun">,</span><span class="pln"> CONTACTS</span><span class="pun">,</span><span class="pln"> </span><span class="pun">...</span><span class="pln"> </span><span class="pun">}</span><span class="pln">

        </span><span class="kwd">public</span><span class="pln"> </span><span class="pun">&lt;</span><span class="pln">T</span><span class="pun">&gt;</span><span class="pln"> T </span><span class="kwd">get</span><span class="pun">(</span><span class="typ">CacheKey</span><span class="pln"> key</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Class</span><span class="pun">&lt;</span><span class="pln">T</span><span class="pun">&gt;</span><span class="pln"> returnType</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="pun">...</span><span class="pln"> </span><span class="pun">}</span><span class="pln">
        </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> put</span><span class="pun">(</span><span class="typ">CacheKey</span><span class="pln"> key</span><span class="pun">,</span><span class="pln"> </span><span class="typ">Object</span><span class="pln"> value</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="pun">...</span><span class="pln"> </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<h2 id="步骤2一个rest客户端step-2-a-rest-client">步骤2：一个REST客户端（Step 2 — A REST client）</h2>

<p>我给的这个，仅做一个示例，只要确保你的 <code>REST API</code> 的逻辑代码，编写在一处地方（保证单一职责），就好了。</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="lit">@Rest</span><span class="pun">(</span><span class="pln">rootUrl </span><span class="pun">=</span><span class="pln"> </span><span class="str">"http://anything.com"</span><span class="pun">)</span><span class="pln">
</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">interface</span><span class="pln"> </span><span class="typ">CandyshopApi</span><span class="pln"> </span><span class="pun">{</span><span class="pln">

    </span><span class="lit">@Get</span><span class="pun">(</span><span class="str">"/api/contacts/"</span><span class="pun">)</span><span class="pln">
    </span><span class="typ">ContactsWrapper</span><span class="pln"> fetchContacts</span><span class="pun">();</span><span class="pln">

    </span><span class="lit">@Get</span><span class="pun">(</span><span class="str">"/api/user/"</span><span class="pun">)</span><span class="pln">
    </span><span class="typ">User</span><span class="pln"> fetchUser</span><span class="pun">();</span><span class="pln">

</span><span class="pun">}</span></code></pre>

<h2 id="步骤3一个贯穿整个应用的事件驱动step-3-an-application-wide-event-bus">步骤3：一个贯穿整个应用的事件驱动（Step 3 — An application-wide event bus）</h2>

<p>在合理的地方，创建一个实例，保证在App的任何地方都可以访问，在<code>Application</code> 中创建，就是一个不错的选择。</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">CandyshopApplication</span><span class="pln"> </span><span class="kwd">extends</span><span class="pln"> </span><span class="typ">Application</span><span class="pln"> </span><span class="pun">{</span><span class="pln">  
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">final</span><span class="pln"> </span><span class="kwd">static</span><span class="pln"> </span><span class="typ">EventBus</span><span class="pln"> BUS </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">EventBus</span><span class="pun">();</span><span class="pln">
    </span><span class="pun">...</span><span class="pln">
</span><span class="pun">}</span></code></pre>

<h2 id="步骤4一个需要加载数据的activitystep-4-an-activity-which-needs-some-data">步骤4：一个需要加载数据的Activity（Step 4 — An Activity which needs some data!）</h2>

<p>我的实现方式是，模拟<code>Robospice</code>基于一个<code>service</code>，不是Android自带的（经过定制的），一个常规的单列模式，整个应用都可以访问。在<strong>步骤5</strong> 中将展示service 的代码，现在，让我们先看看<code>Activity</code>中的代码如何书写， <br>
因为，这里，是第一个地方，我希望保持最简单的。</p>

<pre class="prettyprint prettyprinted"><code><span class="lit">@EActivity</span><span class="pun">(</span><span class="pln">R</span><span class="pun">.</span><span class="pln">layout</span><span class="pun">.</span><span class="pln">activity_main</span><span class="pun">)</span><span class="pln">
</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">MainActivity</span><span class="pln"> </span><span class="kwd">extends</span><span class="pln"> </span><span class="typ">Activity</span><span class="pln"> </span><span class="pun">{</span><span class="pln">

    </span><span class="com">// Inject the service</span><span class="pln">
    </span><span class="lit">@Bean</span><span class="pln"> </span><span class="kwd">protected</span><span class="pln"> </span><span class="typ">AppService</span><span class="pln"> appService</span><span class="pun">;</span><span class="pln">

    </span><span class="com">// Once everything is loaded…</span><span class="pln">
    </span><span class="lit">@AfterViews</span><span class="pln"> </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> afterViews</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="com">// … request the user and his contacts (returns immediately)</span><span class="pln">
        appService</span><span class="pun">.</span><span class="pln">getUser</span><span class="pun">();</span><span class="pln">
        appService</span><span class="pun">.</span><span class="pln">getContacts</span><span class="pun">();</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="com">/*
        The result of the previous calls will
        come as events through the EventBus.
        We'll probably update the UI, so we
        need to use @UiThread.
    */</span><span class="pln">

    </span><span class="lit">@UiThread</span><span class="pln"> </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onEvent</span><span class="pun">(</span><span class="typ">UserFetchedEvent</span><span class="pln"> e</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="pun">...</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="lit">@UiThread</span><span class="pln"> </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onEvent</span><span class="pun">(</span><span class="typ">ContactsFetchedEvent</span><span class="pln"> e</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="pun">...</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="com">// Register the activity in the event bus when it starts</span><span class="pln">
    </span><span class="lit">@Override</span><span class="pln"> </span><span class="kwd">protected</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onStart</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="kwd">super</span><span class="pun">.</span><span class="pln">onStart</span><span class="pun">();</span><span class="pln">
        BUS</span><span class="pun">.</span><span class="kwd">register</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">);</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="com">// Unregister it when it stops</span><span class="pln">
    </span><span class="lit">@Override</span><span class="pln"> </span><span class="kwd">protected</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onStop</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="kwd">super</span><span class="pun">.</span><span class="pln">onStop</span><span class="pun">();</span><span class="pln">
        BUS</span><span class="pun">.</span><span class="pln">unregister</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">);</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

</span><span class="pun">}</span></code></pre>

<p>一条线去request请求用户，一条线传递请求返回的响应，同样的事情关联一起，听上去不错的哦。</p>

<h2 id="步骤5一个单列的service-step-5-a-singleton-service">步骤5：一个单列的service （Step 5 — A singleton service）</h2>

<p>正如我在 <strong>步骤4</strong> 中提到，我使用的<code>service</code> 不是Android自带的<code>service</code>，事实上，我自己定制了一个，但是我改变了想法，理由是 <strong>极简</strong> ，当没有显示<code>Activity</code>的时候，或者，跟其他应用做数据交互的时候，<code>Services</code>会被用来处理各种任务，那些都不是我需要的。使用一个单列模式，可以使我们避免跟<code>ServiceConnection,</code> <code>Binder</code>, 等等…打交道的。</p>

<p>这里有很多东西要说明，首先，先让我们看一个大体结构，瞧瞧，当我在<code>Activity</code> 调用<code>getUser()</code> 和 <code>getContacts()</code>的是，都做哪些处理。接着，我会解释代码意思。</p>

<p>你可以假设<strong>每一串</strong> （就是下图中的<code>serial</code>标识的地方）都是一个线程。</p>

<p><img src="http://joanzap.ghost.io/content/images/2014/Apr/article1_serials--3-.png" alt="enter image description here" title=""></p>

<p>你这里所看到的呢，就是在这个模式中，我真正喜欢的地方；view视图直接用缓存数据填充，这样呢，用户就不需要等待了，接着，当请求的最新数据返回了时候，在更换界面上的数据，还有一点，你需要保证你的Activity 能接接收到同样类型的数据，当有多次请求的时候，在创建Activity的时候，要记住这一点，就好了。</p>

<p>好啦，我们看一下代码片段。</p>

<pre class="prettyprint prettyprinted"><code><span class="com">// As I said, a simple class, with a singleton scope</span><span class="pln">
</span><span class="lit">@EBean</span><span class="pun">(</span><span class="pln">scope </span><span class="pun">=</span><span class="pln"> </span><span class="typ">EBean</span><span class="pun">.</span><span class="typ">Scope</span><span class="pun">.</span><span class="typ">Singleton</span><span class="pun">)</span><span class="pln">
</span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">AppService</span><span class="pln"> </span><span class="pun">{</span><span class="pln">

    </span><span class="com">// (Explained later)</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">static</span><span class="pln"> </span><span class="kwd">final</span><span class="pln"> </span><span class="typ">String</span><span class="pln"> NETWORK </span><span class="pun">=</span><span class="pln"> </span><span class="str">"NETWORK"</span><span class="pun">;</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">static</span><span class="pln"> </span><span class="kwd">final</span><span class="pln"> </span><span class="typ">String</span><span class="pln"> CACHE </span><span class="pun">=</span><span class="pln"> </span><span class="str">"CACHE"</span><span class="pun">;</span><span class="pln">

    </span><span class="com">// Inject the cache (step 1)</span><span class="pln">
    </span><span class="lit">@Bean</span><span class="pln"> </span><span class="kwd">protected</span><span class="pln"> </span><span class="typ">Cache</span><span class="pln"> cache</span><span class="pun">;</span><span class="pln">

    </span><span class="com">// Inject the rest client (step 2)</span><span class="pln">
    </span><span class="lit">@RestService</span><span class="pln"> </span><span class="kwd">protected</span><span class="pln"> </span><span class="typ">CandyshopApi</span><span class="pln"> candyshopApi</span><span class="pun">;</span><span class="pln">

    </span><span class="com">// This is what the activity calls, it's public</span><span class="pln">
    </span><span class="lit">@Background</span><span class="pun">(</span><span class="pln">serial </span><span class="pun">=</span><span class="pln"> CACHE</span><span class="pun">)</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> getContacts</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">

        </span><span class="com">// Try to load the existing cache</span><span class="pln">
        </span><span class="typ">ContactsFetchedEvent</span><span class="pln"> cachedResult </span><span class="pun">=</span><span class="pln">
            cache</span><span class="pun">.</span><span class="kwd">get</span><span class="pun">(</span><span class="pln">KEY_CONTACTS</span><span class="pun">,</span><span class="pln"> </span><span class="typ">ContactsFetchedEvent</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">);</span><span class="pln">

        </span><span class="com">// If there's something in cache, send the event</span><span class="pln">
        </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">cachedResult </span><span class="pun">!=</span><span class="pln"> </span><span class="kwd">null</span><span class="pun">)</span><span class="pln"> BUS</span><span class="pun">.</span><span class="pln">post</span><span class="pun">(</span><span class="pln">cachedResult</span><span class="pun">);</span><span class="pln">

        </span><span class="com">// Then load from server, asynchronously</span><span class="pln">
        getContactsAsync</span><span class="pun">();</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="lit">@Background</span><span class="pun">(</span><span class="pln">serial </span><span class="pun">=</span><span class="pln"> NETWORK</span><span class="pun">)</span><span class="pln">
    </span><span class="kwd">private</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> getContactsAsync</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">

        </span><span class="com">// Fetch the contacts (network access)</span><span class="pln">
        </span><span class="typ">ContactsWrapper</span><span class="pln"> contacts </span><span class="pun">=</span><span class="pln"> candyshopApi</span><span class="pun">.</span><span class="pln">fetchContacts</span><span class="pun">();</span><span class="pln">

        </span><span class="com">// Create the resulting event</span><span class="pln">
        </span><span class="typ">ContactsFetchedEvent</span><span class="pln"> </span><span class="kwd">event</span><span class="pln"> </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">ContactsFetchedEvent</span><span class="pun">(</span><span class="pln">contacts</span><span class="pun">);</span><span class="pln">

        </span><span class="com">// Store the event in cache (replace existing if any)</span><span class="pln">
        cache</span><span class="pun">.</span><span class="pln">put</span><span class="pun">(</span><span class="pln">KEY_CONTACTS</span><span class="pun">,</span><span class="pln"> </span><span class="kwd">event</span><span class="pun">);</span><span class="pln">

        </span><span class="com">// Post the event</span><span class="pln">
        BUS</span><span class="pun">.</span><span class="pln">post</span><span class="pun">(</span><span class="kwd">event</span><span class="pun">);</span><span class="pln">

    </span><span class="pun">}</span><span class="pln">

</span><span class="pun">}</span></code></pre>

<p><strong>单个request请求就有许多代码</strong>，不过呢，我把它拆分了，使它看起来清晰，经常会出现类似模式的代码，可以简单，添加一个帮助类（<strong>helpers</strong>） ，抽成一个方法，直接在需要的调用。例如：<code>getUser()</code> 可以这样写：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="lit">@Background</span><span class="pun">(</span><span class="pln">serial </span><span class="pun">=</span><span class="pln"> CACHE</span><span class="pun">)</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> getUser</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        postIfPresent</span><span class="pun">(</span><span class="pln">KEY_USER</span><span class="pun">,</span><span class="pln"> </span><span class="typ">UserFetchedEvent</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">);</span><span class="pln">
        getUserAsync</span><span class="pun">();</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="lit">@Background</span><span class="pun">(</span><span class="pln">serial </span><span class="pun">=</span><span class="pln"> NETWORK</span><span class="pun">)</span><span class="pln">
    </span><span class="kwd">private</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> getUserAsync</span><span class="pun">()</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        cacheThenPost</span><span class="pun">(</span><span class="pln">KEY_USER</span><span class="pun">,</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">UserFetchedEvent</span><span class="pun">(</span><span class="pln">candyshopApi</span><span class="pun">.</span><span class="pln">fetchUser</span><span class="pun">()));</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<p>那<code>serial</code> （见图中）那一串做了哪些事情呢？下面是文档中描述的：</p>

<blockquote>
  <p>默认情况下，所有标有 <code>@Background</code>注解的的方法，都会并行运行，2个方法使用同一个系列<code>serial</code> 被保证运行在同一个线程中，有秩序的。（一个接一个的运行）</p>
</blockquote>

<p>网络请求也一个接一个的跑的话，可能会导致冲突，但是，却更容易处理像<code>GET-after-POST</code>（先提交后获取）这种类型任务，这样做呢，意味着，得牺牲一点点协调操作方面的东西了。另外，其实你可以很容易的通过协调serials 的顺序，来提高整体的协调，根据你的情况。目前，在我的<strong>Candyshop</strong> 应用中，我使用了4个不同的系列（<code>serials</code>）。</p>

<h2 id="来总结一下">来总结一下</h2>

<p>上面我所讲的只是一个草稿，只是一个基本的概念，几个月前弄的，现在，我能够解决各种情形中遇到的麻烦。我很享受我的工作，到目前。还有一些很赞的东西，关于这个框架模式的，我想分享给大家。比如说，错误管理，异常处理，POST 请求，终止无用的操作，但是 我真的很感谢你阅读这里，我不会发表上来的（作者好贱哦，小爷翻译了这么累）</p>

<p><strong>怎么样？你有没有找到你想要的设计呀？或者，你的日常工作，有没有一个很享受的事情啊？</strong> （你妹…..）</p>

<pre><code>Joan Zapata
Android Lead Developer at Candyshop
Paris, Francehttps://twitter.com/JoanZap
</code></pre>

<div class="footnotes"><hr><ol><li id="fn:rest">REST ：REST软件架构是由Roy Thomas Fielding博士在2000年首次提出的。他为我们描绘了开发基于互联网的网络软件的蓝图。REST软件架构是一个抽象的概念，是一种为了实现这一互联网的超媒体分布式系统的行动指南。利用任何的技术都可以实现这种理念。具体内容百度百科 <a href="#fnref:rest" title="Return to article" class="reversefootnote">↩</a></li></ol></div></div></body>
</html>