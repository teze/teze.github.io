<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>MVP for Android</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h1 id="mvp-for-android-如何组织表现层">MVP for Android :如何组织“表现层”</h1>

<hr>

<p>( <em>翻译</em> ) <a href="http://antonioleiva.com/mvp-android/">查看原文</a></p>

<p><em>Written by Fooyou Email : <a href="mailto:foyou23@qq.com">foyou23@qq.com</a></em> </p>

<pre><code>2014/3/25
</code></pre>

<hr>

<p><strong>MVP</strong> 模式(Model View Presenter,模型，视图，表现)是一个衍生于大家熟知的 <strong>MVC</strong>模式 (Model View Controller,模型，视图，控制器)，一时间，它在Android应用开发中变得越来越重要了，越来越多的人们开始讨论这个模式，但是没有一个靠谱的，结构化的信息。这就是我为什么要写这个博客，来鼓励更多人们参与到这个讨论中，提供各种方案，思路，来完善这个模式，以便于更好的服务我们的开发项目。</p>

<h2 id="what-is-mvp什么是mvp">What is MVP?（什么是MVP）</h2>

<p><strong>MVP</strong> 模式是允许 <strong><code>表现层</code></strong>和<strong><code>逻辑层</code></strong>分离，这样呢，就可以将任何逻辑接口层和屏幕视图呈现逻辑分离解耦了，理论上 <strong>MVP</strong> 模式可以使得，同样一套逻辑代码，可以套用不同的视图<strong><code>（译者：就像Web开发中MVC，同一套逻辑层可以套用不同的css样式一样）</code></strong> 。</p>

<p>首先要明确一点，<strong>MVP</strong>不是一个<strong>架构模式</strong>，它只充当一个表现层的角色，不管怎么样，在你的项目结构中运用一点这个思想，总比一点都不使用要更好把。</p>

<h2 id="why-use-mvp为什么要是使用mvp">Why use MVP?（为什么要是使用MVP）</h2>

<p>在Android开发中，我们发现有一个问题原来越明显，那就是Android的Activity 与接口层，数据访问层，的耦合性越来越大，比如一个极端的例子：<strong><code>CursorAdapter</code></strong>，这个类把视图，适配器，还有一下数据访问层的操作，全部杂合在一起了。</p>

<p>对一个应用，想要更好的扩展性和维护性，我们就需要定义一个好的分层结构。试想，如果哪天我们需要从一个web服务器获取数据，而不是从本地数据库获取数据，这是不是就意味着我们得重新编写所有的<strong>视图</strong>模块了？</p>

<p>MVP可以使所有的<strong>视图</strong> 独立于<strong>数据</strong> <code>（译者：也就是数据解耦）</code>，我们把一个应用至少分层3个层次，这样呢，就可以让他们相互独立，使用<strong>MVP</strong> 模式，可以做到大部分的逻辑层独立于Activity操作，以此同时，我们以后做测试的时候，也可以不需要使用<strong>instrumentation tests</strong>。</p>

<h2 id="how-to-implement-mvp-for-android如何实现mvp-for-android">How to implement MVP for Android（如何实现MVP for Android）</h2>

<p>额…这个地方是很多开发者众说纷纭，见仁见智了，有很多<strong>MVP</strong> 模式的实现方式，而且每个人可以用他们自己的方式把这个模式思想更好的应用他们的需求，用起来更爽！这些<strong>实现模式</strong>不同点，基本上取决于我们对<strong>表现层</strong>职责的划分。</p>

<p>比如：一个进度条开启或者关闭，是交给<strong>视图类</strong>负责控制呢？还是只能由<strong>表现层类</strong>控制？再比如：由谁（<strong>表现层</strong>还是<strong>视图层</strong>）来控制Action Bar的显示呢？这些仅仅使我们要思考讨论的一小部分呢。我将会展示我通常是如何处理的，但是，我更希望这个篇文章有更多的人参与讨论，发表你们实现<strong>MVP</strong>模式的思路，因为，要知道，这并没有一个严格的标准实现方式的。</p>

<blockquote>
  <p>（译者：以下是作者推荐的一套实现方式）</p>
</blockquote>

<h2 id="the-presenter表现层">The presenter（表现层）</h2>

<p><strong>表现层</strong> 的职责是，充当<strong>视图层（View）</strong>和<strong>模型层（model）</strong> 之间的纽带，它负责从 <strong>model</strong> 中获取数据，然后经过格式处理 返回给 <strong>视图 view</strong>，但是跟典型的<strong>MVC</strong>有点不一样，它还负责控制视图<strong>用户交互</strong>逻辑。</p>

<h2 id="the-view视图层">The View（视图层）</h2>

<p>视图，通常是一个<code>Activity</code>(也有可能是：<code>Fragment</code>, a <code>View</code>等等，这取决于的程序结构了)，在这个<code>Acitivity</code>中，持有一个<strong>presenter（表现层）</strong>的引用，理论上呢，这个<strong>presenter</strong> 可以通过依赖注入方式引入，比如,可以使用<a href="http://square.github.io/dagger/">Dagger</a>(一个依赖注入的类，这一点可以参考<code>web</code>开发中的<code>spring</code>框架)，但是，有时候，你也不能这么干，因为这会导致，<code>Acitivity</code>还要负责<code>presenter</code>的创建了。视图仅仅只负责调用<code>presenter</code>的方法接口响应操作（例如按钮点击。。。）。</p>

<h2 id="the-model模型层">The model（模型层）</h2>

<p>一个层次结构清晰的应用，模型<strong>model</strong>，仅仅只是充当各个层之间的一扇门，如果，我们按照 <a href="http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html">Uncle Bob clean architecture</a>这个结构模式的话，<strong>model</strong> 也可以做为用例的纽带，这个就另说了，以后讨论，现在，模型<strong>model</strong>只充当数据的载体，提供给<strong>视图</strong>使用。</p>

<h2 id="an-example举一个例子啦">An example（举一个例子啦）</h2>

<p>说了这么一堆来解释，我创建一个例子,它包含一个登入页面，在里面验证数据，可以访问列表数据，数据是从模型中解析出来的，文章就不对代码做解释了，很简单的，如果你觉得哪里看不懂的，我可以创建一个另外一篇文章来详细阐述。 <br>
<a href="https://github.com/antoniolg/androidmvp">Go to MVP example on Github</a></p>

<h2 id="conclusion总结">Conclusion（总结）</h2>

<p>在Android 中，要把接口和逻辑分开不是一件不容易的事情。但是<strong>Model-View-Presenter</strong> 模式 可以使得Acitivity里面不再堆一大堆复杂的代码，成千上万行的代码。在大型应用里面，管理好代码是很有必要的。不然，就会变得很难扩展维护的。</p></div></body>
</html>