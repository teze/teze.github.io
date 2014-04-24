<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Android Tips Round-Up  Part 1</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h2 id="android-tips-round-up-part-1">Android Tips Round-Up  Part 1</h2>

<p>( <em>翻译</em> ) <a href="http://blog.danlew.net/2014/03/30/android-tips-round-up-part-1/">查看原文</a></p>

<p><em>Written by Fooyou Email : <a href="mailto:foyou23@qq.com">foyou23@qq.com</a></em> </p>

<pre><code>2014/4/24
</code></pre>

<p>在最近的一个<a href="http://blog.danlew.net/2014/03/17/android-tip-of-the-day/">项目</a>，我发表了一篇<code>one Android class/method a day</code> . 大家都来询问我这些文章的连接，所以，每隔2周，我都会打算把这些归纳总结到这。同时，我也会添加一些简单注释，但是，对我来说做这些仍然还不能成为我工作中的一部分。 <br>
更多：Part 1 | <a href="http://blog.danlew.net/2014/04/14/android-tips-round-up-part-2/">Part 2</a></p>

<ul>
<li><p><a href="http://developer.android.com/reference/android/app/Activity.html#startActivities%28android.content.Intent%5b%5d%29">Activity.startActivities() </a>– 开启多个Acitivity，非常适合开启一个中间要经过很多界面的应用流程。</p></li>
<li><p><a href="http://developer.android.com/reference/android/text/TextUtils.html#isEmpty%28java.lang.CharSequence%29">TextUtils.isEmpty()</a> &gt;&gt; 简单实用，判断字符是否空的工具类。</p></li>
<li><a href="http://developer.android.com/reference/android/text/TextUtils.html#isEmpty%28java.lang.CharSequence%29">Html.fromHtml()</a> &gt;&gt; 格式化HTML内容，很实用的。但是，其速度不够快，我用的不多(比如：字符加粗的时候，不适合使用，用手动构建<a href="http://flavienlaurent.com/blog/2014/01/31/spans/">Spannable</a>来代替比较合适）适用来渲染web网页。</li>
<li><a href="http://developer.android.com/reference/android/widget/TextView.html#setError%28java.lang.CharSequence%29">TextView.setError()</a> &gt;&gt;  很不错的，用来校验用户输入内容。</li>
<li><a href="http://developer.android.com/reference/android/os/Build.VERSION_CODES.html">Build.VERSION_CODES</a> &gt;&gt;  不仅可以看版本编号，还能很直观的看出各个版本意义。</li>
<li><a href="http://developer.android.com/reference/android/util/Log.html#getStackTraceString%28java.lang.Throwable%29">Log.getStackTraceString()</a> &gt;&gt; 打印追踪日志。</li>
<li><a href="http://developer.android.com/reference/android/view/ViewConfiguration.html#getScaledTouchSlop%28%29">ViewConfiguration.getScaledTouchSlop()</a> &gt;&gt;  获取系统触摸阀值，使用这个返回值，触摸交互就跟系统保持一致啦。</li>
<li><a href="http://developer.android.com/reference/android/telephony/PhoneNumberUtils.html#convertKeypadLettersToDigits%28java.lang.String%29">PhoneNumberUtils.convertKeypadLettersToDigits</a> &gt;&gt; 把键盘字母转化数字。</li>
<li><a href="http://developer.android.com/reference/android/content/Context.html#getCacheDir%28%29">Context.getCacheDir()</a> &gt;&gt; 获取缓存目录，实用。</li>
<li><a href="http://developer.android.com/reference/android/animation/ArgbEvaluator.html">ArgbEvaluator</a> &gt;&gt; 颜色argb模拟器。可以把一种颜色改变成另一种颜色。</li>
<li><a href="http://developer.android.com/reference/android/view/ContextThemeWrapper.html">ContextThemeWrapper</a> &gt;&gt; 程序运行时候，切换主题。这个很好用的。</li>
<li><a href="http://developer.android.com/reference/android/widget/Space.html">Space</a> &gt;&gt; 轻量级的视图控件，忽略绘画。空白，可以站位。</li>
<li><a href="http://developer.android.com/reference/android/animation/ValueAnimator.html#reverse%28%29">ValueAnimator.reverse()</a> &gt;&gt; 很平滑的终止动画。喜欢！</li>
</ul></div></body>
</html>