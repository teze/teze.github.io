---

layout: post
title: Android-code-optimization-part2
categories: [Android]
tags: [Android]
description:
fullview: true

---

 _Edit by Fooyou Email : <foyou23@qq.com>_ 
    
    2014/9/25

-------------------------------------
###优化建议  (续....2014-09-25)

## 5. Avoid Creating Unnecessary Objects （避免不必要对象创建）

## 6.Prefer Static Over Virtual（尽可能使用静态方法）

>**在一个类中编写方法时候，如果，你不需要访问这个类的成员变量，尽可能，把方法定义成静态的，在Android中，静态方法的调用比虚拟方法调用快15%-20%，因为这样做，可以告诉方法签名不需要改变对象状态 。**

## 7. Use Static Final For Constants（使用static，final，修饰`常量`）

**通常写法**	
>static int intVal = 42;
	static String strVal = "Hello, 
	
**改进写法：**	
>static final int intVal = 42;
static final String strVal = "Hello, world!";

##8. Avoid Internal Getters/Setters（避免内部Getters/Setters方法）

>  **在同一个类里面，就直接访问变量。在C++语言中，通常大家会使用`getters (i = getCount())` 而不是直接访问成员变量(i = mCount). 这个是很好的习惯，在其他面向对象编程中，比如C# 和Java中，这也是推荐的。但是在Android中，是不可取的，虚拟方法的调用开销比较大，比查找成员变量的方式（ 直接访问成员变量）开销大的多。但是，在对外公共接口，依然，可以使用这种getter/setter 方式。**
>  
**不使用JIT，直接访问全局成员变量，比通过“调用方法”访问的方式要快3倍。如果使用JIT（这种模式，全局变量和局部变量的调用速度一样快），则是快7倍。**
>
 **注意，如果你有使用[ProGuard](http://developer.android.com/tools/help/proguard.html)的话，上面2种方式都是可以，都能获得很好的性能，因为，`ProGuard` 会转换为`inline`（内连） 方式访问。**

##9.Consider Package Instead of Private Access with Private Inner Classes(避免内部类私有访问)
>**请看下面的代码：**

	 public class Foo {
	    private class Inner {
	        void stuff() {
	            Foo.this.doStuff(Foo.this.mValue);
	        }
	    }
	
	    private int mValue;
	
	    public void run() {
	        Inner in = new Inner();
	        mValue = 27;
	        in.stuff();
	    }
	
	    private void doStuff(int value) {
	        System.out.println("Value is " + value);
	    }
	}
>	
    重点是，这里定义了一个 (Foo$Inner)内部类，直接去访问外部类的私有变量，这里合法的，而且也能正常编译，也能打印出结果 `"Value is 27"` ，问题是，实际上，VM会认为这是不合法的，因为 Foo 和 Foo$Inner 是2个不同的类，即使Java 语法本身是合法的，为了衔接起来，编译器会生成一组虚拟的方法：

	/*package*/ static int Foo.access$100(Foo foo) {
			    return foo.mValue;
			}
	/*package*/ static void Foo.access$200(Foo foo, int value) {
			    foo.doStuff(value);
			}
>那实际上，内部类访问的是这一组静态方法的，在需要的时候，根据不同参数类型，调用不同的方法。	刚刚上面也讲到，调用方法访问方式是比较慢的，这些通常都是隐形的优化建议。
>如果你知道这些原理，那么当你在写代码的时候，就可以避免了，怎么样改进呢？很简单的，把那些，被 内部类访问的**private**变量定义为**package**级的域访问，也即是把 **private** 关键字去掉，默认**package**，这样就意味同包下的类都能访问了。

##10. Avoid Using Floating-Point（避免使用浮点型）
> 根据经验，在Android设备中，浮点型数据**floating-point**，比**integer** 慢**2倍**，速度上而言， 大部分机器上，**float** 和**double**是没有什么差别的，占用空间上，**double**是2倍，对PC 上应用来说，不是问题，你可以倾向于使用**double** 。
> 同样的，即使对于**integers**，处理器有硬件区别的，但是缺乏硬件拆分，这种情况下，**integer** 拆分和模块的操作，就会在软件层操作了，如果有一堆的**hash table** ，或者大量数学计算，这样操作，就可想而知了。
##11.Know and Use the Libraries（掌握一些有用的库）

> 尽量使用系统的库，如果可以，而不是自己去实现同样的功能，记住哦，系统库是可以任意替换的，并且总是会用针对**JIT**优化过的代码， 典型的例子 **String.indexOf()**， **Dalvik** 就是做过优化的，同样的 **System.arraycopy()**，这个方法比你手工实现的，（自己通过循环方式实现）要快**9倍**多，在带有**JIT**的**Nexus One**机器。

##12.Use Native Methods Carefully(注意使用JNI)
> 实际上，用 **Android NDK** 开发不是很有必要，也没有更高效，相对于Java开发，如果可以Java 语言实现。

##13.Performance Myths （操作神话）
> 在没有**JIT**的情况下，使用变量调用方法，比使用接口调用方法，更高效，（比如：使用**HashMap map** 变量调用相关方法，比 使用**Map map** 变量调用方法效率更高，即使 **2**个**map**变量都是 **HashMap**实现的），通常慢2倍，实际差别是 慢 **6%**。不过，将来，在**JIT**的设备上，2者不会有太多的差别了。
> 
> 在没有**JIT**设备上，缓存全局变量，比直接访问变量，快**20%** ，在有**JIT**设备上，全局变量，和局部变量是一样的效率。所以，没有必要特地去优化，除非你觉得这样做可以，提高代码可读性。（针对**final, static, and static final fields**一样适应）。

##14.Always Measure（总是度量 ）
> 使用精确的的技算，度量，在优化之前，确保你是有问题需要被解决。确保你能精确的度量你的现有的操作，不然话，你也不能确定，你所做的优化改进，到底有没有达到更好效果。

>数据说话。
>本文中很多精确的数据，测量，源码可以在 [code.google.com "dalvik" project](http://code.google.com/p/dalvik/source/browse/#svn/trunk/benchmarks) 中找到。

> 数据是用[Caliper](http://code.google.com/p/caliper/) 这个工具构建的。
>
>你会发现[Traceview](http://developer.android.com/tools/debugging/debugging-tracing.html) 
在优化中很有用的。


##15.Beautiful code
> 优雅的编码。

read more >> [google-styleguide](http://google-styleguide.googlecode.com/svn/trunk/javaguide.html)

未完待续。。。。。