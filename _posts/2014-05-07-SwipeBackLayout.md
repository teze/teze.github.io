<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>2014-05-07-SwipeBackLayout.md</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><h1 id="swipebacklayout-滑动返回关闭界面控件">SwipeBackLayout 滑动返回关闭界面控件</h1>

<hr>

<p><em>Edit by Fooyou Email : <a href="mailto:foyou23@qq.com">foyou23@qq.com</a></em> </p>

<pre><code>15:31 05/07/2014
</code></pre>

<hr>

<p>因工作需要，在项目中需实现体验效果：在Android App 中，手势向右滑动，拖拽，关闭当前页面。具备飘逸的特效，阻泥效果。</p>

<p>经过一番调研摸索。有一个了解决方案：基于Android自带的控件 <code>SlidingPaneLayout</code> 做修改，可以实现需求效果。</p>

<p><a href="http://developer.android.com/reference/android/support/v4/widget/SlidingPaneLayout.html">SlidingPaneLayout</a> 是一个侧滑动栏。手势向右拉，可以弹出侧滑菜单的，于是就想，如果把滑动菜单挖空，透明。并且菜单滑动最右边时候，触发关闭当前Activity呢？带这个设想，做一个了尝试，实践证明是可行的。</p>

<p>先看看效果咯！</p>

<p><img src="https://drive.google.com/uc?id=0By_KX7XayXSldDJnc0hvMzdGRzA" alt="enter image description here" title=""></p>

<h2 id="实现步骤">实现步骤：</h2>

<ol>
<li>拷贝<code>SlidingPaneLayout</code>源码，做适当修改。代码篇幅比较长，这里不做讨论。后面会附上<code>GitHub repo</code>地址，具体内容看代码注释。</li>
<li><code>Activity</code>部分，我们需要重写一个Acitivity 中onPostCreate方法. 目的是：把<code>SlidingPaneLayout</code> 套入视图的顶层，即，让所有的视图控件都包含在这里面。这样随着<code>SlidingPaneLayout</code>滑动，整个界面也跟着滑动了。</li>
<li>配置<code>Activity</code>主题，使其窗口变透明，以至于，当<code>SlidingPaneLayout</code> 向右边滑动时候，能够透过当前窗口，看到上一个<code>Acitivity</code>的窗口（如图中<code>MainActivity</code>），这样就产生了滑动推拉的效果了。</li>
</ol>

<p>OK！ 上面是整体思路，下面我们看一下 关键点 代码片段。</p>

<blockquote>
  <p><strong>文件：SwipeBackActivity.java</strong></p>
</blockquote>

<pre class="prettyprint prettyprinted"><code><span class="kwd">public</span><span class="pln"> </span><span class="kwd">class</span><span class="pln"> </span><span class="typ">SwipeBackActivity</span><span class="pln"> </span><span class="kwd">extends</span><span class="pln"> </span><span class="typ">FragmentActivity</span><span class="pln"> </span><span class="kwd">implements</span><span class="pln"> </span><span class="typ">PanelSlideListener</span><span class="pun">{</span><span class="pln">

    </span><span class="pun">...</span><span class="pln">
    </span><span class="com">//其他代码忽略</span><span class="pln">
    </span><span class="pun">...</span><span class="pln">

    </span><span class="com">/**
     *  onPostCreate 方法是在Acitivity start 完成之后执行，拦截这里，操控调整布局，把 SlidingPaneLayout 套入当前界面。
     *  (after onStart and onRestoreInstanceState have been called)
     */</span><span class="pln">
    </span><span class="lit">@Override</span><span class="pln">
    </span><span class="kwd">protected</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onPostCreate</span><span class="pun">(</span><span class="typ">Bundle</span><span class="pln"> savedInstanceState</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="com">/*if (BuildConfig.DEBUG)Log.i(TAG, "onPostCreate &gt;&gt; swipeEnable &gt;&gt; "+swipeEnable);*/</span><span class="pln">
        </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">swipeEnable</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
            </span><span class="typ">ViewGroup</span><span class="pln"> localViewGroup </span><span class="pun">=</span><span class="pln"> </span><span class="pun">(</span><span class="typ">ViewGroup</span><span class="pun">)</span><span class="pln">getWindow</span><span class="pun">().</span><span class="pln">getDecorView</span><span class="pun">();</span><span class="pln">   </span><span class="com">//获取当前 窗口顶级DecorView</span><span class="pln">
            </span><span class="typ">View</span><span class="pln"> localView </span><span class="pun">=</span><span class="pln"> localViewGroup</span><span class="pun">.</span><span class="pln">getChildAt</span><span class="pun">(</span><span class="lit">0</span><span class="pun">);</span><span class="pln">
            localViewGroup</span><span class="pun">.</span><span class="pln">removeView</span><span class="pun">(</span><span class="pln">localView</span><span class="pun">);</span><span class="pln">       </span><span class="com">//  把contentView 先移除</span><span class="pln">
            </span><span class="typ">SlidingPaneLayout</span><span class="pln"> slidingPaneLayout</span><span class="pun">=</span><span class="kwd">new</span><span class="pln"> </span><span class="typ">SlidingPaneLayout</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">);</span><span class="pln">
            slidingPaneLayout</span><span class="pun">.</span><span class="pln">setPanelSlideListener</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">);</span><span class="pln">
            slidingPaneLayout</span><span class="pun">.</span><span class="pln">setSliderFadeColor</span><span class="pun">(</span><span class="pln">getResources</span><span class="pun">().</span><span class="pln">getColor</span><span class="pun">(</span><span class="pln">android</span><span class="pun">.</span><span class="pln">R</span><span class="pun">.</span><span class="pln">color</span><span class="pun">.</span><span class="pln">transparent</span><span class="pun">));</span><span class="pln">
            slidingPaneLayout</span><span class="pun">.</span><span class="pln">setShadowResource</span><span class="pun">(</span><span class="pln">mShadowResource</span><span class="pun">);</span><span class="pln">
            </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">mShadowDrawable</span><span class="pun">!=</span><span class="kwd">null</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
                slidingPaneLayout</span><span class="pun">.</span><span class="pln">setShadowDrawable</span><span class="pun">(</span><span class="pln">mShadowDrawable</span><span class="pun">);</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
            slidingPaneLayout</span><span class="pun">.</span><span class="pln">setCoveredFadeColor</span><span class="pun">(</span><span class="pln">getResources</span><span class="pun">().</span><span class="pln">getColor</span><span class="pun">(</span><span class="pln">android</span><span class="pun">.</span><span class="pln">R</span><span class="pun">.</span><span class="pln">color</span><span class="pun">.</span><span class="pln">transparent</span><span class="pun">));</span><span class="pln">
            </span><span class="com">/*slidingPaneLayout.setBackgroundColor(Color.alpha(0));*/</span><span class="pln">

            </span><span class="typ">View</span><span class="pln"> localViewTemp</span><span class="pun">=</span><span class="kwd">new</span><span class="pln"> </span><span class="typ">TextView</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">);</span><span class="pln">
            </span><span class="com">/*localViewTemp.setTextColor(getResources().getColor(R.color.beta));
            localViewTemp.setBackgroundResource(android.R.color.black);*/</span><span class="pln">
            </span><span class="typ">ViewHelper</span><span class="pun">.</span><span class="pln">setAlpha</span><span class="pun">(</span><span class="pln">localViewTemp</span><span class="pun">,</span><span class="pln"> </span><span class="lit">0.0f</span><span class="pun">);</span><span class="pln">
            </span><span class="typ">ViewGroup</span><span class="pun">.</span><span class="typ">LayoutParams</span><span class="pln"> localLayoutParams </span><span class="pun">=</span><span class="pln"> </span><span class="kwd">new</span><span class="pln"> </span><span class="typ">ViewGroup</span><span class="pun">.</span><span class="typ">LayoutParams</span><span class="pun">(</span><span class="typ">LayoutParams</span><span class="pun">.</span><span class="pln">MATCH_PARENT</span><span class="pun">,</span><span class="pln"> </span><span class="typ">LayoutParams</span><span class="pun">.</span><span class="pln">MATCH_PARENT</span><span class="pun">);</span><span class="pln">
            slidingPaneLayout</span><span class="pun">.</span><span class="pln">addView</span><span class="pun">(</span><span class="pln">localViewTemp</span><span class="pun">,</span><span class="pln"> localLayoutParams</span><span class="pun">);</span><span class="pln">
            slidingPaneLayout</span><span class="pun">.</span><span class="pln">addView</span><span class="pun">(</span><span class="pln">localView</span><span class="pun">);</span><span class="pln"> </span><span class="com">// 构建完slidingPaneLayout 后，把上面 移除的 当前视图contentView ,在添加到slidingPaneLayout上去。</span><span class="pln">
            localViewGroup</span><span class="pun">.</span><span class="pln">addView</span><span class="pun">(</span><span class="pln">slidingPaneLayout</span><span class="pun">);</span><span class="com">// 最后 把slidingPaneLayout 当作窗口的contentView 。</span><span class="pln">
            getWindow</span><span class="pun">().</span><span class="pln">setBackgroundDrawableResource</span><span class="pun">(</span><span class="pln">android</span><span class="pun">.</span><span class="pln">R</span><span class="pun">.</span><span class="pln">color</span><span class="pun">.</span><span class="pln">transparent</span><span class="pun">);</span><span class="com">// 较为关键，把当前窗口设置为透明。</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">
        </span><span class="kwd">super</span><span class="pun">.</span><span class="pln">onPostCreate</span><span class="pun">(</span><span class="pln">savedInstanceState</span><span class="pun">);</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="pun">...</span><span class="pln">
    </span><span class="com">//其他代码忽略</span><span class="pln">
    </span><span class="pun">...</span></code></pre>

<p>这个方法在<code>Acitivity</code>中是核心，目的就是替换当前窗口的<code>contentView</code>，把<code>slidingPaneLayout</code> 嵌套其中。</p>

<p>是否有注意到，当前<code>Acitivity</code> 需实现<code>PanelSlideListener</code>接口。</p>

<pre class="prettyprint prettyprinted"><code><span class="lit">@Override</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onPanelSlide</span><span class="pun">(</span><span class="typ">View</span><span class="pln"> panel</span><span class="pun">,</span><span class="pln"> </span><span class="kwd">float</span><span class="pln"> slideOffset</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">

    </span><span class="lit">@Override</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> onPanelOpened</span><span class="pun">(</span><span class="typ">View</span><span class="pln"> panel</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="kwd">super</span><span class="pun">.</span><span class="pln">finish</span><span class="pun">();</span><span class="pln">
    </span><span class="pun">}</span></code></pre>

<blockquote>
  <p><strong>文件：SlidingPaneLayout.java</strong></p>
</blockquote>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="pun">...</span><span class="pln">
    </span><span class="com">//其他代码忽略</span><span class="pln">
    </span><span class="pun">...</span><span class="pln">

    </span><span class="com">// 这个片段，是把子控件中包含 左右滑动的控件 挑出来。</span><span class="pln">
    </span><span class="com">//对于他们的触控onTouch事件，需要单独处理。</span><span class="pln">

    </span><span class="kwd">private</span><span class="pln"> </span><span class="kwd">void</span><span class="pln"> findIgnoredView</span><span class="pun">(</span><span class="typ">View</span><span class="pln"> v</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="com">/*if(BuildConfig.DEBUG)Log.d(TAG, "findIgnoredView");*/</span><span class="pln">
        </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">v </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">ViewGroup</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
            </span><span class="typ">ViewGroup</span><span class="pln"> vg </span><span class="pun">=</span><span class="pln"> </span><span class="pun">(</span><span class="typ">ViewGroup</span><span class="pun">)</span><span class="pln"> v</span><span class="pun">;</span><span class="pln">
            </span><span class="kwd">for</span><span class="pln"> </span><span class="pun">(</span><span class="kwd">int</span><span class="pln"> i </span><span class="pun">=</span><span class="pln"> </span><span class="lit">0</span><span class="pun">;</span><span class="pln"> i </span><span class="pun">&lt;</span><span class="pln"> vg</span><span class="pun">.</span><span class="pln">getChildCount</span><span class="pun">();</span><span class="pln"> i</span><span class="pun">++)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
                </span><span class="kwd">final</span><span class="pln"> </span><span class="typ">View</span><span class="pln"> child </span><span class="pun">=</span><span class="pln"> vg</span><span class="pun">.</span><span class="pln">getChildAt</span><span class="pun">(</span><span class="pln">i</span><span class="pun">);</span><span class="pln">
                </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">child </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">ViewPager</span><span class="pln">
                        </span><span class="pun">||</span><span class="pln"> child </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">ScrollView</span><span class="pln">
                        </span><span class="pun">||</span><span class="pln"> child </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">Gallery</span><span class="pln">
                        </span><span class="pun">||</span><span class="pln"> child </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">HorizontalScrollView</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
                    mIgnoredViews</span><span class="pun">.</span><span class="pln">add</span><span class="pun">(</span><span class="pln">child</span><span class="pun">);</span><span class="pln">
                </span><span class="pun">}</span><span class="pln"> </span><span class="kwd">else</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
                    findIgnoredView</span><span class="pun">(</span><span class="pln">child</span><span class="pun">);</span><span class="pln">
                </span><span class="pun">}</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
        </span><span class="pun">}</span><span class="pln"> </span><span class="kwd">else</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
            </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">v </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">ViewPager</span><span class="pln">
                    </span><span class="pun">||</span><span class="pln"> v </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">ScrollView</span><span class="pln">
                    </span><span class="pun">||</span><span class="pln"> v </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">Gallery</span><span class="pln">
                    </span><span class="pun">||</span><span class="pln"> v </span><span class="kwd">instanceof</span><span class="pln"> </span><span class="typ">HorizontalScrollView</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
                mIgnoredViews</span><span class="pun">.</span><span class="pln">add</span><span class="pun">(</span><span class="pln">v</span><span class="pun">);</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">
    </span><span class="pun">}</span><span class="pln">


     </span><span class="lit">@Override</span><span class="pln">
    </span><span class="kwd">public</span><span class="pln"> </span><span class="kwd">boolean</span><span class="pln"> onInterceptTouchEvent</span><span class="pun">(</span><span class="typ">MotionEvent</span><span class="pln"> ev</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
        </span><span class="kwd">final</span><span class="pln"> </span><span class="kwd">int</span><span class="pln"> action </span><span class="pun">=</span><span class="pln"> </span><span class="typ">MotionEventCompat</span><span class="pun">.</span><span class="pln">getActionMasked</span><span class="pun">(</span><span class="pln">ev</span><span class="pun">);</span><span class="pln">

        </span><span class="com">// Preserve the open state based on the last view that was touched.</span><span class="pln">
        </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(!</span><span class="pln">mCanSlide </span><span class="pun">&amp;&amp;</span><span class="pln"> action </span><span class="pun">==</span><span class="pln"> </span><span class="typ">MotionEvent</span><span class="pun">.</span><span class="pln">ACTION_DOWN </span><span class="pun">&amp;&amp;</span><span class="pln"> getChildCount</span><span class="pun">()</span><span class="pln"> </span><span class="pun">&gt;</span><span class="pln"> </span><span class="lit">1</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
            </span><span class="com">// After the first things will be slideable.</span><span class="pln">
            </span><span class="kwd">final</span><span class="pln"> </span><span class="typ">View</span><span class="pln"> secondChild </span><span class="pun">=</span><span class="pln"> getChildAt</span><span class="pun">(</span><span class="lit">1</span><span class="pun">);</span><span class="pln">
            </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">secondChild </span><span class="pun">!=</span><span class="pln"> </span><span class="kwd">null</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
                mPreservedOpenState </span><span class="pun">=</span><span class="pln"> </span><span class="pun">!</span><span class="pln">mDragHelper</span><span class="pun">.</span><span class="pln">isViewUnder</span><span class="pun">(</span><span class="pln">secondChild</span><span class="pun">,</span><span class="pln">
                        </span><span class="pun">(</span><span class="kwd">int</span><span class="pun">)</span><span class="pln"> ev</span><span class="pun">.</span><span class="pln">getX</span><span class="pun">(),</span><span class="pln"> </span><span class="pun">(</span><span class="kwd">int</span><span class="pun">)</span><span class="pln"> ev</span><span class="pun">.</span><span class="pln">getY</span><span class="pun">());</span><span class="pln">
            </span><span class="pun">}</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">

        </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(!</span><span class="pln">mCanSlide </span><span class="pun">||</span><span class="pln"> </span><span class="pun">(</span><span class="pln">mIsUnableToDrag </span><span class="pun">&amp;&amp;</span><span class="pln"> action </span><span class="pun">!=</span><span class="pln"> </span><span class="typ">MotionEvent</span><span class="pun">.</span><span class="pln">ACTION_DOWN</span><span class="pun">))</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
            mDragHelper</span><span class="pun">.</span><span class="pln">cancel</span><span class="pun">();</span><span class="pln">
            </span><span class="kwd">return</span><span class="pln"> </span><span class="kwd">super</span><span class="pun">.</span><span class="pln">onInterceptTouchEvent</span><span class="pun">(</span><span class="pln">ev</span><span class="pun">);</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">

        </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">action </span><span class="pun">==</span><span class="pln"> </span><span class="typ">MotionEvent</span><span class="pun">.</span><span class="pln">ACTION_CANCEL </span><span class="pun">||</span><span class="pln"> action </span><span class="pun">==</span><span class="pln"> </span><span class="typ">MotionEvent</span><span class="pun">.</span><span class="pln">ACTION_UP</span><span class="pun">)</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
            mDragHelper</span><span class="pun">.</span><span class="pln">cancel</span><span class="pun">();</span><span class="pln">
            </span><span class="kwd">return</span><span class="pln"> </span><span class="kwd">false</span><span class="pun">;</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">

        </span><span class="com">//关键点，当遇到ScrollView，ViewPager 诸如此类的控件，他们本身有左右滑动事件，避免冲突，再次做拦截。</span><span class="pln">
        </span><span class="kwd">if</span><span class="pln"> </span><span class="pun">(</span><span class="pln">isIgnoredViewUnder</span><span class="pun">(</span><span class="pln">ev</span><span class="pun">))</span><span class="pln"> </span><span class="pun">{</span><span class="com">//hack by Fooyou</span><span class="pln">
            mDragHelper</span><span class="pun">.</span><span class="pln">cancel</span><span class="pun">();</span><span class="pln">
            </span><span class="kwd">return</span><span class="pln"> </span><span class="kwd">false</span><span class="pun">;</span><span class="pln">
        </span><span class="pun">}</span><span class="pln">

    </span><span class="pun">...</span><span class="pln">
    </span><span class="com">//其他代码忽略</span><span class="pln">
    </span><span class="pun">...</span></code></pre>

<blockquote>
  <p><strong>文件：styles.xml</strong></p>
</blockquote>

<pre class="prettyprint prettyprinted"><code><span class="pln">    </span><span class="com">// 这里我们定义了2个主题，带左右推拉动画，透明属性。</span><span class="pln">
     </span><span class="pun">&lt;</span><span class="pln">style name</span><span class="pun">=</span><span class="str">"CustomTheme.Animation"</span><span class="pln"> parent</span><span class="pun">=</span><span class="str">"AppBaseTheme"</span><span class="pun">&gt;</span><span class="pln">
        </span><span class="pun">&lt;</span><span class="pln">item name</span><span class="pun">=</span><span class="str">"android:windowIsTranslucent"</span><span class="pun">&gt;</span><span class="kwd">true</span><span class="pun">&lt;</span><span class="str">/item&gt;
        &lt;item name="android:windowBackground"&gt;@color/</span><span class="pln">alpa</span><span class="pun">&lt;</span><span class="str">/item&gt;
        &lt;item name="android:windowAnimationStyle"&gt;@style/</span><span class="typ">AnimationActivity</span><span class="pun">&lt;</span><span class="str">/item&gt;
    &lt;/</span><span class="pln">style</span><span class="pun">&gt;</span><span class="pln">

    </span><span class="pun">&lt;</span><span class="pln">style name</span><span class="pun">=</span><span class="str">"AnimationActivity"</span><span class="pln"> parent</span><span class="pun">=</span><span class="str">"@android:style/Animation.Activity"</span><span class="pun">&gt;</span><span class="pln">
        </span><span class="pun">&lt;</span><span class="pln">item name</span><span class="pun">=</span><span class="str">"android:activityOpenEnterAnimation"</span><span class="pun">&gt;</span><span class="lit">@anim</span><span class="pun">/</span><span class="pln">in_from_right</span><span class="pun">&lt;</span><span class="str">/item&gt;
        &lt;item name="android:activityOpenExitAnimation"&gt;@anim/</span><span class="pln">out_to_left</span><span class="pun">&lt;</span><span class="str">/item&gt;
        &lt;item name="android:activityCloseEnterAnimation"&gt;@anim/</span><span class="pln">in_from_left</span><span class="pun">&lt;</span><span class="str">/item&gt;
        &lt;item name="android:activityCloseExitAnimation"&gt;@anim/</span><span class="pln">out_to_right</span><span class="pun">&lt;</span><span class="str">/item&gt;
        &lt;!-- &lt;item name="android:activityCloseExitAnimation"&gt;@anim/</span><span class="pln">fade_out</span><span class="pun">&lt;</span><span class="str">/item&gt; --&gt;
    &lt;/</span><span class="pln">style</span><span class="pun">&gt;</span></code></pre>

<blockquote>
  <p><strong>文件：AndroidManifest.xml</strong></p>
</blockquote>

<pre class="prettyprint prettyprinted"><code><span class="com">// 这里是主配置文件，注意到，部分Acitivity 设置我们上面定义的主题。</span><span class="pln">
 </span><span class="pun">&lt;</span><span class="pln">application
        android</span><span class="pun">:</span><span class="pln">allowBackup</span><span class="pun">=</span><span class="str">"true"</span><span class="pln">
        android</span><span class="pun">:</span><span class="pln">icon</span><span class="pun">=</span><span class="str">"@drawable/ic_launcher"</span><span class="pln">
        android</span><span class="pun">:</span><span class="pln">label</span><span class="pun">=</span><span class="str">"@string/app_name"</span><span class="pln"> </span><span class="pun">&gt;</span><span class="pln">
        </span><span class="pun">&lt;</span><span class="pln">activity
            android</span><span class="pun">:</span><span class="pln">name</span><span class="pun">=</span><span class="str">"name.teze.test.MainActivityB"</span><span class="pln">
            android</span><span class="pun">:</span><span class="pln">label</span><span class="pun">=</span><span class="str">"@string/title_activity_b"</span><span class="pln">
            android</span><span class="pun">:</span><span class="pln">theme</span><span class="pun">=</span><span class="str">"@style/CustomTheme.Animation"</span><span class="pln"> </span><span class="pun">&gt;</span><span class="pln">
        </span><span class="pun">&lt;</span><span class="str">/activity&gt;
        &lt;activity
            android:name="name.teze.test.MainActivityA"
            android:label="@string/</span><span class="pln">title_activity_main</span><span class="str">"
            android:theme="</span><span class="lit">@style</span><span class="pun">/</span><span class="typ">AppTheme</span><span class="str">" &gt;
            &lt;intent-filter&gt;
                &lt;action android:name="</span><span class="pln">android</span><span class="pun">.</span><span class="pln">intent</span><span class="pun">.</span><span class="pln">action</span><span class="pun">.</span><span class="pln">MAIN</span><span class="str">" /&gt;

                &lt;category android:name="</span><span class="pln">android</span><span class="pun">.</span><span class="pln">intent</span><span class="pun">.</span><span class="pln">category</span><span class="pun">.</span><span class="pln">LAUNCHER</span><span class="str">" /&gt;
            &lt;/intent-filter&gt;
        &lt;/activity&gt;
        &lt;activity
            android:name="</span><span class="pln">name</span><span class="pun">.</span><span class="pln">teze</span><span class="pun">.</span><span class="pln">test</span><span class="pun">.</span><span class="typ">MainActivityC</span><span class="str">"
            android:label="</span><span class="lit">@string</span><span class="pun">/</span><span class="pln">title_activity_c</span><span class="str">"
            android:theme="</span><span class="lit">@style</span><span class="pun">/</span><span class="typ">CustomTheme</span><span class="pun">.</span><span class="typ">Animation</span><span class="str">" &gt;
        &lt;/activity&gt;
        &lt;activity
            android:name="</span><span class="pln">name</span><span class="pun">.</span><span class="pln">teze</span><span class="pun">.</span><span class="pln">layout</span><span class="pun">.</span><span class="pln">lib</span><span class="pun">.</span><span class="typ">SwipeBackActivity</span><span class="str">"
            android:label="</span><span class="lit">@string</span><span class="pun">/</span><span class="pln">title_activity_swipe_back</span><span class="str">" &gt;
        &lt;/activity&gt;
    &lt;/application&gt;</span></code></pre>

<p>关键点代码，主要是这些。</p>

<h2 id="代码下载">代码下载</h2>

<p>程序这东西，看代码比较容易理解。</p>

<p><a href="https://github.com/teze/swipebacklayout">GitHub</a>项目地址。</p></div></body>
</html>