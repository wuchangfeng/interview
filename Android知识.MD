



#### Android 相关

* InstantRun 原理

  * hotSwap 最快的，应用不需要重启或者退出，甚至连 activtiy 都不需要重启
  * wramSwap 重启 activity，可以看到屏幕会很快的重新闪动一下
  * coldSwap 重启应用 这种是最慢的。cold swap相对而言就要更慢一些了，Android Studio会自动记录我们项目的每次修改，然后将修改的这部分内容打成一个dex文件发送到手机上，尽管这种swap类型仍然不需要去安装一个全新的APK，但是为了加载这个新的dex文件，整个应用程序必须进行重启才行。另外，cold swap的工作原理是基于multidex机制来实现的，在不引入外部library的情况下，只有5.0及以上的设备才支持multidex，因此，如果你使用了5.0以下的设备，那么cold swap就无法工作了，这种情况会执行最原始的完整APK安装过程。


* 说说Service的生命周期？
  
  启动Service的方式有两种，各自的生命周期也有所不同。
  * 通过startService启动Service：onCreate、onStartCommand、onDestory。
  * 通过bindService绑定Service：onCreate、onBind、onUnbind、onDestory。


* listview的卡顿分析和优化

  用好 View Type，例如你的 ListView 中有几个类型的 Item，需要给每个类型创建不同的 View，这样有利于 ListView 的回收，当然类型不能太多；

  尽量让 ItemView 的 Layout 层次结构简单，这是所有 Layout 都必须遵循的；

   善用自定义 View，自定义 View 可以有效的减小 Layout 的层级，而且对绘制过程可以很好的控制；

  尽量能保证 Adapter 的 hasStableIds() 返回 true，这样在 notifyDataSetChanged() 的时候，如果 id 不变，ListView 将不会重新绘制这个 View，达到优化的目的；

  每个 Item 不能太高，特别是不要超过屏幕的高度，可以参考 Facebook 的优化方法，把特别复杂的 Item 分解成若干小的 Item，特别推荐看一下这个文章：[https://code.facebook.com/posts/879498888759525/fast-rendering-news-feed-on-android/**](//link.zhihu.com/?target=https%3A//code.facebook.com/posts/879498888759525/fast-rendering-news-feed-on-android/)

  为了保证 ListView 滑动的流畅性，getView() 中要做尽量少的事情，不要有耗时的操作。特别是滑动的时候不要加载图片，停下来再加载，这个库可以帮助你 Glide：[https://github.com/bumptech/glide**](//link.zhihu.com/?target=https%3A//github.com/bumptech/glide)

  使用 RecycleView 代替。 ListView 每次更新数据都要 notifyDataSetChanged()，有些太暴力了。RecycleView 在性能和可定制性上都有很大的改善，推荐使用

* 四大组件

  * Activity
  * 广播
  * 服务 其实服务是运行在主线程的，不要与线程搞混淆
  * contentprovider

* ThreadLocal 

  并不是一个 thread ，**而是一个局部变量**。当使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。从线程的角度看，目标变量就象是线程的本地变量，这也是类名中“Local”所要表达的意思。

* 在Android开发中Serializable和Parcelable的用法

  * Parcelable是Android 框架提供给我使用的，Google提供了比较好的接口和文档支持，例如上面的putExtra，就有对Parcelable数组的重载方法。
  *  Parcelable效率更高，Parcelable底层实现是内存的copy，速度很快，Serializable是IO操作，而且会用到反射，相对比较慢，国外有人测试过，Parcelable比Serializable从序列化到传输到反序列化，平均要快10倍左右。
  *  最后一个原因也是最重要的原因，Parcelable要序列化哪些字段，我们完全可以控制，而且还可以在其中加入各种转换，修饰，因为写接口暴露给我们了，我们可以自由定制，而Serializable就显的比较笨拙，而且需要一些额外的字节来存储类的信息，当然Serializable使用起来要更简单。



* Activity 和 Fragment 的生命周期

  - Activity——onCreate->onStart->onResume->onPause->onStop->onDestroy
  - Fragment——onAttach->onCreate->onCreateView->onActivityCreated->onStart->onResume->onPause->onStop->onDestroyView->onDestroy->onDetach

- 经典的一个`Activity A`启动一个`Activity B`，他们的生命周期

  * BACK键

  当我们按BACK键时，我们这个应用程序将结束，这时候我们将先后调用onPause()->onStop()->onDestory()三个方法。再次启动App时，会执行onCreate()->onStart()->onResume()

  * HOME键

  当我们打开应用程序时，比如浏览器，我正在浏览NBA新闻，看到一半时，我突然想听歌，这时候我们会选择按HOME键，然后去打开音乐应用程序，而当我们按HOME的时候，Activity先后执行了onPause()->onStop()这两个方法，这时候应用程序并没有销毁。不可见时候，都执行了 onStop() 这个方法。

  而当我们从桌面再次启动应用程序时，则先后分别执行了**onRestart()**->onStart()->onResume()三个方法。

  补充一点，当前Activity产生事件弹出Toast和AlertDialog的时候Activity的生命周期不会有改变

  Activity运行时按下HOME键(跟被完全覆盖是一样的)：onSaveInstanceState --> onPause --> onStop，再次进入激活状态时： onRestart -->onStart--->onResume

- startActivityForResult是哪个类的方法

  1,startActivity(Intent intent);启动其他Activity

  2,startActivityForResult(Intent intent，int requestCode)：以指定指定的请求码（requestCode）启动Activity，并且程序将会等到新启动Activity的结果(通过重写onActivityResult()方法来获取)

- 启动模式，各自的特点，

  众所周知当我们多次启动同一个Activity时，系统会创建多个实例，并把它们按照先进后出的原则一一放入任务栈中，当我们按back键时，就会有一个activity从任务栈顶移除，重复下去，直到任务栈为空，系统就会回收这个任务栈。但是这样以来，系统多次启动同一个Activity时就会重复创建多个实例，这种做法显然不合理，为了能够优化这个问题，[Android](http://lib.csdn.net/base/android)提供四种启动模式来修改系统这一默认行为。 进入正题，Activity的四种启动模式如下： 

  * standard：标准模式，这是默认的启动模式，每次都会创建新的实例
  * singleTop：栈顶复用模式，如果已经有一个 activity 的实例在任务栈的，并且还在栈顶，就只节用。
  * singleTask：栈内复用模式。如果已经有一个该 activity 的实例在任务栈中，那么久**销毁**它上面的所有的activity，使其成为栈顶，当具有一个 singletask 启动模式的 activity 请求启动之后，系统会首先寻找是否存在 activity 想要得任务栈，**如果不存在，就重新创建一个任务栈**，然后创建 activity 的实例将其放进去。**如果存在 activity 所需要的任务栈**，这个时候就看栈中是否存在 activity 实例，如果有，那么系统就会把该 activity 实例调到栈顶，并调用其 onNewIntent 方法(它之上的Activity会被迫出栈，所以**singleTask模式具有FLAG_ACTIVITY_CLEAR_TOP效果**)，如果不存在，那么就创建 activity 实例并把它放进栈中。
  * **singleInstance**：单实例模式 该Activity会在一个独立的任务栈中启动，并且这个任务栈中有且只有这一个实例。当再次启动该Activity的时候，会重用已存在的任务栈和实例。

- Activity 任务栈

  taskAffinity 参数，任务相关性，**表示一个 activity 所需要的任务栈的名字**。默认情况下，所有的 activity 所需要的任务栈的名字都是应用的包名。任务栈分为前后台任务栈，后台任务栈中的 activity 处于暂停状态，用户可以再次切换其至前台。askAffinity属性主要和singleTask启动模式和allowTaskRepeating属性配对使用，在其他情况下使用没有意义。

- res/raw 和 assets 之间的区别

  这两个目录下的文件都会被打包进APK，并且不经过任何的压缩处理。
  assets与res/raw不同点在于，assets支持任意深度的子目录，**这些文件不会生成任何资源ID**，只能使用AssetManager按相对的路径读取文件。如需访问原始文件名和文件层次结构，则可以考虑将某些资源保存在assets目录下。

- android中Invalidate和postInvalidate的区别

  Android中实现view的更新有两组方法，一组是invalidate，另一组是postInvalidate，其中前者是在UI线程自身中使用，而后者在非UI线程中使用。 
  Android提供了Invalidate方法实现界面刷新，但是Invalidate不能直接在线程中调用，因为他是违背了单线程模型：Android UI操作并不是线程安全的，并且这些操作必须在UI线程中调用。 
  　　Android程序中可以使用的界面刷新方法有两种，分别是利用Handler和利用postInvalidate()来实现在线程中刷新界面。 

- Activity 标记位

  * FLAG_ACTIVITY_NEW_TASK

  * FLAG_ACTIVITY_SINGLE_TOP

  * FLAG_aCTIVITY_CLEAR_top

  `FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS`：具有这个标记的Activity不会出现在历史Activity列表中，当某些情况下我们不希望用户通过历史列表回到我们的Activity的时候这个标记比较有用，它等同于属性设置`android:excludeFromRecents="true"`。

  从非Activity类型的Context(例如ApplicationContext、Service等)中以`standard`模式启动新的Activity是不行的，因为这类context并没有任务栈，所以需要为待启动Activity指定`FLAG_ACTIVITY_NEW_TASK`标志位。

  ``` java
  Intent demoIntent = new Intent(this, DemoActivity.class);
  demoIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
  startActivity(demoIntent);
  ```


- Handler 为什么可能造成内存泄露？（message 引用）

  当 Android 应用程序启动时，framework 会为该应用程序的主线程创建一个 Looper 对象。Looper 对象包含一个简单的消息队列 Message Queue，并且能够循环的处理队列中的消息。这些消息包括大多数应用程序 framework 事件，例如 Activity 生命周期方法调用、button 点击等，这些消息都会被添加到消息队列中并被逐个处理。主线程的 Looper 对象会伴随该**应用程序的整个生命周期**。

  当我们在主线程中实例化一个 Handler 对象后，会自动与主线程 **Looper 的消息队列关联起来**。所有发送到消息队列的消息 Message 都会拥有一个对 Handler 的引用，而此时当前 Activity 如果已经结束/销毁，而 Handler 由于是非静态内部类就会持有外部类的对象，抓住当前 Activity 对象不放，此时就极有可能导致内存泄漏。

- Handler 的原理

  为了不让主线程执行一些耗时的操作，利用 handler 通信机制解决线程之间的通信问题。Android 基本的 Handler 机制就是背后有一个 looper 、messagequenue以及 message。创建一个与当前线程相关的 looper 对象，去 mq 中取得 msg 对象，让 msg 的发送者去处理它。


- ListView 优化

  * 从文件系统中加载图片也没有内存中加载那么快，甚至可能内存中加载也不够快。因此在ListView中应设立busy标志位，当ListView滚动时busy设为true，停止各个view的图片加载。否则可能会让UI不够流畅用户体验度降低。
  * 文件加载图片放在子线程实现，否则快速滑动屏幕会卡
  * 开启网络访问等耗时操作需要开启新线程，应使用线程池避免资源浪费，最起码也要用AsyncTask。
  * Bitmap从网络下载下来最好先放到文件系统中缓存。这样一是方便下一次加载根据本地uri直接找到，二是如果Bitmap过大，从本地缓存可以方便的使用Option.inSampleSize配合Bitmap.decodeFile(ui, options)或Bitmap.createScaledBitmap来进行内存压缩

- android:layout_gravity 和 android:gravity 的区别

  从名字上可以看到，android:gravity是对元素本身说的，元素本身的文本显示在什么地方靠着换个属性设置，不过不设置默认是在左侧的。

  android:layout_gravity是相对与它的父元素说的，说明元素显示在父元素的什么位置。

  比如说button： android:layout_gravity 表示按钮在界面上的位置。 android:gravity表示button上的字在button上的位置。


- AsyncTask 的实现原理

  asynctask 封装了 线程池和 handler，方便在子线程中更新 ui。Asynctask 排队执行的过程：系统先把参数 Params 封装成 FutureTask 对象，它相当于 Runnable，接着 FutureTask 交给 SerialExcutor 的 execute 方法，将 FutureTask 插入到任务队列 tasks 中，如果这时候没有正在活动的 AsyncTask 任务，那么久会执行下一个 at 任务，同时当一个 at 任务执行完毕之后，at 会继续执行其他任务知道所有的任务都执行为止。，在doInBackground()方法中调用publishProgress()方法才可以从子线程切换到UI线程，从而完成对UI元素的更新操作。其实也没有什么神秘的，因为说到底，AsyncTask也是使用的异步消息处理机制，只是做了非常好的封装而已。

  * execute 用来执行一个异步任务
  * onPreExectute 就是调用后立即执行
  * doinbackgroud 就是用来执行耗时操作
  * onProgressUpdate 用来将进度信息更新到 UI 上

- Service 是运行在哪个线程？IntentService 有什么特点？

  service 不是一个单独的线程，只是长期运行在后台的一个应用组件。不要把耗时操作放在里面，很容易引起 anr，耗时操作单独开一个线程。

  intentService 是继承与 service 并异步处理的请求的一个类，在 intentservice 中有一个工作线程来处理耗时操作，启动 intentservice 方式和传统的 service 方式一样。同时，当任务执行完后，IntentService 会自动停止，而不需要我们去手动控制。另外，可以启动 IntentService 多次，而每一个耗时操作会以工作队列的方式在IntentService 的 onHandleIntent 回调方法中执行，并且，每次只会执行一个工作线程，执行完第一个再执行第二个，以此类推。而且，所有请求都在一个单线程中，不会阻塞应用程序的主线程（UI Thread），同一时间只处理一个请求。 那么，用 IntentService 有什么好处呢？首先，我们省去了在 Service 中手动开线程的麻烦，第二，当操作完成时，我们不用手动停止 Service。

  IntentService 是直接继承与 Service的，继承Service后 它的代码一共就100多行。内部在 onCreate()时，新建了一个HandlerThread 实例。
  （HandlerThread 是一个Thread的 子类，HandlerThread 内部 有点线我们的UI线程，内部一个Looper loop循环一直轮询消息 获得消息 处理消息。）而IntentService, 内部有一个Handler子类 ServiceHandler，它的Looper用的是这个HandlerThread 的Looper,IntentSerivce 在onStart()通过发送Message,ServiceHandler在处理Message 调用的是 onHandleIntent。 所以简单的说一个IntentService,内部就创建了一个线程，通过Android提供的 Handler Message Looper,这些消息处理的类 构成了一个消息处理的模型。所以IntentService 的onHandleIntent 这个方法其实是在IntentService 中开辟的一个子线程中处理的。**

- 广播分为几类？

  安卓广播分为两类:

  1.**普通广播**， broadcast,广播发出之后所有满足条件的应用都能获取到广播里面的数据，缺点是应用获取广播中的数据修改之后不能传递给其它接收广播的应用；
  2.**有序广播**，orderbroadcast,广播发出之后各应用根据应用的优先级依次接收广播，优先级高的应用接收广播之后修改的数据也可以传递给后来的接受者，优先级高的应用也可以调用abrotbroascast方法停止该广播的向下传播，优先级靠应用的android:prioioty属性控制，该值的取值区间为-1000到1000，值越大优先级越高。

- Intent 的匹配规则？action，category，data

  只有action、data、category三方都匹配，Intent才算是匹配成功，进而才能打开相应的Component。一个Component若声明了多个Intent Filter，只需要匹配任意一个即可启动该组件。


- 触摸事件冲突的处理

  ![](http://ww1.sinaimg.cn/large/006dXScfgy1fcmsif4q6tj30oa0gotbb)

  - 对于 dispatchTouchEvent，onTouchEvent，return true是终结事件传递。return false 是回溯到父View的onTouchEvent方法。
  - ViewGroup **想把自己分发给自己的onTouchEvent，需**要拦截器onInterceptTouchEvent方法return true 把事件拦截下来。
  - ViewGroup 的拦截器onInterceptTouchEvent 默认是不拦截的，所以return super.onInterceptTouchEvent()=return false；
  - View 没有拦截器，为了让View可以把事件分发给自己的onTouchEvent**，View的dispatchTouchEvent默认实现（super）就是把事件分发给自己的onTouchEvent。**

- okHttp 和 HttpClient 比较有什么优点？

  volley是一个**简单的异步http库**，仅此而已。**缺点是不支持同步，这点会限制开发模式**；**不能post大数据，**所以不适合用来上传文件。android-async-http。与volley一样是异步网络库，但volley是封装的httpUrlConnection，它是封装的httpClient，而android平台不推荐用HttpClient了，所以这个库已经**不适合android平台**了。
  okhttp是**高性能**的http库，支持同步、异步，而且实现了spdy、http2、websocket协议，api很简洁易用，和volley一样实现了http协议的缓存。picasso就是利用okhttp的缓存机制实现其文件缓存，实现的很优雅，很**正确，**反例就是UIL（universal image loader），自己做的文件缓存，而且不遵守http缓存机制。

  **Volley 也有缺陷，比如不支持 post 大数据**，所以不适合上传文件。不过 Volley 设计的初衷本身也就是为频繁的、数据量小的网络请求而生！

  **毫无疑问 Volley 的优势在于封装的更好**，而使用 OkHttp 你需要有足够的能力再进行一次封装。而 OkHttp 的优势在于性能更高，因为 OkHttp 基于 NIO 和 Okio ，所以性能上要比 Volley更快。    

  估计有些读者不理解 IO 和 NIO 的概念，这里姑且简单提下，这两个都是 Java 中的概念，如果我从硬盘读取数据，第一种方式就是程序一直等，数据读完后才能继续操作，这种是最简单的也叫阻塞式 IO，还有一种就是你读你的，我程序接着往下执行，等数据处理完你再来通知我，然后再处理回调。而第二种就是 NIO 的方式，非阻塞式。    

  所以 NIO 当然要比 IO 的性能要好了， 而 Okio 是 Square 公司基于 IO 和 NIO 基础上做的一个更简单、高效处理数据流的一个库。    

  理论上如果 Volley 和 OkHttp 对比的话，我更倾向于使用 Volley，因为 Volley 内部同样支持使用 OkHttp ，这点 OkHttp 的性能优势就没了，而且 Volley 本身封装的也更易用，扩展性更好些。

  httpclient 已经废弃，而 asynv-http 就是基于他的，所以原作者放弃维护。


- 描述一下 MVC,MVP,MVVM各自的模式及优缺点

  **MVC和MVP的关系**
  我们都知道MVP是从经典的模式MVC演变而来，它们的基本思想有相通的地方：Controller/Presenter负责逻辑的处理，Model提供数 据，View负责显示。作为一种新的模式，MVP与MVC有着一个重大的区别：在MVP中View并不直接使用Model，它们之间的通信是通过 Presenter (MVC中的Controller)来进行的，所有的交互都发生在Presenter内部，而在MVC中View会直接从Model中读取数据而不是通过 Controller。

  **MVVM和MVP的关系**
  而 MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。 唯一的区别是，它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。这样开发者就不用处理接收事件和View更新的工作，框架已经帮你做好了。

  **总结解释一下就是说：****从MVC到MVP的一个转变，就是减少了Activity的职责，减轻了它的负担，简化了Activity中的代码和一些操作，将逻辑代码提取到了Presenter中进行处理，降低了其耦合度。**

  因此我们可以发现**MVP的优点**如下：

  1、模型与视图完全分离，我们可以修改视图而不影响模型；

  2、可以更高效地使用模型，因为所有的交互都发生在一个地方——Presenter内部；

  3、我们可以将一个Presenter用于多个视图，而不需要改变Presenter的逻辑。这个特性非常的有用，因为视图的变化总是比模型的变化频繁；

  4、如果我们把逻辑放在Presenter中，那么我们就可以脱离用户接口来测试这些逻辑（单元测试）。

- Android 中的软引用及弱引用有什么特点？http://www.jianshu.com/p/8488079a939b

  **如果一个对象只具有软引用，那么如果内存空间足够，垃圾回收器就不会回收**它；如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收，Java虚拟机就会把这个软引用加入到与之关联的引用队列中。

  如果一个对象只具有弱引用，那么在垃圾回收器线程扫描的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象。弱引用也可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java虚拟机就会把这个弱引用加入到与之关联的引用队列中。
  弱引用与软引用的根本区别在于：**只具有弱引用的对象拥有更短暂的生命周期，可能随时被回收。而只具有软引用的对象只有当内存不够的时候才被回收，在内存足够的时候，通常不被回收。**

- Android 中常见的内存泄露【核心就是该放手时候放手】

  * Handler 引起的内存泄漏
  * 非静态内部类持有外部类的引用
  * 单利模式导致
  * 线程造成

- 性能优化

  * 布局优化
  * 过度绘制优化
  * 内存泄漏优化
  * 响应速度优化
  * Bitmap 以及线程优化





- Httpclient 与 httpurlclient 之间的区别

  Http Client适用于web浏览器，拥有大量灵活的API，实现起来比较稳定，且其功能比较丰富，提供了很多工具，封装了http的请求头，参数，内容体，响应，还有一些高级功能，代理、COOKIE、鉴权、压缩、连接池的处理。正是因为如此，API 比较多难以修改，android 在 6.0 中抛弃了它，转而用 okhttp

  HttpURLConnection对于大部分功能都进行了包装，Http Client的高级功能代码会较复杂，另外，HttpURLConnection在Android 2.3中增加了一些Https方面的改进(包括Http Client，两者都支持https)。且在Android 4.0中增加了response cache。

  - **功能用法对比**
    - 从功能上对比，HttpClient库要丰富很多，提供了很多工具，封装了http的请求头，参数，内容体，响应，还有一些高级功能，代理、COOKIE、鉴权、压缩、连接池的处理。
    - HttpClient高级功能代码写起来比较复杂，对开发人员的要求会高一些，而HttpURLConnection对大部分工作进行了包装，屏蔽了不需要的细节，适合开发人员直接调用。
    - 另外，HttpURLConnection在2.3版本增加了一些HTTPS方面的改进，4.0版本增加一些响应的缓存。
  - **性能对比**
    - HttpUrlConnection直接支持GZIP压缩；HttpClient也支持，但要自己写代码处理。
    - HttpUrlConnection直接支持系统级连接池，即打开的连接不会直接关闭，在一段时间内所有程序可共用；HttpClient当然也能做到，但毕竟不如官方直接系统底层支持好。
    - HttpUrlConnection直接在系统层面做了缓存策略处理（4.0版本以上），加快了重复请求的速度。
    - [这篇文章](http://wj98127.iteye.com/blog/617014)对两者的速度做了一个对比，做法是两个类都使用默认的方法去请求百度的网页内容，测试结果是使用httpurlconnection耗时47ms，使用httpclient耗时641ms。httpURLConnection在速度有比较明显的优势，当然这跟压缩内容和缓存都有直接关系。

- APK 瘦身

  - 移除冗余的代码 用 proguard 来检查
  - 移除无用的资源 用 lint 工具
  - 对资源文件进行取舍
  - 优化压缩图片

- Android 横竖屏切换

  onSaveInstanceState-->onPause-->onStop-->onDestroy-->onCreate-->onStart-->onRestoreInstanceState-->onResume-->

  1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期，切横屏时会执行一次，切竖屏时会执行两次

  2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次

  3、设置Activity的android:configChanges="orientation|keyboardHidden"时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法



- ontouchevent 和 ontouch 有什么区别

  onTouchListener的onTouch方法优先级比onTouchEvent高，会先触发。

  假如onTouch方法返回false会接着触发onTouchEvent，反之onTouchEvent方法不会被调用。

  内置诸如click事件的实现等等都基于onTouchEvent，假如onTouch返回true，这些事件将不会被触发。

  * OnTouchEvent()方法：

        onTouchEvent是手机屏幕事件的处理方法，是获取的对屏幕的各种操作，比如向左向右滑动，点击返回按钮等等。属于一个宏观的屏幕触摸监控。onTouchEvent方法是override 的Activity的方法。重新了Activity的onTouchEvent方法后，当屏幕有touch事件时，此方法就会别调用。

  * OnTouch方法

        onTouch()是OnTouchListener接口的方法，它是获取某一个控件的触摸事件，因此使用时，必须使用setOnTouchListener绑定到控件，然后才能鉴定该控件的触摸事件。当一个View绑定了OnTouchLister后，当有touch事件触发时，就会调用onTouch方法。通过getAction()方法可以获取当前触摸事件的状态：

  ACTION_DOWN：表示按下了屏幕的状态。
  ACTION_MOVE ：表示为移动手势
  ACTION_UP      ：表示为离开屏幕
  ACTION_CANCEL ：表示取消手势，不会由用户产生，而是由程序产生的

- Android 中如何避免 oom

  - 使用更加轻量级的数据结构 ArrayMap/SparseArray 来替代传统的 HashMap
  - 避免在 Android 中使用枚举,以及减少 Bitmap 所占的内存
  - 缩放图片比例以及尽量不要去加载大图
  - 在ListView和GridView等显示大量图片的控件里面需要使用LRU机制缓存Bitmap.




-    什么是三级缓存

     - 网络缓存, 不优先加载, 速度慢,浪费流量
     - 本地缓存, 次优先加载, 速度快
     - 内存缓存, 优先加载, 速度最快

-    Android 中的 IPC 机制

     - 使用文件
     - 使用 messager
     - 使用 socket
     - 使用 contentprovider
     - 使用 bundle
     - 使用 AIDL

-    AIDL 机制，其实就是 android 中的 ipc 机制

              所谓AIDL：Android Interface Definition Language，是一种Android接口定义语言，用于编写Android进程间通信代码。也就是说AIDL只是一个实现进程间通信的一个工具，真正实现Android进程间通信机制的其实是幕后“主谋”Binder机制。所有有关AIDL实现进程间通信都是依赖于Android的Binder机制，那么这个Binder机制到底是个什么东西呢？

              AIDL适用场景

              官方文档特别提醒我们何时使用AIDL是必要的：只有你允许客户端从不同的应用程序为了进程间的通信而去访问你的service，以及想在你的service处理多线程。具体的不同场景下的AIDL设计规则如下：

              如果不需要进行不同应用程序间的并发通信(IPC)，通过实现一个Binder对象来创建接口；或者你想进行IPC，但不需要处理多线程的，则使用Messenger对象来创建接口。无论如何，在使用AIDL前，必须要理解如何绑定service——bindService。

              AIDL开发步骤

              AIDL接口文件，和普通的接口内容没有什么特别，只是它的扩展名为.aidl，保存在src目录下。如果其他应用程序需要IPC，则那些应用程序的src也要带有这个文件。Android SDK tools就会在gen目录自动生成一个IBinder接口文件。service必须适当地实现这个IBinder接口。那么客户端程序就能绑定这个service并在IPC时从IBinder调用方法。每个aidl文件只能定义一个接口，而且只能是接口的声明和方法的声明。

              换言之，AIDL文件的作用即是在任何需要跨进程调用的方法都需要声明在涉及的每一个服务或者进程中。

-    service 生命周期

              Service对象不能自己启动，需要通过某个Activity、Service或者其他Context对象来启动。启动的方法有两种，Context.startService和Context.bindService()。两种方式的生命周期是不同的，具体如下所示。

              Context.startService方式的生命周期： 
              启动时，startService –> onCreate() –> onStart() 
              停止时，stopService –> onDestroy()
            
              Context.bindService方式的生命周期： 
              绑定时,bindService  -> onCreate() –> onBind() 
              解绑定时,unbindService –>onUnbind() –> onDestory()

-    srcoll 工作原理

              Scroller的工作原理：Scroller本身并不能实现view的滑动，它需要配合**view的computeScroll方法**才能完成弹性滑动的效果，它不断地让view重绘，而每一次重绘距滑动起始时间会有一个时间间隔，通过这个时间间隔Scroller就可以得出view的当前的滑动位置，知道了滑动位置就可以通过scrollTo方法来完成view的滑动。就这样，view的每一次重绘都会导致view进行小幅度的滑动，而多次的小幅度滑动就组成了弹性滑动，这就是Scroller的工作原理。

-    view 的滑动冲突的解决

              (1)常见的滑动冲突的场景：

              1.外部滑动方向和内部滑动方向不一致，例如viewpager中包含listview；

              2.外部滑动方向和内部滑动方向一致，例如viewpager的单页中存在可以滑动的bannerview；

              3.上面两种情况的嵌套，例如viewpager的单个页面中包含了bannerview和listview。

              (2)滑动冲突处理规则

              可以根据滑动距离和水平方向形成的夹角；或者根绝水平和竖直方向滑动的距离差；或者两个方向上的速度差等

              (3)解决方式

              1.外部拦截法：点击事件都先经过父容器的拦截处理，如果父容器需要此事件就拦截，如果不需要就不拦截。该方法需要重写父容器的`onInterceptTouchEvent`方法，在内部做相应的拦截即可，其他均不需要做修改。

              2.内部拦截法：父容器不拦截任何事件，所有的事件都传递给子元素，如果子元素需要此事件就直接消耗掉，否则就交给父容器来处理。这种方法和Android中的事件分发机制不一致，需要配合`requestDisallowInterceptTouchEvent`方法才能正常工作。

-    HandlerThread 始终是一个 thread，如果是 thread 的话不能直接创建 handler，但是 handlerThread 不一样，它与一般的 thread 的 run 方法不一样，一般的 run 方法中是执行耗时操作，但是 handlerthread 中的 run 方法中执行的是 looper.prepare() 和 looper.loop() 方法，所以 可以在 handlerthread 中创建 handler。

-    属性动画

              新引入的属性动画机制已经不再是针对于View来设计的了，也不限定于只能实现移动、缩放、旋转和淡入淡出这几种动画操作，同时也不再只是一种视觉上的动画效果了。它实际上是一种不断地对值进行操作的机制，并将值赋值到指定对象的指定属性上，可以是任意对象的任意属性。所以我们仍然可以将一个View进行移动或者缩放，但同时也可以对自定义View中的Point对象进行动画操作了。我们只需要告诉系统动画的运行时长，需要执行哪种类型的动画，以及动画的初始值和结束值，剩下的工作就可以全部交给系统去完成了。

     - valueanimator
     - objectanimator

-    **APK 打包流程**

     * 打包资源文件，生成R.java文件
     * 处理aidl文件，生成相应java 文件
     * 编译工程源代码，生成相应class 文件
     * 转换所有class文件，生成classes.dex文件
     * 打包生成apk
     * 对apk文件进行签名
     * 对签名后的apk文件进行对其处理



-    Butterknife 最新的实现原理，主要是利用 APT 在编译期生成相关代码  https://joyrun.github.io/2016/07/19/AptHelloWorld/

         APT(Annotation Processing Tool)是一种处理注释的工具,它对源代码文件进行检测找出其中的Annotation，使用Annotation进行额外的处理。
              Annotation处理器在处理Annotation时可以根据源文件中的Annotation生成额外的源文件和其它的文件(文件具体内容由Annotation处理器的编写者决定),APT还会编译生成的源文件和原来的源文件，将它们一起生成class文件。

-    otto 与 eventbus 出现的理由以及实现原理

    为了简化并且更加高质量的在Activities, Fragments, Threads, Services等等之间的通信，同时解决组建之间的高耦合还能继续高效的通信。事件总线设计出现了。
              总线，在计算机组成原理中遇到过io总线。总线的思路就是负责传递某种object到指定的地方。
              在Android内置的Intent和BroadcastReceiver就是采用了类似事件总线的设计思路。这两者都可以起到跟事件总线类似的效果。注册广播接收器和单纯发一个intent就可以唤起其他组件，提醒其他组件更新，这是非常方便的，同时也是本文提到的两个开源方案所做不到的。但也有不好地方，它们内部的实现都需要 IPC，单从传递效率上来讲，可能并不太适合上层的组件间通信。

-    ContentProvider 的相关说法

     * ContentProvider的作用 1.封装数据：对数据进行封装，提供统一的接口 2.跨进程的数据共
     * ContentProvider数据访问限制** android:exported 属性用于指示该服务是否能够被其他应用程序组件调用或跟它交互。如果设置为true，则能够被调用或交互。如果设置为false时，只有同一个应用程序的组件或带有相同用户ID的应用程序才能启动或绑定该服务。**对于需要开放的组件应设置合理的权限，如果只需要对同一个签名的其它应用开放content provider，则可以设置signature级别的权限。**
     * ContentProvider运行的进程和线程** ContentProvider可以在AndroidManifest.xml中配置一个叫做android:multiprocess 的属性，默认值是false，表示ContentProvider是单例的，无论哪个客户端应用的访问都将是同一个ContentProvider对象。如果设为true，系统会为每一个访问该ContentProvider的进程创建一个实例。
     * 问：每个ContentProvider的操作是在哪个线程中运行的呢（其实我们关心的是UI线程和工作线程）？比如我们在UI线程调用getContentResolver().query查询数据，而当数据量很大时（或者需要进行较长时间的计算）会不会阻塞UI线程呢？ 答：**ContentProvider和调用者在同一个进程，ContentProvider的方法（query/insert/update/delete等）和调用者在同一线程中；****ContentProvider和调用者在不同的进程，ContentProvider的方法会运行在它自身所在进程的一个Binder线程中。**
     *  问：ContentProvider是如何在不同应用程序之间传输数据的？ 答：**ContentResolver虽然是通过Binder进程间通信机制打通了应用程序之间共享数据的通道，但Content Provider组件在不同应用程序之间传输数据是基于匿名共享内存机制来实现的。**

-    SurfaceView与View的区别

     * View主要适用于主动更新的情况下，而SurfaceView主要适用于被动更新，例如频繁地刷新；
     * View在主线程中对画面进行刷新，而SurfaceView通常会通过一个子线程来进行页面刷新； 
     * View在绘图时没有使用双缓冲机制，而SurfaceView在底层实现机制中就已经实现了双缓冲机制。

-    Service 能不能弹出 Dialog ？

     一般情况下 Dialog 的显示必须要依附于 Window，但是有时候可以申请一些权限，来弹出系统级别的 Dialog，所以是可以实现的

-    WindowManager 的相关知识 

     * WindowManager主要用来管理窗口的一些状态、属性、view增加、删除、更新、窗口顺序、消息收集和处理等。
     * 通过Context.getSystemService(Context.WINDOW_SERVICE)的方式可以获得WindowManager的实例.
     * WindowManager继承自ViewManager，里面涉及到窗口管理的三个重要方法，分别是：

     * addView(); 
     * updateViewLayout();
     * removeView();  

  在WindowManager中还有一个重要的静态类LayoutParams.通过它可以设置和获得当前窗口的一些属性。

  我们先来看看addView()方法，在addView中，会利用LayoutParams获得window的View属性，并为每个window创建ViewRoot，ViewRoot是View和WindowManager之间的桥梁，真正把View传递给WindowManager的是通过ViewRoot的setView()方法，ViewRoot实现了View和WindowManager之间的消息传递。整个Android的窗口机制是基于一个叫做 WindowManager，这个接口可以添加view到屏幕，也可以从屏幕删除view。它面向的对象一端是屏幕，另一端就是View，直接忽略我们以前的Activity或者Dialog之类的东东。其实我们的Activity或者Diolog底层的实现也是通过WindowManager，这个 WindowManager是全局的，整个系统就是这个唯一的东东。它是显示View的最底层了。

  ``` java
   WindowManager mWm = (WindowManager)getSystemService(Context.WINDOW_SERVICE);
          Button view = new Button(this);
          view.setText("window manager test!");
          WindowManager.LayoutParams mParams = new WindowManager.LayoutParams(); 
          mWm.addView(view, mParams);
  ```

  我们这个button的显示和当前的运行环境基本上是无关的，当前是什么activity或者是桌面，使用这个底层的结果给你的编程带来很大的灵活性，但是要注意，显示出来就要销毁掉，这个是必须的，销毁其实就是一个remove。


- 单利模式双重判断是否为空以及加上两种锁的原因

  强烈推荐 http://www.jianshu.com/p/4586cb11c62d

  为什么要两次判断是否为 null？联想第三个线程过来取 instance 实例的时候，instance 还未实例化好。因为 JMM在创建对象时候分这个三部：1：分配对象的内存空间 2：初始化对象 3：设置 instance 指向刚分配的内存地址。第三个步骤和第二个步骤依赖于第一行执行结果，存在数据依赖性，但是第二和第三之间却没有。**但是第2行和第3行却不存在数据依赖性，这里可能会发生重排序！**

  也就是说第3行可能会优先于第2行执行，先设置instance指向内存地址，此时instance就!=null；然后再初始化对象。如果初始化对象耗时较长，在还没有初始化完成时，线程C访问getInstance方法，在第5行会判断到instance!=null，直接返回未初始化完成的instance实例对象。故而我们用 volatile 关键字来解决这个问题，第一个禁止指令重新排序，第二个及时刷新一个变量对于线程的可见性。
  

- 位操作

  ![](http://ww1.sinaimg.cn/large/b10d1ea5ly1fd3pf13bobj20mj04uq2z)

- webview 控件的使用

  https://www.kymjs.com/code/2015/05/03/01/

  ​

- 正则表达式

- 找出链表是否有环，以及环的入口节点

  ![](http://ww1.sinaimg.cn/large/006dXScfly1fd2ent76nmj30t90fntb4)

- 判断两个链表是否相交[注意相交的概念与传统的不一样]

- 找出两个链表相交的第一个节点[注意相交的概念与传统的不一样]

- 找出左右子树的公共节点

  - 是不是二叉搜索树
  - 不是二叉搜索树
  - 树的节点有没有指向父节点的指针
  - 最普通树，解决办法就是每遍历一个节点就判断这个节点是否包含两个子树
  - 用两个链表保存从根节点到目的节点的路径，最后按照链表方式求解

  这个问题可以转化为求两个链表的公共节点，真是机智啊。

- 泛型擦除 http://wiki.jikexueyuan.com/project/java-vm/syntactic-sugar.html

  1.虚拟机中没有泛型，只有普通类和普通方法,所有泛型类的类型参数在编译时都会被擦除,泛型类并没有自己独有的Class类对象。比如并不存在`List`.class或是`List.class`，而只有`List.class`。 2.创建泛型对象时请指明类型，让编译器尽早的做参数检查（**Effective Java，第23条：请不要在新代码中使用原生态类型**） 3.不要忽略编译器的警告信息，那意味着潜在的`ClassCastException`等着你。 4.静态变量是被泛型类的所有实例所共享的。对于声明为`MyClass`的类，访问其中的静态变量的方法仍然是 `MyClass.myStaticVar`。不管是通过`new MyClass`还是`new MyClass`创建的对象，都是共享一个静态变量。 5.泛型的类型参数不能用在`Java`异常处理的`catch`语句中。因为异常处理是由JVM在运行时刻来进行的。由于类型信息被擦除，`JVM`是无法区分两个异常类型`MyException`和`MyException`的。对于`JVM`来说，它们都是 `MyException`类型的。也就无法执行与异常对应的`catch`语句。

  ``` java
  class Apple<T extends Number>{
      public T getSize() {
          return size;
      }

      public void setSize(T size) {
          this.size = size;
      }

      T size;
      public Apple(){}
      public Apple(T size){
          this.size = size;
      	}
  	}
  	public class ErasureTest {

     	 public static void main(String args[]){
          Apple<Integer> a = new Apple<>(6);
          // a的getSize()方法返回 Integer对象
          Integer as = a.getSize();
          // 把a对象赋值给b，丢失尖括号里面的类型信息
          Apple b = a;
          // b只知道size的类型是number
          Number size1 = b.getSize();
          // 下面的代码出现编译错误
          //  Integer size2 = b.getSize();
      	}
  }
  ```

- super 以及 extend 在泛型中的作用

  1) 参数写成：T<? super B>，对于这个泛型，?代表容器里的元素类型，由于只规定了元素必须是B的超类，导致元素没有明确统一的“根”（除了Object这个必然的根），所以这个泛型你其实无法使用它，对吧，除了把元素强制转成Object。所以，对把参数写成这样形态的函数，你函数体内，只能对这个泛型做**插入操作，而无法读**

  2) 参数写成： T<? extends B>，由于指定了B为所有元素的“根”，你任何时候都可以安全的用B来使用容器里的元素，但是插入有问题，由于供奉B为祖先的子树有很多，不同子树并不兼容，由于实参可能来自于任何一颗子树，所以你的插入很可能破坏函数实参，所以，对这种写法的形参，**禁止做插入操作，只做读取**

  实际上，我们采用extends，super来扩展泛型的目的是为了弥补例如List<E>只能存放一种特定类型数据的不足，将其扩展为List<? extends E> 使其可以接收E的子类型中的任何一种类型元素，这样使它的使用范围更广。List<? super E>同理。

- JVM 类加载机制过程

  加载-->验证-->准备-->解析-->初始化-->使用-->卸载

  加载： JVM基于启动类加载器、扩展类加载器、应用程序类加载器通过双亲委派模型进行类的加载，当然也可以继承。。。实现自定义的类加载。

- Set里的元素是不能重复的，那么用什么方法来区分重复与否呢? 是用==还是equals()? 它们有何区别? 

  Set里的元素是不能重复的，**那么用iterator()方法来区分重复与否**。equals()是判读两个Set是否相等。 
  equals()和==方法决定引用值是否指向同一对象equals()在类中被覆盖，为的是当两个分离的对象的内容和类型相配的话，返回真值。 

- Fragment 的生命周期写一下，onAttach 和 onDetach 的作用
  **onAttach（）**：当Activity和Fragment交互的时候，我们可以在Activity中通过Fragment.setArguments()的方法为Fragment提供数据，然后再Fragment的onAttach（）方法中getArguments()获得一个Bundle对象。 
  **onCreateView()**:创建该Fragment对应的视图，在这里你必须将创建的视图返回诶调用者。它跟onCreate（）的区别：onCreate()是指创建该Fragment，你可以在其中初始化除了View之外的东西。 
  **onActivityCreated()** 
  当Activity的onCreate()方法调用时，该方法被调用 
  **onDetach()** 
  当Fragment和Activity解除关联时调用该方法。

- 哪些对象可以作为GC Roots？
  ​    虚拟机栈（栈帧中的本地变量表）中的引用的对象
  ​    方法区中的类静态属性引用的对象
  ​    方法区中的常量引用的对象
  ​    本地方法栈中JNI（Native方法）的引用对象

- 三种GC之间的区别 Minor GC、Major GC 以及 Full GC 之间的区别

  * Minor GC 每次启动时候都会清理年轻代的内存，发生的非常频繁
  * Major GC 清理老年带的垃圾
  * Full GC 是清理整个堆空间包含年轻代和老年代的垃圾

- 内存分配策略

  内存的分配，主要是在堆里面进行分配（也有可能经过JIT编译后被拆散为标量类型并间接地在栈上分配），堆里面主要是发生在新生代的Eden区中，少数情况下是在老年代中，分配的规则不是固定的，这需要根据当前使用的是哪种垃圾收集器组合还有虚拟机与内存相关的参数设置。

  * 对象优先在Eden分配

  大多数情况下，对象在新生代Eden区进行分配。当Eden区没有足够空间进行分配时JVM发生一次Minor GC。什么叫Minor GC呢？Minor GC是指发生在新生代的垃圾收集动作，因为Java对象大多具备朝生夕死的特性，所以Minor GC非常频繁，当然了，其回收速度肯定也是比较快的~，与之对应，还有个Full GC或者称为Major GC,是指老年代中的GC，经常会伴随一次Minor GC，Major GC速度一般会比Minor GC速度慢10倍以上！

  * 大对象直接进入老年代

  所谓的大对象，是指占用大量连续内存空间的Java对象。最经典的大对象就是那种很长的字符串和数组。大对象对于虚拟机来说是个坏消息~我们写程序时，尽量要避免出现一群朝生夕死的大对象。经常出现大对象容易导致内存还有不少空间时就得提前触发垃圾收集以获取足够的空间来存放大对象。

  * 长期存活的对象将进入老年代

  JVM采用分代收集思想来管理内存，就要去区分哪些是年轻的对象，哪些是老年的对象。我们知道，刚创建的对象肯定是年轻的对象，那么怎么将对象判断为老年呢？

  在Eden区出生，并经过一次Minor GC后仍然存活，并且能被To Suvivor容纳，移动到To Suvivor区后，年龄设置为1。以后每经历一次Minor GC就将年龄加1，当它的年龄达到一个阀值（默认15，也可以更改-XX:MaxTenurinigThreshold来设置），就会被晋级到老年代中。

  * 动态对象年龄判定

  为了更好地适应不同程序内存情况，JVM并不一定是等到对象年龄达到阀值才将对象晋级到老年代。**如果在Survivor空间中的相同年龄的所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年的对象就可以直接进入老年代，无需等到指定的阀值**。这句话可能有点绕，不太好理解，我来再解释一下，就是说，假设Survivor的空间大小为max，年龄为y的对象总共有n个，如果y*n>max/2，那么所有年龄大于y的对象全部进入到老年代。

  ​

- Java 虚拟机运行时数据区域

  ![](http://ww1.sinaimg.cn/large/006dXScfly1fd60l9j2ykj30kj09s43m)

- 在浏览器地址栏键入URL，按下回车之后发生的几个事件：

      1）浏览器向DNS服务器请求解析该URL中的域名所对应的IP地址；

      2）解析出IP地址后，根据该IP地址和默认端口80，和服务器建立TCP连接；

      3）浏览器发出读取文件（URL中域名后面部分对应的文件）的HTTP请求，该请求报文作为TCP三次握手的第三个报文的数据发送给服务器；

      4）服务器给出相应，把对应的html文本发送给浏览器；

      5）释放TCP连接；

      6）浏览器将该文本显示出来

- 堆排序思想以及实现过程 http://www.jb51.net/article/86177.htm

  **1.最大堆的算法思想是：**先将初始的R[0…n-1]建立成最大堆，此时是无序堆，而堆顶是最大元素
  再将堆顶R[0]和无序区的最后一个记录R[n-1]交换，由此得到新的无序区R[0…n-2]和有序区R[n-1]，且满足R[0…n-2].keys ≤ R[n-1].key
  由于交换后，前R[0…n-2]可能不满足最大堆的性质，因此再调整前R[0…n-2]为最大堆，直到只有R[0]最后一个元素才调整完成。
  最大堆排序完成后，其实是升序序列，每次调整堆都是要得到最大的一个元素，然后与当前堆的最后一个元素交换，因此最后所得到的序列是升序序列。

- Java 重写与重载之间的区别

  - 重写[Override]是子类对父类允许访问的方法实现过程进行重新编写，返回值和形参都不能变
  - 重载[Overload]是在一个类中，方法名字相同，而形参不同，返回值可以相同，也可以不同。

- TCP 如何保证可靠性

  - 应用程序被分割成数据片
  - 期待收到接收端的确认机制
  - TCP 将保持它首部和数据的校验和
  - TCP 能提供流量控制。
  - TCP 最可靠的就是得不到确认就会出现重传的机制

- socket 与 http以及 tcp、udp 之间的区别

  tcp 是最底层的通信协议，http 是基于 tcp，socket 是对 tcp 协议的封装，socket 则可以支持不同的传输层协议(tcp/udp) http 属于应用层协议，tcp 属于传输层协议而 IP 协议属于网络层协议。

  - 主机需要网络传输数据，**网络本质上是一种服务，主机和网络之间靠传输层接口**，就好比你要叫快递送东西；
  - 网络可以提供两种服务：
    - 可靠，面向连接；（TCP） 就像靠谱的快递，每一步都有反馈和监控，当然价格也是呵呵
    - 不可靠，尽力而为的传输 （UDP） 就像某些不靠谱的快递或者听都没听过的XX快递，价格低，但是能不能到就靠运气了。
  - 两种服务无所谓好坏，TCP 的可靠是需要消耗很多资源的，效率低 （大块，重要的文件等）UDP 不保证可靠性，但是效率高（视频，语音，不重要的小文件等）

  4，而其他的“**HTTP、FTP、SMTP 等所谓的“Application-layer Protocol”协议**”指的是在TCP/IP 通讯协议框架下具体实现特定功能的应用（HTTP 用来实现超文本传输，FTP文件传输，SMTP处理邮件等等），两者的关系，咳咳，关系通俗的说：

- handler 实现机制是怎么回事？

  主线程始终存在一个 looper 对象，从而不用去调用 looper.prepare() 方法来生成一个 looper 对象。然后就是 hander 发消息进去 mq，looper.loop() 在不停的取得消息，然后传递到 msg.target 的 dispatchmessage 方法，最后回到创建 handler 所在的主线程中，handlermessage 处理消息、

- Java实现多态有三个必要条件：继承、重写、向上转型。

  - 继承：在多态中必须存在有继承关系的子类和父类。
  - 重写：子类对父类中某些方法进行重新定义，在调用这些方法时就会调用子类的方法。
  - 向上转型：在多态中需要将子类的引用赋给父类对象，只有这样该引用才能够具备技能调用父类的方法和子类的方法。

- Equal() 与 == 方法之间的区别

  http://www.importnew.com/6804.html

  equals()和“==”操作用于对象的比较，检查俩对象的相等性，但是他们俩的主要区别在于前者是方法后者是操作符。由于java不支持操作符重载(overloading)，“==”的行为对于每个对象来说与equals()是完全相同的，但是equals()可以基于业务规则的不同而重写（overridden )。另一个需要注意的不同点是“==”习惯用于原生（primitive）类型之间的比较，而equals()仅用于对象之间的比较。

- static 的一些总结

  无论是变量，方法，还是代码块，只要用static修饰，就是在类被加载时就已经"准备好了",也就是可以被使用或者已经被执行，都可以脱离对象而执行。反之，如果没有static，则必须要依赖于对象实例。

- Java 中的 Final

  - final 数据 编译期间常量，运行时初始化
  - final 方法
  - final 类
  - final 参数  在匿名内部类中为了保证参数一致性

- Java NIO 与 IO 之间的区别

  Java IO 是面向流的，意味着每次从流中读取一个或者多个字节， 直到读取所有字节。NIO 是面向缓冲的，也就说会吧所有数据读取到一个缓冲区，然后对缓冲区中的数据进行相应的处理。IO是阻塞IO，而NIO是非阻塞IO。Java NIO 的选择器允许一个单独的线程来监视多个输入通道。NIO可让您只使用一个（或几个）单线程管理多个通道（网络连接或文件），但付出的代价是解析数据可能会比从一个阻塞流中读取数据更复杂。

  NIO主要有三大核心部分：Channel(通道)，Buffer(缓冲区), Selector。**传统IO基于字节流和字符流进行操作**，**而NIO基于Channel和Buffer(缓冲区)进行操作**，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。Selector(选择区)用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个线程可以监听多个数据通道。

- IPC 以及 Binder 机制

- Binder 机制为何存在

  在日常代码编写中. 我们总是会理所应当的对函数进行调用, 对变量进行访问. 之所以能顺利访问时因为所有的**函数**和**变量**都在同一个`进程`之中. 也就是说因为在一个`内存空间`中. 虚拟地址的映射规则完全一致. 而如果想访问的是其他进程的函数或者变量, 是不可能直接通过**内存地址**来直接进行访问的.

  既然进程之间不能访问, 那么如果通过`间接`的方法建立一条通道应该就可以解决了问题. 而`Binder`就是这样一个东西.

  Binder是Android中使用最广泛的IPC(Inter Process Communication)进程间通信机制.

  例如. 比如访问手机短信,联系人, startActivity()编写项目时我们新建一个界面, 服务,广播,内容提供者。WMS窗口管理所有View的显示删除等等. 几乎可以说Binder相当于人体的心脏可以让血液传输到各个地方提供声明的持续的保障.

- 策略模式是什么 使用组合来实现

  主要针对于不同的选择，我们经常倾向于用 case 和 if-else 来解决，带来许多问题，代码臃肿、逻辑复杂以及难以升级维护。而后者则是通过建立抽象，将不同的策略构建成一个具体的策略实现，通过不同的策略实现算法替换。在简化逻辑、结构的同时，增强了系统的可读性、稳定性、可扩展性，这对于较为复杂的业务逻辑显得更为直观，扩展也更为方便。

- 一个 Android 应用可以开启多个进程不？

  要想知道如何使用多进程，先要知道Android里的多进程概念。一般情况下，一个应用程序就是一个进程，这个进程名称就是应用程序包名。我们知道进程是系统分配资源和调度的基本单位，所以每个进程都有自己独立的资源和内存空间，别的进程是不能任意访问其他进程的内存和资源的。那如何让自己的应用拥有多个进程？很简单，我们的四大组件在AndroidManifest文件中注册的时候，有个属性是android:process，1.这里可以指定组件的所处的进程。默认就是应用的主进程。指定为别的进程之后，系统在启动这个组件的时候，就先创建（如果还没创建的话）这个进程，然后再创建该组件。你可以重载Application类的onCreate方法，打印出它的进程名称，就可以清楚的看见了。再设置android:process属性时候，有个地方需要注意：如果是android:process=":deamon"，以:开头的名字，则表示这是一个应用程序的私有进程，否则它是一个全局进程。私有进程的进程名称是会在冒号前自动加上包名，而全局进程则不会。一般我们都是有私有进程，很少使用全局进程。他们的具体区别不知道有没有谁能补充一下。

  2.使用多进程显而易见的好处就是分担主进程的内存压力。我们的应用越做越大，内存越来越多，将一些独立的组件放到不同的进程，它就不占用主进程的内存空间了。当然还有其他好处，有心人会发现Android后台进程里有很多应用是多个进程的，因为它们要常驻后台，特别是即时通讯或者社交应用，不过现在多进程已经被用烂了。典型用法是在启动一个不可见的轻量级私有进程，在后台收发消息，或者做一些耗时的事情，或者开机启动这个进程，然后做监听等。还有就是防止主进程被杀守护进程，守护进程和主进程之间相互监视，有一方被杀就重新启动它。应该还有还有其他好处，这里就不多说了。

  3.坏处的话，多占用了系统的空间，大家都这么用的话系统内存很容易占满而导致卡顿。消耗用户的电量。应用程序架构会变复杂，应为要处理多进程之间的通信。这里又是另外一个问题了。

- [海量数据的处理方法](https://github.com/nonstriater/Learn-Algorithms/tree/master/%E6%B5%B7%E9%87%8F%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86) 

- Bitmap 的编码、压缩和缓存 http://blog.csdn.net/jijiaxin1989/article/details/45063279

  问题归结于如何高效的去加载一张图片。 BitmapFactory 类提供了四类方法：decodeFile、decodeResource、decodeStream 和 decodeByteArray，分别用于支持从文件系统、资源、输入流以及字节数组中加载出一个 Bitmap 对象。如何高效地加载 Bitmap 呢？其实核心思想也很简单，那就是采用 BitmapFactory.Options 来加载所需尺寸的图片。这里假设通过 ImageView 来显示图片，很多时候 ImageView 并没有图片的原始尺寸那么大，这个时候把整个图片加载进来后在设给 ImageView，这显然是没有必要的，因为 ImageView 并没与办法显示原始的图片。通过 BitmapFactory.Options 就可以按一定的采样率来加载缩小后的图片，将缩小后的图片在 ImageView 中显示，这样就会降低内存占用从而在一定程度上避免 OOM，提高了 Bitmap 加载时的性能。BitmapFactory 提供的加载图片的四类方法都支持 BitmapFactory.Options 参数，通过它们就可以很方便地对一个图片进行采样缩放。通过BitmapFactory.Options 来缩放图片，主要是用到了它的 **inSampleSize 参数，即采样率**。通过采样率即可有效地加载图片，那么到底如何获取采样率呢？获取采用率也很简单，遵循如下流程：

  - （1）将 BitmapFactory.Options 的 **inJustDecodeBounds** 参数设置为 true 并加载图片。
  - （2）从 BItmapFactory.Options 中取出图片的原始宽高信息，它们对应于 outWidth 和 outHeight 参数。
  - （3）根据采样率的规则并结合目标 View 的所需大小计算出采样率 inSampleSize。
  - （4）**将 BitmapFactory.Options 的 inJustDecodeBounds 参数设为 false，然后重新加载图片**。

- List 与 Set 之间的区别

  - List,Set都是继承自Collection接口
  - List特点：元素有放入顺序，元素可重复 ，Set特点：元素无放入顺序，元素不可重复（注意：元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的） 
  - List接口有三个实现类：LinkedList，ArrayList，Vector ，Set接口有两个实现类：HashSet(底层由HashMap实现)，LinkedHashSet

- 字符流与字节流之间的区别

  - 字节流操作的基本单元为字节；字符流操作的基本单元为Unicode码元。
  - 字节流默认不使用缓冲区；字符流使用缓冲区。
  - 字节流通常用于处理二进制数据，实际上它可以处理任意类型的数据，但它不支持直接写入或读取Unicode码元；字符流通常处理文本数据，它支持写入及读取Unicode码元。

- AsyncTask 的实现原理

  总的来说，asynctask 基本的实现机制就是 handler + 线程池。它有两个线程池，一个用于排队，一个用于执行任务。其中线程池中的线程的数量和CPU核心数是相关的，最大容量为 128，所以 asynctask 不适合做一些耗时的、大型任务。asynctask 在 1.6 到 3.0 之间可以同时有 5 个任务执行，而 3.0 之后却只能执行一个任务。

  - onProgressUpdate、 onCancelled 和 onPostExecute 等都是通过 handler 的消息传递机制来调用的。所以 AsyncTask 可以理解为“线程池 + Handler”的组合。
  - 在 execute 方法中，会先去调用 onPreExecute 方法，之后再在线程池中执行 mFuture 。这时会调用 doInBackground 方法开始进行任务操作。 mWorker 和 mFuture 都是在构造器中初始化完成的。
  - AsyncTask 支持多线程进行任务操作，默认为单线程进行任务操作。

- Intent 的匹配规则？action，category，data

  - action 只要一个匹配就成功了
  - cateary 必须要所有的都匹配
  - data 的匹配比较法复杂，类似于 URI 规则，并且跟正则关系很大。

- Freaco 框架设计原理

- Android 中的 IPC 机制实现原理

- ContentProvider 的相关说法

- java 创建匿名内部类的目的是什么以及匿名内部类相关内容？

  内部类可以大大弥补 Java 在多重继承上的不足，提高代码的聚合性，可维护性。内部类有以下四种分类。我们给匿名内部类传递参数的时候，若该形参在内部类中需要被使用，那么该形参必须要为final。也就是说：**当所在的方法的形参需要被内部类里面使用时，该形参必须为final。**

  - 成员内部类

    成员内部类是最普通的内部类，它的定义为位于另一个类的内部

  - 局部内部类

    局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内

  - 静态嵌套类

    又叫静态局部类、嵌套内部类，就是修饰为static的内部类。声明为static的内部类，不需要内部类对象和外部类对象之间的联系，就是说我们可以直接引用outer.inner，即不需要创建外部类，也不需要创建内部类。

  - 匿名内部类

    **匿名内部类是唯一一种没有构造器的类**。正因为其没有构造器，所以匿名内部类的使用范围非常有限，大部分匿名内部类用于接口回调。匿名内部类在编译的时候由系统自动起名为Outter$1.class。一般来说，匿名内部类用于继承其他类或是实现接口，并不需要增加额外的方法，只是对继承方法的实现或是重写。匿名内部类的初始化，因为其没有构造器可以用初始化块来解决。

- Fragment 的生命周期，以及每一个阶段是干什么的？

  Fragment 的生命周期写一下，onAttach 和 onDetach 的作用 **onAttach（）**：当Activity和Fragment交互的时候，我们可以在Activity中通过Fragment.setArguments()的方法为Fragment提供数据，然后再Fragment的onAttach（）方法中getArguments()获得一个Bundle对象。  **onCreateView()**:创建该Fragment对应的视图，在这里你必须将创建的视图返回诶调用者。它跟onCreate（）的区别：onCreate()是指创建该Fragment，你可以在其中初始化除了View之外的东西。 **onActivityCreated()**  当Activity的onCreate()方法调用时，该方法被调用  **onDetach()**  当Fragment和Activity解除关联时调用该方法。

  - onAttach()：Fragment与Activity绑定的时候被调用（Activity被传到这里）
  - onCreateView()：Fragment生成View时被调用
  - onActivityCreated()：宿主Activity的onCreate()返回的时候被调用
  - onDestroyView()：Fragment的View移除时被调用
  - onDetach()：Fragment与宿主Activity解绑的时候被调用

- Activity 的生命周期详解 http://ezio.farbox.com/post/android-activity

- ConcurrenthasnMap 的原理分析

  http://www.infoq.com/cn/articles/ConcurrentHashMap/

- 为什么连接的时候是三次握手，关闭的时候却是四次握手？
  答：因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，"你发的FIN报文我收到了"。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。

- TCP 三次握手

  TCP是面向连接的，所谓面向连接，就是当计算机双方通信时必需先建立连接，然后数据传送，最后拆除连接三个过程。并且TCP在建立连接时又分三步走：

  第一步是请求端（客户端）发送一个包含SYN即同步（Synchronize）标志的TCP报文，SYN同步报文会指明客户端使用的端口以及TCP连接的初始序号；

  第二步，服务器在收到客户端的SYN报文后，将返回一个SYN+ACK的报文，表示客户端的请求被接受，同时TCP序号被加一，ACK即确认（Acknowledgement）。

  第三步，客户端也返回一个确认报文ACK给服务器端，同样TCP序列号被加一，到此一个TCP连接完成。 然后才开始通信的第二步：数据处理。

  这就是所说的TCP三次握手（Three-way Handshake）。

  简单的说就是：（C：客户端，S：服务端）

  C：SYN到S

  S：如成功--返回给C（SYN+ACK）

  C：如成功---返回给S（ACK）

  以上是正常的建立连接方式，但如下：

  假设一个C向S发送了SYN后无故消失了，那么S在发出SYN+ACK应答报文后是无法收到C的ACK报文的（第三次握手无法完成），这种情况下S一般会重试（再次发送SYN+ACK给客户端）并等待一段时间后丢弃这个未完成的连接，这段时间的长度我们称为SYNTimeout，一般来说这个时间是分钟的数量级（大约为30秒-2分钟）；一个C出现异常导致S的一个线程等待1分钟并不是什么很大的问题，但如果有一个恶意的攻击者大量模拟这种情况，S将为了维护一个非常大的半连接列表而消耗非常多的资源----数以万计的半连接，即使是简单的保存并遍历也会消耗非常多的CPU时间和内存，何况还要不断对这个列表中的IP进行SYN+ACK的重试。实际上如果S的TCP/IP栈不够强大，最后的结果往往是堆栈溢出崩溃---即使S的系统足够强大，S也将忙于处理攻击者伪造的TCP连接请求而无暇理睬客户的正常请求（毕竟C的正常请求比率非常之小），此时从正常客户的角度看来，S失去响应，这种情况我们称作：服务器端受到了SYN Flood攻击（SYN洪水攻击）

- OSI 七层模型

  ![](http://ww1.sinaimg.cn/large/b10d1ea5ly1fd6ciajuvdj20fx0bxdmg)

- sleep 与 wait 之间的区别

  首先，要记住这个差别，“sleep是Thread类的方法,wait是Object类中定义的方法”。尽管这两个方法都会影响线程的执行行为，但是本质上是有区别的。

  Thread.sleep不会导致锁行为的改变，如果当前线程是拥有锁的，那么Thread.sleep不会让线程释放锁。如果能够帮助你记忆的话，可以简单认为和锁相关的方法都定义在Object类中，因此调用Thread.sleep是不会影响锁的相关行为。

  Thread.sleep和Object.wait都会暂停当前的线程，对于CPU资源来说，不管是哪种方式暂停的线程，都表示它暂时不再需要CPU的执行时间。OS会将执行时间分配给其它线程。区别是，调用wait后，需要别的线程执行notify/notifyAll才能够重新获得CPU执行时间。

  线程的状态参考 Thread.State的定义。新创建的但是没有执行（还没有调用start())的线程处于“就绪”，或者说Thread.State.NEW状态。

  Thread.State.BLOCKED（阻塞）表示线程正在获取锁时，因为锁不能获取到而被迫暂停执行下面的指令，一直等到这个锁被别的线程释放。BLOCKED状态下线程，OS调度机制需要决定下一个能够获取锁的线程是哪个，这种情况下，就是产生锁的争用，无论如何这都是很耗时的操作。

  wait 使用notify 和 notifyAll 或者指定睡眠时间来唤醒当前线程池中等待的线程。

  **最大区别是 sleep 睡眠时仍然保持对象锁，占用该对象锁，而 wait 则会在睡眠时候释放该对象锁、**

- 进程和线程

  进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位.线程是进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源.一个线程可以创建和撤销另一个线程;同一个进程中的多个线程之间可以并发执行.相对进程而言，线程是一个更加接近于执行体的概念，它可以与同进程中的其他线程共享数据，但拥有自己的栈空间，拥有独立的执行序列。

  在串行程序基础上引入线程和进程是为了提高程序的并发度，从而提高程序运行效率和响应时间。

  **进程和线程**的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。**线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间**，**一个线程死掉就等于整个进程死掉**，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

  **1) 简而言之,一个程序至少有一个进程,一个进程至少有一个线程.**

  2) 线程的划分尺度小于进程，使得多线程程序的并发性高。

  3) 另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

  4) 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。**但是线程不能够独立执行，**必须依存在应用程序中，由应用程序提供多个线程执行控制。

  5) 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

- TCP 的流量控制和拥塞控制

  - 利用滑动窗口实现流量控制
  - 慢开始( slow-start )、拥塞避免( congestion avoidance )、快重传( fast retransmit )和快恢复( fast recovery )。


- Bitmap 的压缩和缓存

  通过设置 BitmapFactory.options 中 inSampleSize 的值就可以实现，也就是说采样率。提供内存缓存让程序快速处理和加载图片。其中最核心的类是LruCache (此类在android-support-v4的包中提供) 。这个类非常适合用来缓存图片，它的主要算法原理是把最近使用的对象用强引用存储在 LinkedHashMap 中，并且把最近最少使用的对象在缓存值达到预设定值之前从内存中移除。

  ​

- http 与 http2 之间的区别

  http是HTTP协议运行在TCP之上。所有传输的内容都是明文，客户端和服务器端都无法验证对方的身份。

  https是HTTP运行在SSL/TLS之上，SSL/TLS运行在TCP之上。所有传输的内容都经过加密，加密采用对称加密，但对称加密的密钥用服务器方的证书进行了非对称加密。此外客户端可以验证服务器端的身份，如果配置了客户端验证，服务器方也可以验证客户端的身份。前者默认工作在 80 端口，后者默认工作在 443 端口。

- Sqlite 如何优化

  SQLite的数据库本质文件读写操作，频繁操作打开和关闭是很耗时和浪费资源的。

  - 建立索引,这种情况下针对数据量较少的情况下
  - 编译 sql 语句，用指定符号来替代查询语句
  - 及时关闭游标
  - 耗时异步化，耗时操作放在线程中执行
  - 按需要获取数据信息

- view 动画(视图动画) 的缺点

  - 只针对 View 对象进行动画

  - 然后补间动画还有一个缺陷，就是它只能够实现移动、缩放、旋转和淡入淡出这四种动画操作

  - 就是它只是改变了View的显示效果而已，而不会真正去改变View的属性。什么意思呢？比如说，现在屏幕的左上角有一个按钮，然后我们通过补间动画将它移动到了屏幕的右下角，现在你可以去尝试点击一下这个按钮，点击事件是绝对不会触发的，因为实际上这个按钮还是停留在屏幕的左上角，只不过补间动画将这个按钮绘制到了屏幕的右

    ​

    ​

    ​

    ​

    ​

    ​

    ​

    ​

    ​

    ​

    ​

  ​

  ​
