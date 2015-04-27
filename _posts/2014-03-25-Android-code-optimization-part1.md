

 _Edit by Fooyou Email : <foyou23@qq.com>_ 
    
    2014/3/25

#优化建议 
-------------- 

## 1.内存泄漏 

**Context 内存泄漏示例 :** 
    
    private static Drawable sBackground;
    @Override
    protected void onCreate(Bundle state) {
      super.onCreate(state);
      
      TextView label = new TextView(this);
      label.setText("Leaks are bad");
      
      if (sBackground == null) {
        sBackground = getDrawable(R.drawable.large_bitmap);
      }
      label.setBackgroundDrawable(sBackground);
      
      setContentView(label);
    }

> **解析：**
当屏幕翻转的时候，`Activity` 会被重新创建，而第一次创建的Activity 会发生内存泄漏。
关键点在于`Drawable`是一个`static` 静态类，``Drawable`` 被重新创建时候，`Drawable`却不会。
因为 `Drawable` 被attached （调用`setBackgroundDrawable`）一个view 到时候，这个`view`就会被设置为`Drawable`的 callback ，也就是说`Drawable` 持有了一个`view`的引用，当前`view` 又持有`Activity Context`，这个时候 `GC` 就被阻止 回收 第一次创建的`Activity`。导致 第一次`Activity` 中所有的资源（类，变量 引用）都被阻止回收。  

>**改进方案：**
1.`Drawable` 引用周期跟随`Activity`周期，定义为非静态变量。
2.使用`Activity.getApplication()` Context 创建一个view。


**类似的，innerClass可能内存泄漏示例：**

    public class Leak {
    
    	private static LeakInnerClass innerClass;// 使用 静态 
    	
    	public boolean testLeak(Menu menu) {
    		
    		innerClass=new LeakInnerClass();
    		Loger.v("TAG", innerClass.toString());// 引用周期跟随Activity
    		
    		return true;
    	}
    	
    	class LeakInnerClass{// 非静态
    		//stuff
     	 }
    } 
    
 **改进，合理示例：**
 
    public class Leak {
    
    	private LeakInnerClass innerClass;// 使用 非静态 
    	
    	public boolean testLeak(Menu menu) {
    		
    		innerClass=new LeakInnerClass();
    		Loger.v("TAG", innerClass.toString());// 引用周期跟随Activity
    		
    		return true;
    	}
    	
    	class LeakInnerClass{// 非静态
    		//stuff
    		 
    	}
    	
    }
**改进,合理示例:**

    public class Leak {
    
    	private static LeakInnerClass innerClass;// 使用 静态 
    	
    	public boolean testLeak(Menu menu) {
    		
    		innerClass=new LeakInnerClass();
    		Loger.v("TAG", innerClass.toString());// 引用周期跟随Activity
    		
    		return true;
    	}
    	
    	static class LeakInnerClass{// 静态
    		//stuff
    		 
    	}
    }
>**总结：**

>- 不要对`context-activity` 保持长周期引用，引用到`context-activity` 的代码周期应跟`Activity`周期一样。
>- 尽可能用`context-application` 代替 `context-activity`   
>- 在一个Activity中，避免非静态的内部类的引用，如果不能很好控制其使用周期。可以使用静态 `inner class`.（如果可以，尽可能，使用weak 引用 ）


**持有对象引用的静态域** *(尤其是`final`)*

    class MemorableClass {
        static final ArrayList list = new ArrayList(100);
        static final ArrayList list = new ArrayList();//可以
    }

**没有关闭的 文件I/O操作:**

    try {
        BufferedReader br = new BufferedReader(new FileReader(inputFile));
        ...
        ...
    } catch (Exception e) {
        e.printStacktrace();
    }

**没有关闭的 连接，如：网络连接。**

    try {
        Connection conn = ConnectionFactory.getConnection();
        ...
        ...
    } catch (Exception e) {
        e.printStacktrace();
    }
>**Tips:** [更多优化建议](http://www.appperfect.com/support/java-coding-rules/optimization.html)

## 2.`for` or `for each` / `String` or `Stringbuilder`

**for 循环 建议 >> *不要在循环体里面调用方法***(用**for**还是**for each** 以后讨论)

    //不推荐：
    class Avoid_method_calls_in_loop_violation
    {
    	public void method()
    	{
    		String str = "Hello";
    		for (int i = 0; i < str.length(); i++)	// VIOLATION	
    	  	{
    			i++;
    		}
    	}
    }
 
    //应当这样写:
    class Avoid_method_calls_in_loop_correction
    {
    	public void method()
    	{
    		String str = "Hello";
    		int len = str.length();		// CORRECTION
    		for (int i = 0; i < len ; i++)
    	  	{
    			i++;
    		}
     }
    }

**大量字符串拼接建议使用StringBuidler or StringBuffer**

## 3.Bitmap & BitmapDrawable使用 >> 尽可能回收
    BitmapDrawable bd=(BitmapDrawable) view.getBackground();
		 bitmap=bd.getBitmap();
		 view.setBackgroundResource(0);
		 bd.setCallback(null);
		 bitmap=null;

## 4.Android 布局优化

>- 减少 `LinearLayout` 使用，替换为 `RelativeLayout`
>- 减少 `activity`作为`decoveiw` 使用，使用`fragment`替换。
>- 减少`layout_weight` 属性的使用（ 尤其在`listview` `gridview`中）。
>- 使用`FrameLayout`  `Merge` 代替 root  view

未完待续....

[google-styleguide](http://google-styleguide.googlecode.com/svn/trunk/javaguide.html)
