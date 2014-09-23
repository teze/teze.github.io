---

layout: post
title: Memory Leak
categories: [Android]
tags: [Android]
description:
fullview: true

---

----------------------------------------------------------

###内存泄漏建议
--------------------------------


>  **_Edit by Fooyou Email : <foyou23@qq.com>_**


Cause 1: Context Leak
--------------------------

 - 内存泄漏点 ：Activity context 随意引用
 - 推荐方案：getApplicationContext()


Cause 2: The Handler Leak OR the Inner Class（handler和内部类本质上是同一种）
------------------------------------------------------
- 内存泄漏点：
`
public class SampleActivity extends Activity {
 
  private final Handler mLeakyHandler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
      // ...
    }
  }
 
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
 
    // Post a message and delay its execution for 10 minutes.
    mLeakyHandler.postDelayed(new Runnable() {
      @Override
      public void run() { }
    }, 60 * 10 * 1000);
 
    // Go back to the previous Activity.
    finish();
  }
}
`

-   推荐方案：
```
public class SampleActivity extends Activity {

  /**
   * Instances of static inner classes do not hold an implicit
   * reference to their outer class.
   */
  private static class MyHandler extends Handler {
    private final WeakReference<SampleActivity> mActivity;

    public MyHandler(SampleActivity activity) {
      mActivity = new WeakReference<SampleActivity>(activity);
    }

    @Override
    public void handleMessage(Message msg) {
      SampleActivity activity = mActivity.get();
      if (activity != null) {
        // ...
      }
    }
  }

  private final MyHandler mHandler = new MyHandler(this);

  /**
   * Instances of anonymous classes do not hold an implicit
   * reference to their outer class when they are "static".
   */
  private static final Runnable sRunnable = new Runnable() {
      @Override
      public void run() { }
  };

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // Post a message and delay its execution for 10 minutes.
    mHandler.postDelayed(sRunnable, 60 * 10 * 1000);
    
    // Go back to the previous Activity.
    finish();
  }
}
```



Cause 3: The Drawable Callback
--------------------------------------
- 内存泄漏点：Drawable 回调没有回收。
- 推荐方案：
```
private static void unbindDrawable(Drawable d) {
if (d != null)
d.setCallback(null);
}
```

Cause 4: The External Context
------------------------------------
- 内存泄漏点：外部的Context， 例如：各种输入法的对context的引用。
- 推荐方案：WeakReference解决。

Cause 5: The Too Large Objects
-------------------------------------
- 内存泄漏点：各种大对象，例如：list ，map。
- 推荐方案：根据程序具体逻辑，适当时候，清空。

Cause 6: The Database Connection
-----------------------------------------
- 内存泄漏点：没有关闭的数据库连接，网络连接，文件I/O操作。
- 推荐方案：相应的代码逻辑，在不需要使用时候，记得，调用各种close方法。
