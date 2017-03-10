* 各大热修复补丁方案比较

  * Dexposed

    基于 Xposed 实现的无侵入的运行时 [AOP (Aspect-oriented Programming)](http://en.wikipedia.org/wiki/Aspect-oriented_programming)  框架，可以实现在线修复 Bug，修复粒度方法级别，但是由于对 ART 虚拟机不支持，导致其对 Android 5.0、6.0 均不支持，使用局限性太大。目前基于这一原理实现的解决方案是手淘团队开源的 [Dexposed](https://github.com/alibaba/dexposed) 项目。

  * Andfix

    native hook 方式，其核心部分在 JNI 层对方法进行替换，替换有问题的方法，修复粒度方法级别，无法在类中新增和删减字段，可以做到即时生效，该原理的实现方案主要是阿里团队开源的 [AndFix](https://github.com/alibaba/AndFix) 。

  * classloader

    multidex方案的实现，其实就是把多个dex放进app的classloader之中，从而使得所有dex的类都能被找到。而实际上findClass的过程中，如果出现了重复的类，参照下面的类加载的实现，是会使用第一个找到的类的。该热补丁方案就是从这一点出发，只要把有问题的类修复后，放到一个单独的dex，通过反射插入到dexElements数组的最前面，不就可以让虚拟机加载到打完补丁的class了吗。

    说到此处，似乎已经是一个完整的方案了，但在实践中，会发现运行加载类的时候报preverified错误，原来在`DexPrepare.cpp`，将dex转化成odex的过程中，会在`DexVerify.cpp`进行校验，验证如果直接引用到的类和clazz是否在同一个dex，如果是，则会打上CLASS_ISPREVERIFIED标志。通过在所有类（Application除外，当时还没加载自定义类的代码）的构造函数插入一个对在单独的dex的类的引用，就可以解决这个问题。空间使用了javaassist进行编译时字节码插入。

  * 微信

    dex 文件全量替换，基于 DexDiff 技术，对比修复前后的 dex 文件，生成 patch.dex，再根据 patch.dex 更新有问题的 dex 文件。该方案由微信团队提出：[微信Android热补丁实践演进之路](http://bugly.qq.com/bbs/forum.php?mod=viewthread&tid=1264) ，

* ART、JIT、AOT、Dalvik 之间的区别和联系

  https://github.com/ZhaoKaiQiang/AndroidDifficultAnalysis/blob/master/10.ART%E3%80%81JIT%E3%80%81AOT%E3%80%81Dalvik%E4%B9%8B%E9%97%B4%E6%9C%89%E4%BB%80%E4%B9%88%E5%85%B3%E7%B3%BB%EF%BC%9F.md

  用 java 开发 android 时候，编译打包 apk 时候，经过 java 编译器将 java 文件编译为 class 文件

  dx 工具应用编译输出的类文件转化为 Dalvik 字节码，即 dex 文件。

  - JIT代表运行时编译策略，也可以理解成一种运行时编译器，是为了加快Dalvik虚拟机解释dex速度提出的一种技术方案，来缓存频繁使用的本地机器码
  - ART和Dalvik都算是一种Android运行时环境，或者叫做虚拟机，用来解释dex类型文件。但是ART是安装时解释，Dalvik是运行时解释
  - AOT可以理解为一种编译策略，即运行前编译，ART虚拟机的主要特征就是AOT

* Android 动态加载基础 classloader 工作机制

  一个 android 应用启动至少有两个 classloader ：BootClassLoader 和 PathClassLoader。我们可以自己创建 classloader 实例来加载 dex 文件中的 class，但是在创建 classloader 实例的时候，需要一个现有的classloader 来作为新创建的实例的 parent，由此整个 android 系统中所有的 classloader 实例都会被一棵树关联起来，其实就是 classloader 的双亲代理模型的特点。在 jvm 中的 classloader 通过 defineclass 方法加载 jar 中的 class ，但是在 android 中这个方法被弃用了，取而代之的是 loadclass 方法。ClassLoader的创建和加载类的过程的完成了。有趣的是，标准JVM中，ClassLoader是用defineClass加载类的，而Android中defineClass被弃用了，改用了loadClass方法，而且加载类的过程也挪到了DexFile中，在**DexFile中加载类的具体方法也叫defineClass**，不知道是Google故意写成这样的还是巧合。

  如果希望动态加载，加载一个新版本的 dex 文件，从而替换旧类，从而修复bug，此时你要保证新的类应该优先于旧类被加载。，在Java中，只有当两个实例的类名、包名以及加载其的ClassLoader都相同，才会被认为是同一种类型。上面分别加载的新类和旧类，虽然包名和类名都完全一样，但是由于加载的ClassLoader不同，所以并不是同一种类型，在实际使用中可能会出现类型不符异常。

  https://segmentfault.com/a/1190000004062880

* 65535 问题？为什么是 65535 以及如何解决这个问题？

  单个 dex 文件中，method 个数采用原生类型 short 来进行索引，而 short 类型最大能存 65536 个

  解决 65535 问题的办法以及 dex 工作原理，推荐下面这篇文章，写的非常好：

  http://blog.csdn.net/zhaokaiqiang1992/article/details/50412975

* Android 中的 Dalvik/ART 加载 class 文件与 JVM 那样加载 jar 之间有什么区别？

  Android 中的 Dalvik/ART 无法像 JVM 那样 **直接** 加载 class 文件和 jar 文件中的 class，需要通过 dx 工具来**优化转换成 Dalvik byte code 才行，**只能通过 dex 或者 包含 dex 的jar、apk 文件来加载（注意 odex 文件后缀可能是 .dex 或 .odex，也属于 dex 文件），因此 Android 中的 ClassLoader 工作就交给了 BaseDexClassLoader 来处理。

* class 的热修复

  最基础的就是就是基于 class 的热修复了，一般 class 都是通过 classloader 去生成的，不只有一个 classloader，比如 pathclassloader 和 dexclassloader，它们之间的区别是前者只能加载本地的 class 即加载本地系统中已经安装过的 apk，而后者可以加载任何 apk 或者 jar 压缩包中的 dex 文件。它们都是继承 BaseDexClassLoader 。很明显，对比 PathClassLoader 只能加载已经安装应用的 dex 或 apk 文件，DexClassLoader 则没有此限制，可以从 SD 卡上加载包含 class.dex 的 .jar 和 .apk 文件，这也是插件化和热修复的基础，在不需要安装应用的情况下，完成需要使用的 dex 的加载。

  **关于热修复实现的一个点**，就是将补丁 dex 文件放到 dexElements 数组前面，这样在加载 class 时，优先找到补丁包中的 dex 文件，加载到 class 之后就不再寻找，从而原来的 apk 文件中同名的类就不会再使用，从而达到修复的目的，虽然说起来较为简单，但是实现起来还有很多细节需要注意，本文先热身，后期再分析具体实现。

  至此，BaseDexClassLader 寻找 class 的路线就清晰了：

  1. 当传入一个完整的类名，调用 BaseDexClassLader 的 `findClass(String name) `方法
  2. BaseDexClassLader 的 findClass 方法会交给 DexPathList 的 `findClass(String name, List suppressed `方法处理
  3. 在 DexPathList 方法的内部，会遍历 dexFile ，通过 DexFile 的 `dex.loadClassBinaryName(name, definingContext, suppressed)`来完成类的加载

* dex 的工作机制是什么样的？

* 什么是 dex 文件

  dex 文件是 android 系统中可执行文件，包含应用程序的全部指令操作以及运行时数据。由于 dalvik 是一种针对嵌入式设备而设计的特殊虚拟机，所以 dex 文件与标准版的 class 文件在结构上有着本质上的区别。当java程序编译成class后，还需要使用dx工具将所有的class文件整合到一个dex文件，目的是其中各个类能够共享数据，在一定程度上降低了冗余，同时也是文件结构更加经凑，实验表明，dex文件是传统jar文件大小的50%左右。

  ![](http://ww1.sinaimg.cn/large/006dXScfly1fdcvae0muvj30gs0ea447)

* Java 类加载过程 

  具体参考这篇文章 http://www.importnew.com/23650.html

* ListView 优化技巧

  **ListView的优化** 问：getView的执行时间应该控制在多少以内？ 答：1秒之内屏幕可以完成30帧的绘制，这样人看起来会觉得比较流畅（苹果手机是接近60帧，高于60之后人眼也无法分辨）。每帧可使用的时间：1000ms/30 = 33.33 ms 每个ListView一般要显示6个ListItem，加上1个重用convertView：33.33ms/7 = 4.76ms 即是说，每个getView要在4.76ms内完成工作才会较流畅，但是事实上，每个getView间的调用也会有一定的间隔（有可能是由于handler在处理别的消息），UI的handler处理不好的话，这个间隔也可能会很大（0ms-200ms）。结论就是，**留给getView使用的时间应该在4ms之内**，如果不能控制在这之内的话，ListView的滑动就会有卡顿的现象。

  优化建议： 

  * 重用ConvertView (避免每次都创建ItemView); 
  * 使用View Holder模式 (避免每次都inflate View和findViewById)； 
  * 使用异步线程加载图片（一般都是直接使用图片库加载，如Glide, Picasso）； 
  * 在adapter的getView方法中尽可能的减少逻辑判断，特别是耗时的判断； 
  * 避免GC（可以从logcat中查看有无GC的LOG）； 
  * 在快速滑动时不要加载图片； 
  * 将ListView的scrollingCache和animateCache这两个属性设置为false（默认是true）; 
  * 尽可能减少List Item的Layout层次（如可以使用RelativeLayout替换LinearLayout，或使用自定的View代替组合嵌套使用的Layout）； 
  * 使用硬件加速来解决莫名的卡顿问题，给Activity添加配置`android:hardwareAccelerated="true"`；

  关于第6点快速滑动时不要加载图片的优化实现原理： 在适配器中声明一个全局的boolean变量用来保存此刻是否在滚动，然后通过给ListView设置滚动监听，然后在滚动监听器的onScrollStateChanged()方法中给boolean值赋值，看是否在滚动。 这样在我们使用这个适配器的时候，就可以根据滚动状态的不同来判断：比如正在滚动的时候就只显示内存缓存的图片，如果内存缓存中没有就显示一张默认图片；而如果没有在滚动就采用正常的图片加载方案去加载网络或者缓存中的图片。

* Bitmap 总结

  **Bitmap 和 Drawable** Bitmap代表的是图片资源在内存中的数据结构，如它的像素数据、长宽等属性都存放在Bitmap对象中。**Bitmap类的构造函数是私有的，只能是通过JNI实例化，系统提供BitmapFactory工厂类从File、Stream和byte[]创建Bitmap的方式**。

  Drawable官方文档说明为可绘制物件的一般抽象。View也是可以绘制的，但Drawable与View不同，**Drawable不接受事件，无法与用户进行交互**。我们知道很多UI控件都提供设置Drawable的接口，如ImageView可以通过setImageDrawable(Drawable drawable)设置它的显示，这个drawable可以是来自Bitmap的BitmapDrawable，也可以是其他的如ShapeDrawable。

  **Drawable是一种抽像，最终实现的方式可以是绘制Bitmap的数据或者图形、Color数据等。**

  **Bitmap优化** 在Android 3.0之前的版本，Bitmap像素数据存放在Native内存中，而且Nativie内存的释放是不确定的，容易内存溢出而Crash，所以一般我们不使用的图片要调用recycle()。 从3.0开始，Bitmap像素数据和Bitmap对象一起存放在Dalvik堆内存中（从源代码上看是多了一个byte[] buffer用来存放数据），也就是我们常说的Java Heap内存。 除了这点改变之外，**3.0版本的还增加了一个inBitmap属性（BitmapFactory.Options.inBitmap）。如果设置了这个属性则会重用这个Bitmap的内存从而提升性能。但是这个重用是有条件的，在Android4.4之前只能重用相同大小的Bitmap，Android4.4+则只要比重用Bitmap小即可。**

  其他优化手段： 1.使用采样率（inSampleSize），如果最终要压缩图片，如显示缩略图，我们并不需要加载完整的图片数据，只需要按一定的比例加载即可； 2.使用Matrix变形等，比如使用Matrix进行放大，虽然图像大了，但并没有占用更多的内存。

* 用广播来更新界面是否合适

  不合适吧，少量的还是可以，但是广播的启动和接受都是会花费一定代价的。广播的到达还有可能会延时。

* Java 重写与重载之间的区别

  * 重写[Override]是子类对父类允许访问的方法实现过程进行重新编写，返回值和形参都不能变
  * 重载[Overload]是在一个类中，方法名字相同，而形参不同，返回值可以相同，也可以不同。

* TCP 如何保证可靠性

  * 应用程序被分割成数据片
  * 期待收到接收端的确认机制
  * TCP 将保持它首部和数据的校验和
  * TCP 能提供流量控制。
  * TCP 最可靠的就是得不到确认就会出现重传的机制

* socket 与 http以及 tcp、udp 之间的区别

  tcp 是最底层的通信协议，http 是基于 tcp，socket 是对 tcp 协议的封装，socket 则可以支持不同的传输层协议(tcp/udp) http 属于应用层协议，tcp 属于传输层协议而 IP 协议属于网络层协议。

  * 主机需要网络传输数据，**网络本质上是一种服务，主机和网络之间靠传输层接口**，就好比你要叫快递送东西；
  * 网络可以提供两种服务：
    * 可靠，面向连接；（TCP） 就像靠谱的快递，每一步都有反馈和监控，当然价格也是呵呵
    * 不可靠，尽力而为的传输 （UDP） 就像某些不靠谱的快递或者听都没听过的XX快递，价格低，但是能不能到就靠运气了。
  * 两种服务无所谓好坏，TCP 的可靠是需要消耗很多资源的，效率低 （大块，重要的文件等）UDP 不保证可靠性，但是效率高（视频，语音，不重要的小文件等）

  4，而其他的“**HTTP、FTP、SMTP 等所谓的“Application-layer Protocol”协议**”指的是在TCP/IP 通讯协议框架下具体实现特定功能的应用（HTTP 用来实现超文本传输，FTP文件传输，SMTP处理邮件等等。

* handler 实现机制是怎么回事？

  主线程始终存在一个 looper 对象，从而不用去调用 looper.prepare() 方法来生成一个 looper 对象。然后就是 hander 发消息进去 mq，looper.loop() 在不停的取得消息，然后传递到 msg.target 的 dispatchmessage 方法，最后回到创建 handler 所在的主线程中，handlermessage 处理消息、

* Java实现多态有三个必要条件：继承、重写、向上转型。

  * 继承：在多态中必须存在有继承关系的子类和父类。
  * 重写：子类对父类中某些方法进行重新定义，在调用这些方法时就会调用子类的方法。
  * 向上转型：在多态中需要将子类的引用赋给父类对象，只有这样该引用才能够具备技能调用父类的方法和子类的方法。

* Equal() 与 == 方法之间的区别

  http://www.importnew.com/6804.html

  equals()和“==”操作用于对象的比较，检查俩对象的相等性，但是他们俩的主要区别在于前者是方法后者是操作符。由于java不支持操作符重载(overloading)，“==”的行为对于每个对象来说与equals()是完全相同的，但是equals()可以基于业务规则的不同而重写（overridden )。**另一个需要注意的不同点是“==”习惯用于原生（primitive）类型之间的比较，而equals()仅用于对象之间的比较。**

* static 的一些总结

  无论是变量，方法，还是代码块，只要用static修饰，就是在类被加载时就已经"准备好了",也就是可以被使用或者已经被执行，都可以脱离对象而执行。反之，如果没有static，则必须要依赖于对象实例。

* Java 中的 Final

  * final 数据 编译期间常量，运行时初始化
  * final 方法
  * final 类
  * final 参数  在匿名内部类中为了保证参数一致性

* Java NIO 与 IO 之间的区别

  Java IO 是面向流的，意味着每次从流中读取一个或者多个字节， 直到读取所有字节。NIO 是面向缓冲的，也就说会吧所有数据读取到一个缓冲区，然后对缓冲区中的数据进行相应的处理。IO是阻塞IO，而NIO是非阻塞IO。Java NIO 的选择器允许一个单独的线程来监视多个输入通道。NIO可让您只使用一个（或几个）单线程管理多个通道（网络连接或文件），但付出的代价是解析数据可能会比从一个阻塞流中读取数据更复杂。

  NIO主要有三大核心部分：Channel(通道)，Buffer(缓冲区), Selector。**传统IO基于字节流和字符流进行操作**，**而NIO基于Channel和Buffer(缓冲区)进行操作**，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。Selector(选择区)用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个线程可以监听多个数据通道。

* IPC 以及 Binder 机制

* Binder 机制为何存在

  在日常代码编写中. 我们总是会理所应当的对函数进行调用, 对变量进行访问. 之所以能顺利访问时因为所有的**函数**和**变量**都在同一个`进程`之中. 也就是说因为在一个`内存空间`中. 虚拟地址的映射规则完全一致. 而如果想访问的是其他进程的函数或者变量, 是不可能直接通过**内存地址**来直接进行访问的.

  既然进程之间不能访问, 那么如果通过`间接`的方法建立一条通道应该就可以解决了问题. 而`Binder`就是这样一个东西.

  Binder是Android中使用最广泛的IPC(Inter Process Communication)进程间通信机制.

  例如. 比如访问手机短信,联系人, startActivity()编写项目时我们新建一个界面, 服务,广播,内容提供者。WMS窗口管理所有View的显示删除等等. 几乎可以说Binder相当于人体的心脏可以让血液传输到各个地方提供声明的持续的保障.

* 策略模式是什么 使用组合来实现

  主要针对于不同的选择，我们经常倾向于用 case 和 if-else 来解决，带来许多问题，代码臃肿、逻辑复杂以及难以升级维护。而后者则是通过建立抽象，将不同的策略构建成一个具体的策略实现，通过不同的策略实现算法替换。在简化逻辑、结构的同时，增强了系统的可读性、稳定性、可扩展性，这对于较为复杂的业务逻辑显得更为直观，扩展也更为方便。

* 一个 Android 应用可以开启多个进程不？

  要想知道如何使用多进程，先要知道Android里的多进程概念。一般情况下，一个应用程序就是一个进程，这个进程名称就是应用程序包名。我们知道进程是系统分配资源和调度的基本单位，所以每个进程都有自己独立的资源和内存空间，别的进程是不能任意访问其他进程的内存和资源的。那如何让自己的应用拥有多个进程？很简单，我们的四大组件在AndroidManifest文件中注册的时候，有个属性是android:process，1.这里可以指定组件的所处的进程。默认就是应用的主进程。指定为别的进程之后，系统在启动这个组件的时候，就先创建（如果还没创建的话）这个进程，然后再创建该组件。你可以重载Application类的onCreate方法，打印出它的进程名称，就可以清楚的看见了。再设置android:process属性时候，有个地方需要注意：如果是android:process=":deamon"，以:开头的名字，则表示这是一个应用程序的私有进程，否则它是一个全局进程。私有进程的进程名称是会在冒号前自动加上包名，而全局进程则不会。一般我们都是有私有进程，很少使用全局进程。他们的具体区别不知道有没有谁能补充一下。

  2.使用多进程显而易见的好处就是分担主进程的内存压力。我们的应用越做越大，内存越来越多，将一些独立的组件放到不同的进程，它就不占用主进程的内存空间了。当然还有其他好处，有心人会发现Android后台进程里有很多应用是多个进程的，因为它们要常驻后台，特别是即时通讯或者社交应用，不过现在多进程已经被用烂了。典型用法是在启动一个不可见的轻量级私有进程，在后台收发消息，或者做一些耗时的事情，或者开机启动这个进程，然后做监听等。还有就是防止主进程被杀守护进程，守护进程和主进程之间相互监视，有一方被杀就重新启动它。应该还有还有其他好处，这里就不多说了。

  3.坏处的话，多占用了系统的空间，大家都这么用的话系统内存很容易占满而导致卡顿。消耗用户的电量。应用程序架构会变复杂，应为要处理多进程之间的通信。这里又是另外一个问题了。

* [海量数据的处理方法](https://github.com/nonstriater/Learn-Algorithms/tree/master/%E6%B5%B7%E9%87%8F%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86) 

* Bitmap 的编码、压缩和缓存 http://blog.csdn.net/jijiaxin1989/article/details/45063279

  问题归结于如何高效的去加载一张图片。 BitmapFactory 类提供了四类方法：decodeFile、decodeResource、decodeStream 和 decodeByteArray，分别用于支持从文件系统、资源、输入流以及字节数组中加载出一个 Bitmap 对象。如何高效地加载 Bitmap 呢？其实核心思想也很简单，那就是采用 BitmapFactory.Options 来加载所需尺寸的图片。这里假设通过 ImageView 来显示图片，很多时候 ImageView 并没有图片的原始尺寸那么大，这个时候把整个图片加载进来后在设给 ImageView，这显然是没有必要的，因为 ImageView 并没与办法显示原始的图片。通过 BitmapFactory.Options 就可以按一定的采样率来加载缩小后的图片，将缩小后的图片在 ImageView 中显示，这样就会降低内存占用从而在一定程度上避免 OOM，提高了 Bitmap 加载时的性能。BitmapFactory 提供的加载图片的四类方法都支持 BitmapFactory.Options 参数，通过它们就可以很方便地对一个图片进行采样缩放。通过BitmapFactory.Options 来缩放图片，主要是用到了它的 **inSampleSize 参数，即采样率**。通过采样率即可有效地加载图片，那么到底如何获取采样率呢？获取采用率也很简单，遵循如下流程：

  - （1）将 BitmapFactory.Options 的 **inJustDecodeBounds** 参数设置为 true 并加载图片。
  - （2）从 BItmapFactory.Options 中取出图片的原始宽高信息，它们对应于 outWidth 和 outHeight 参数。
  - （3）根据采样率的规则并结合目标 View 的所需大小计算出采样率 inSampleSize。
  - （4）**将 BitmapFactory.Options 的 inJustDecodeBounds 参数设为 false，然后重新加载图片**。

* List 与 Set 之间的区别

  * List,Set都是继承自Collection接口
  * List特点：元素有放入顺序，元素可重复 ，Set特点：元素无放入顺序，元素不可重复（注意：元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的） 
  * List接口有三个实现类：LinkedList，ArrayList，Vector ，Set接口有两个实现类：HashSet(底层由HashMap实现)，LinkedHashSet

* 字符流与字节流之间的区别

  - 字节流操作的基本单元为字节；字符流操作的基本单元为Unicode码元。
  - 字节流默认不使用缓冲区；字符流使用缓冲区。
  - 字节流通常用于处理二进制数据，实际上它可以处理任意类型的数据，但它不支持直接写入或读取Unicode码元；字符流通常处理文本数据，它支持写入及读取Unicode码元。

* AsyncTask 的实现原理

  总的来说，asynctask 基本的实现机制就是 handler + 线程池。它有两个线程池，一个用于排队，一个用于执行任务。其中线程池中的线程的数量和CPU核心数是相关的，最大容量为 128，所以 asynctask 不适合做一些耗时的、大型任务。asynctask 在 1.6 到 3.0 之间可以同时有 5 个任务执行，而 3.0 之后却只能执行一个任务。

  - onProgressUpdate、 onCancelled 和 onPostExecute 等都是通过 handler 的消息传递机制来调用的。所以 AsyncTask 可以理解为“线程池 + Handler”的组合。
  - 在 execute 方法中，会先去调用 onPreExecute 方法，之后再在线程池中执行 mFuture 。这时会调用 doInBackground 方法开始进行任务操作。 mWorker 和 mFuture 都是在构造器中初始化完成的。
  - AsyncTask 支持多线程进行任务操作，默认为单线程进行任务操作。

* Intent 的匹配规则？action，category，data

  * action 只要一个匹配就成功了
  * cateary 必须要所有的都匹配
  * data 的匹配比较法复杂，类似于 URI 规则，并且跟正则关系很大。

* Freaco 框架设计原理

* Android 中的 IPC 机制实现原理

* ContentProvider 的相关说法

  * 作用是：1. 对外提供统一数据接口 2.跨进程数据共享
  * contentprovider 如何在不同程序中传递数据的：本质上还是通过匿名共享内存

* java 创建匿名内部类的目的是什么以及匿名内部类相关内容？

  内部类可以大大弥补 Java 在多重继承上的不足，提高代码的聚合性，可维护性。内部类有以下四种分类。我们给匿名内部类传递参数的时候，若该形参在内部类中需要被使用，那么该形参必须要为final。也就是说：**当所在的方法的形参需要被内部类里面使用时，该形参必须为final。**

  * 成员内部类

    成员内部类是最普通的内部类，它的定义为位于另一个类的内部

  * 局部内部类

    局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内

  * 静态嵌套类

    又叫静态局部类、嵌套内部类，就是修饰为static的内部类。声明为static的内部类，不需要内部类对象和外部类对象之间的联系，就是说我们可以直接引用outer.inner，即不需要创建外部类，也不需要创建内部类。

  * 匿名内部类

    **匿名内部类是唯一一种没有构造器的类**。正因为其没有构造器，所以匿名内部类的使用范围非常有限，大部分匿名内部类用于接口回调。匿名内部类在编译的时候由系统自动起名为Outter$1.class。一般来说，匿名内部类用于继承其他类或是实现接口，并不需要增加额外的方法，只是对继承方法的实现或是重写。匿名内部类的初始化，因为其没有构造器可以用初始化块来解决。

* Fragment 的生命周期，以及每一个阶段是干什么的？

  Fragment 的生命周期写一下，onAttach 和 onDetach 的作用 **onAttach（）**：当Activity和Fragment交互的时候，我们可以在Activity中通过Fragment.setArguments()的方法为Fragment提供数据，然后再Fragment的onAttach（）方法中getArguments()获得一个Bundle对象。  **onCreateView()**:创建该Fragment对应的视图，在这里你必须将创建的视图返回诶调用者。它跟onCreate（）的区别：onCreate()是指创建该Fragment，你可以在其中初始化除了View之外的东西。 **onActivityCreated()**  当Activity的onCreate()方法调用时，该方法被调用  **onDetach()**  当Fragment和Activity解除关联时调用该方法。

  * onAttach()：Fragment与Activity绑定的时候被调用（Activity被传到这里）
  * onCreateView()：Fragment生成View时被调用
  * onActivityCreated()：宿主Activity的onCreate()返回的时候被调用
  * onDestroyView()：Fragment的View移除时被调用
  * onDetach()：Fragment与宿主Activity解绑的时候被调用

* Activity 的生命周期详解 http://ezio.farbox.com/post/android-activity

* ConcurrenthasnMap 的原理分析

  http://www.infoq.com/cn/articles/ConcurrentHashMap/

* 为什么连接的时候是三次握手，关闭的时候却是四次握手？
  答：因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，"你发的FIN报文我收到了"。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。

* TCP 三次握手

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

* OSI 七层模型

  ![](http://ww1.sinaimg.cn/large/b10d1ea5ly1fd6ciajuvdj20fx0bxdmg)

* sleep 与 wait 之间的区别

  首先，要记住这个差别，“sleep是Thread类的方法,wait是Object类中定义的方法”。尽管这两个方法都会影响线程的执行行为，但是本质上是有区别的。

  Thread.sleep不会导致锁行为的改变，如果当前线程是拥有锁的，那么Thread.sleep不会让线程释放锁。如果能够帮助你记忆的话，可以简单认为和锁相关的方法都定义在Object类中，因此调用Thread.sleep是不会影响锁的相关行为。

  Thread.sleep和Object.wait都会暂停当前的线程，对于CPU资源来说，不管是哪种方式暂停的线程，都表示它暂时不再需要CPU的执行时间。OS会将执行时间分配给其它线程。区别是，调用wait后，需要别的线程执行notify/notifyAll才能够重新获得CPU执行时间。

  线程的状态参考 Thread.State的定义。新创建的但是没有执行（还没有调用start())的线程处于“就绪”，或者说Thread.State.NEW状态。

  Thread.State.BLOCKED（阻塞）表示线程正在获取锁时，因为锁不能获取到而被迫暂停执行下面的指令，一直等到这个锁被别的线程释放。BLOCKED状态下线程，OS调度机制需要决定下一个能够获取锁的线程是哪个，这种情况下，就是产生锁的争用，无论如何这都是很耗时的操作。

  wait 使用notify 和 notifyAll 或者指定睡眠时间来唤醒当前线程池中等待的线程。

  **最大区别是 sleep 睡眠时仍然保持对象锁，占用该对象锁，而 wait 则会在睡眠时候释放该对象锁、**

* 进程和线程

  进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位.线程是进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源.一个线程可以创建和撤销另一个线程;同一个进程中的多个线程之间可以并发执行.相对进程而言，线程是一个更加接近于执行体的概念，它可以与同进程中的其他线程共享数据，但拥有自己的栈空间，拥有独立的执行序列。

  在串行程序基础上引入线程和进程是为了提高程序的并发度，从而提高程序运行效率和响应时间。

  **进程和线程**的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。**线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间**，**一个线程死掉就等于整个进程死掉**，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

  **1) 简而言之,一个程序至少有一个进程,一个进程至少有一个线程.**

  2) 线程的划分尺度小于进程，使得多线程程序的并发性高。

  3) 另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

  4) 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。**但是线程不能够独立执行，**必须依存在应用程序中，由应用程序提供多个线程执行控制。

  5) 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

* TCP 的流量控制和拥塞控制

  * 利用滑动窗口实现流量控制
  * 慢开始( slow-start )、拥塞避免( congestion avoidance )、快重传( fast retransmit )和快恢复( fast recovery )。


* Bitmap 的压缩和缓存

  通过设置 BitmapFactory.options 中 inSampleSize 的值就可以实现，也就是说采样率。提供内存缓存让程序快速处理和加载图片。其中最核心的类是LruCache (此类在android-support-v4的包中提供) 。这个类非常适合用来缓存图片，它的主要算法原理是把最近使用的对象用强引用存储在 LinkedHashMap 中，并且把最近最少使用的对象在缓存值达到预设定值之前从内存中移除。

  ​

* http 与 http2 之间的区别

  http是HTTP协议运行在TCP之上。所有传输的内容都是明文，客户端和服务器端都无法验证对方的身份。

  https是HTTP运行在SSL/TLS之上，SSL/TLS运行在TCP之上。所有传输的内容都经过加密，加密采用对称加密，但对称加密的密钥用服务器方的证书进行了非对称加密。此外客户端可以验证服务器端的身份，如果配置了客户端验证，服务器方也可以验证客户端的身份。前者默认工作在 80 端口，后者默认工作在 443 端口。

* HTTP2.0 与 HTTP1.1 之间的区别

  * HTTP2 采用二进制格式而非文本格式传输
  * HTTP2 采用二进制格式而非文本格式传输
  * HTTP2 使用报头压缩，降低了开销
  * HTTP2 可以主动将服务器的响应推送到客户端

* Sqlite 如何优化

  SQLite的数据库本质文件读写操作，频繁操作打开和关闭是很耗时和浪费资源的。

  * 建立索引,这种情况下针对数据量较少的情况下
  * 编译 sql 语句，用指定符号来替代查询语句
  * 及时关闭游标
  * 耗时异步化，耗时操作放在线程中执行
  * 按需要获取数据信息

  ​
  ​