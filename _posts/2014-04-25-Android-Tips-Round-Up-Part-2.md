<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Android Tips Round-Up, Part 2.md</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h1 id="android-tips-round-up-part-2">Android Tips Round-Up, Part 2</h1>

<p>(翻译)<a href="http://blog.danlew.net/2014/04/14/android-tips-round-up-part-2/">原文</a></p>

<hr>

<p><em>Edit by Fooyou Email : <a href="mailto:foyou23@qq.com">foyou23@qq.com</a></em> </p>

<pre><code>10:51 2014-5-6
</code></pre>

<hr>

<p>这是我所发布的，第二篇关于Android 建议整理归纳。</p>

<p>更多: <a href="http://teze.github.io/Android-Tips-Round-Up-Part-1/">Part 1</a> | Part 2 | <a href="http://blog.danlew.net/2014/04/28/android-tips-round-up-part-3/">Part 3</a></p>

<ul>
<li><a href="http://developer.android.com/reference/android/text/format/DateUtils.html#formatDateTime%28android.content.Context,%20long,%20int%29">DateUtils.formatDateTime()</a>  &gt;&gt;  一步到位的时间日期格式化。</li>
<li><a href="http://developer.android.com/reference/android/app/AlarmManager.html#setInexactRepeating%28int,%20long,%20long,%20android.app.PendingIntent%29">AlarmManager.setInexactRepeating</a> &gt;&gt; 把所有的<code>alarms</code>定时器收集到一起，就算是你只有一个定时器，这样做也会更好的省电(这样可以确保当这个定时器完成后，<code>AlarmManager.cancel()</code> 被调用)。 </li>
<li><a href="http://developer.android.com/reference/android/text/format/Formatter.html#formatFileSize%28android.content.Context,%20long%29">Formatter.formatFileSize()</a> &gt;&gt;  获取本地化格式的文件大小。</li>
<li><a href="http://developer.android.com/reference/android/app/ActionBar.html#hide%28%29">ActionBar.hide()</a>/<a href="http://developer.android.com/reference/android/app/ActionBar.html#show%28%29">.show()</a> &gt;&gt;  带有动画效果的隐藏，打开菜单标题栏。可以用来切换全屏模式。</li>
<li><a href="http://developer.android.com/reference/android/text/util/Linkify.html#addLinks%28android.text.Spannable,%20int%29">Linkify.addLinks()</a> &gt;&gt; 给文本添加自定义链接。</li>
<li><a href="http://developer.android.com/reference/android/text/StaticLayout.html">StaticLayout</a> &gt;&gt; 对于绘制到自定义的视图的文本，用它可以很好的做相关计算。</li>
<li><a href="http://developer.android.com/reference/android/app/Activity.html#onBackPressed%28%29">Activity.onBackPressed()</a> &gt;&gt; 很好的控制返回键。有时候不能正常的拦截返回键，很有必要做一个流程控制。</li>
<li><a href="http://developer.android.com/reference/android/view/GestureDetector.html">GestureDetector</a>  &gt;&gt; 手势监听器，比自己实现一套动作监听容易。</li>
<li><a href="http://developer.android.com/reference/android/graphics/DrawFilter.html">DrawFilter</a> &gt;&gt;  很好的控制画布，即使你没有调用绘画命令，比如，你可以自定义一个view，并且设置DrawFilter，达到抗锯齿的效果。</li>
<li><a href="http://developer.android.com/reference/android/app/ActivityManager.html#getMemoryClass%28%29">ActivityManager.getMemoryClass()</a> &gt;&gt; 获取当前设备使用内存。方便控制缓存。</li>
<li><a href="http://developer.android.com/reference/android/os/SystemClock.html#sleep%28long%29">SystemClock.sleep()</a> &gt;&gt; 很方便的方法，控制线程休眠。可以用来调试，模拟网络延迟。</li>
<li><a href="http://developer.android.com/reference/android/view/ViewStub.html">ViewStub</a> &gt;&gt; 视图副本，初始化的时候不做任何处理，在运行时候动态填充。用于站位，延迟加载视图啊。不过，不支持<code>&lt;merge&gt;</code> 标签。如果不小心使用，会造成冗余的布局。</li>
<li><a href="http://developer.android.com/reference/android/util/DisplayMetrics.html#density">DisplayMetrics.density</a> &gt;&gt; 获取屏幕像素密度。通常时候，不用去控制缩放操作，比较好，但是有时候，适当控制是有必要的，尤其是自定义视图的时候。</li>
<li><a href="http://developer.android.com/reference/android/util/Pair.html#create%28A,%20B%29">Pair.create()</a> &gt;&gt; 很方便的方法，方便的构建器方法。</li>
</ul></div></body>
</html>