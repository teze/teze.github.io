
# Robust and readable a App(健壮的，可读性高的框架 for  Android App)

(翻译)[原文](http://joanzap.ghost.io/robust-architecture-for-an-android-app/)

----------------------------------------

 _Edit by Fooyou Email : foyou23#qq.com_
 
    10:03 2014-5-4
    
----------------------------------------
 
  很早以前，我就一直在寻找一个健壮的方式构建Android应用，它能够做到，保持IO 操作在UI线程之外，避免重复冗余的网络请求，合理的缓存资源，合理的更新缓存，等等...能够变得更有条理，结构清晰。
    
**这篇博客，不会提供一个精确的实现方式，只是给出一个可行的参考方案，来构建一个具有协调的，良好可读性，健壮，可扩展的应用。**

一些现有的解决方案，Some existing solutions
---------------------------------------------
  Android一开始的时候，大部分的人都依靠[AsyncTasks][1]来跑一个较长时间的处理任务，事实上，这个很烂的，已经有大量的文章关于这个部分的方案的。比如，后来的`Honeycomb` 引入了[Loaders][2]，它能够更好的支持配置变化，在2012年的时候，[Robospice][3]出现,基于一个后台`Android` `Service` ，这里有一张信息[图表][4]，上面展示了工作原理。
  
  它比`AsyncTask` 强多了，不过呢，对于它，我还是发现有些问题，下面有一段使用`Robospice`的常规写法的代码，写在一个Acitivity中。你没有必要仔细阅读具体内容，我只是引用说明一下观点。
  
```
  FollowersRequest request = new FollowersRequest(user);  
  lastRequestCacheKey = request.createCacheKey();  
  spiceManager.execute(request, lastRequestCacheKey,  
    DurationInMillis.ONE_MINUTE, 
    new RequestListener<FollowerList> {
      @Override
      public void onRequestSuccess(FollowerList listFollowers) {
        // On failure
      }

      @Override
      public void onRequestFailure(SpiceException e) {
          // On success
      }
    });
```
`Request`请求方式如下：

```
public class FollowersRequest extends SpringAndroidSpiceRequest<FollowerList> {  
  private String user;

  public FollowersRequest(String user) {
    super(FollowerList.class);
    this.user = user;
  }

  @Override
  public FollowerList loadDataFromNetwork() throws Exception {
    String url = format("https://api.github.com/users/%s/followers", user);
    return getRestTemplate().getForObject(url, FollowerList.class);
  }

  public String createCacheKey() {
      return "followers." + user;
  }
}
```
# 问题点（上面实现代码有什么问题呢）：
1. 这段代码看起来挺恐怖的，**每一个** `request` 请求，你都得这么写（太麻烦了）。
2. 你还得为每一个`request`请求，创建一个`SpiceRequest`内部类。
3. 你还得为每一个`request`，创建一个`RequestListener` 
4. 如果，缓存有效期太短，每次请求，用户就要等待很久。
5. 如果，缓存有效期太长，用户每次看到数据都是过期的数据。
6. `RequestListener` 持有一个`activity`的引用，会不会引起内存泄漏呢？

所以说，没有那么完美的。


#5招搞定 ，简洁，稳定 (Concise and robust in five steps)

在我搞[Candyshop][5]的时候，我尝试了一些别的，我把一些不同类库中不错的特性，综合起来，并且，尽可能的保持简单稳定。

- [AndroidAnnotations][6] 负责[@Background][7]注解(采用**注解**方式，标识为**后台线程**) ; [@EBean][8] (标识为**模型类**)等等...
- [Spring RestTemplate][9] 负责 REST[^REST] 网络请求，可以跟`AndroidAnnotations` 很好的集成一起。
- [SnappyDB][10] 负责 `Java`对象快速磁盘缓存。
- [EventBus][11] 负责事件驱动。

下面是整个大体结构，接下来会详细解释。

![global schema][12]


步骤一：简单容易的使用缓存系统（Step 1 — An easy to use cache system）
-------------------------------------------------------------

  你需要一个持久化的缓存机制，简单就好。
  
```
      @EBean
    public class Cache {  
        public static enum CacheKey { USER, CONTACTS, ... }
    
        public <T> T get(CacheKey key, Class<T> returnType) { ... }
        public void put(CacheKey key, Object value) { ... }
    }
```

步骤2：一个REST客户端（Step 2 — A REST client）
-----------------------------------------------
我给的这个，仅做一个示例，只要确保你的 `REST API` 的逻辑代码，编写在一处地方（保证单一职责），就好了。

```
    @Rest(rootUrl = "http://anything.com")
public interface CandyshopApi {

    @Get("/api/contacts/")
    ContactsWrapper fetchContacts();

    @Get("/api/user/")
    User fetchUser();

}
```

步骤3：一个贯穿整个应用的事件驱动（Step 3 — An application-wide event bus）
---------------------------------------------------------------------
 在合理的地方，创建一个实例，保证在App的任何地方都可以访问，在`Application` 中创建，就是一个不错的选择。
 
```
    public class CandyshopApplication extends Application {  
    public final static EventBus BUS = new EventBus();
    ...
}
```
步骤4：一个需要加载数据的Activity（Step 4 — An Activity which needs some data!）
------------------------------------------------------------------------
我的实现方式是，模拟`Robospice`基于一个`service`，不是Android自带的（经过定制的），一个常规的单列模式，整个应用都可以访问。在**步骤5** 中将展示service 的代码，现在，让我们先看看`Activity`中的代码如何书写，
因为，这里，是第一个地方，我希望保持最简单的。
 
```
@EActivity(R.layout.activity_main)
public class MainActivity extends Activity {

    // Inject the service
    @Bean protected AppService appService;

    // Once everything is loaded…
    @AfterViews public void afterViews() {
        // … request the user and his contacts (returns immediately)
        appService.getUser();
        appService.getContacts();
    }

    /*
        The result of the previous calls will
        come as events through the EventBus.
        We'll probably update the UI, so we
        need to use @UiThread.
    */

    @UiThread public void onEvent(UserFetchedEvent e) {
        ...
    }

    @UiThread public void onEvent(ContactsFetchedEvent e) {
        ...
    }

    // Register the activity in the event bus when it starts
    @Override protected void onStart() {
        super.onStart();
        BUS.register(this);
    }

    // Unregister it when it stops
    @Override protected void onStop() {
        super.onStop();
        BUS.unregister(this);
    }

}
```
一条线去request请求用户，一条线传递请求返回的响应，同样的事情关联一起，听上去不错的哦。

步骤5：一个单列的service （Step 5 — A singleton service）
--------------------------------------------------------

 正如我在 **步骤4** 中提到，我使用的`service` 不是Android自带的`service`，事实上，我自己定制了一个，但是我改变了想法，理由是 **极简** ，当没有显示`Activity`的时候，或者，跟其他应用做数据交互的时候，`Services`会被用来处理各种任务，那些都不是我需要的。使用一个单列模式，可以使我们避免跟`ServiceConnection,` `Binder`, 等等...打交道的。
 
  这里有很多东西要说明，首先，先让我们看一个大体结构，瞧瞧，当我在`Activity` 调用`getUser()` 和 `getContacts()`的是，都做哪些处理。接着，我会解释代码意思。
  
  你可以假设**每一串** （就是下图中的`serial`标识的地方）都是一个线程。
 
![enter image description here][13]

你这里所看到的呢，就是在这个模式中，我真正喜欢的地方；view视图直接用缓存数据填充，这样呢，用户就不需要等待了，接着，当请求的最新数据返回了时候，在更换界面上的数据，还有一点，你需要保证你的Activity 能接接收到同样类型的数据，当有多次请求的时候，在创建Activity的时候，要记住这一点，就好了。

好啦，我们看一下代码片段。

```
// As I said, a simple class, with a singleton scope
@EBean(scope = EBean.Scope.Singleton)
public class AppService {

    // (Explained later)
    public static final String NETWORK = "NETWORK";
    public static final String CACHE = "CACHE";

    // Inject the cache (step 1)
    @Bean protected Cache cache;

    // Inject the rest client (step 2)
    @RestService protected CandyshopApi candyshopApi;

    // This is what the activity calls, it's public
    @Background(serial = CACHE)
    public void getContacts() {

        // Try to load the existing cache
        ContactsFetchedEvent cachedResult =
            cache.get(KEY_CONTACTS, ContactsFetchedEvent.class);

        // If there's something in cache, send the event
        if (cachedResult != null) BUS.post(cachedResult);

        // Then load from server, asynchronously
        getContactsAsync();
    }

    @Background(serial = NETWORK)
    private void getContactsAsync() {

        // Fetch the contacts (network access)
        ContactsWrapper contacts = candyshopApi.fetchContacts();

        // Create the resulting event
        ContactsFetchedEvent event = new ContactsFetchedEvent(contacts);

        // Store the event in cache (replace existing if any)
        cache.put(KEY_CONTACTS, event);

        // Post the event
        BUS.post(event);

    }

}
```
**单个request请求就有许多代码**，不过呢，我把它拆分了，使它看起来清晰，经常会出现类似模式的代码，可以简单，添加一个帮助类（**helpers**） ，抽成一个方法，直接在需要的调用。例如：`getUser()` 可以这样写：

```
    @Background(serial = CACHE)
    public void getUser() {
        postIfPresent(KEY_USER, UserFetchedEvent.class);
        getUserAsync();
    }

    @Background(serial = NETWORK)
    private void getUserAsync() {
        cacheThenPost(KEY_USER, new UserFetchedEvent(candyshopApi.fetchUser()));
    }
```
那`serial` （见图中）那一串做了哪些事情呢？下面是文档中描述的：
> 默认情况下，所有标有 `@Background`注解的的方法，都会并行运行，2个方法使用同一个系列`serial` 被保证运行在同一个线程中，有秩序的。（一个接一个的运行）

网络请求也一个接一个的跑的话，可能会导致冲突，但是，却更容易处理像`GET-after-POST`（先提交后获取）这种类型任务，这样做呢，意味着，得牺牲一点点协调操作方面的东西了。另外，其实你可以很容易的通过协调serials 的顺序，来提高整体的协调，根据你的情况。目前，在我的**Candyshop** 应用中，我使用了4个不同的系列（`serials`）。

来总结一下
----------
上面我所讲的只是一个草稿，只是一个基本的概念，几个月前弄的，现在，我能够解决各种情形中遇到的麻烦。我很享受我的工作，到目前。还有一些很赞的东西，关于这个框架模式的，我想分享给大家。比如说，错误管理，异常处理，POST 请求，终止无用的操作，但是 我真的很感谢你阅读这里，我不会发表上来的（作者好贱哦，小爷翻译了这么累）

**怎么样？你有没有找到你想要的设计呀？或者，你的日常工作，有没有一个很享受的事情啊？** （你妹.....）
  
    Joan Zapata
    Android Lead Developer at Candyshop
    Paris, Francehttps://twitter.com/JoanZap
  
  
  
  
  
  
  
  
  
  
  
  
  
  [^REST]: REST ：REST软件架构是由Roy Thomas Fielding博士在2000年首次提出的。他为我们描绘了开发基于互联网的网络软件的蓝图。REST软件架构是一个抽象的概念，是一种为了实现这一互联网的超媒体分布式系统的行动指南。利用任何的技术都可以实现这种理念。具体内容百度百科
  


  [1]: http://developer.android.com/reference/android/os/AsyncTask.html
  [2]: http://developer.android.com/guide/components/loaders.html
  [3]: https://github.com/stephanenicolas/robospice
  [4]: https://raw.github.com/stephanenicolas/robospice/master/gfx/RoboSpice-InfoGraphics.png
  [5]: https://play.google.com/store/apps/details?id=com.candyshop
  [6]: https://github.com/excilys/androidannotations
  [7]: https://github.com/excilys/androidannotations/wiki/WorkingWithThreads
  [8]: https://github.com/excilys/androidannotations/wiki/Enhance-custom-classes
  [9]: http://projects.spring.io/spring-android/
  [10]: https://github.com/nhachicha/SnappyDB
  [11]: https://github.com/greenrobot/EventBus
  [12]: http://joanzap.ghost.io/content/images/2014/Apr/article1_global_schema--2-.png
  [13]: http://joanzap.ghost.io/content/images/2014/Apr/article1_serials--3-.png
