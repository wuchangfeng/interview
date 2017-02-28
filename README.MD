### 算法

- 两个单向链表，从高位到低位排序，（比如链表5—>4—>3—>2—>1表示数字54321），要求两个链表相加输出一个新的链表，同样是由高位排序（主要要考虑一个位数太多的问题，我的解法是反转链表从低位开始加，超过两位数就进位）。

  两个单链表，每个节点存储一个0-9的数字，那么一个单链表就表示一个大数。 从高位到低位存，即表头对应的是这个大数的最高位。两个链表的长度相等， 我们要返回一个新的单链表，是这两个输入链表代表的数的和。我们不能使用递归， 不能使用额外的存储空间，即空间复杂度是O(1)。只遍历输入链表一次， 输出链表也是单链表(没有前向指针)。

  http://www.hawstein.com/posts/add-singly-linked-list.html

- 链表的反转。

- 二分查找算法。

- 两个数组，都是有序数组，要求输出它们的并集

  这题注意是两个有序数组，如果不是有序的还要先对其排序。有序的话就好办，联想到合并两个有序数组的知识，上下两个数组的指针同时移动，谁的数值大加入新的数组，直到其中一个结束，再把另一个数组剩余的数加入到新的数组中(有序的，所以可以放心往后面加）

* 各种排序算法 

  * 归并排序[联想到合并两个有序的数组] http://flyingcat2013.blog.51cto.com/7061638/1281026

    ``` java
    // 归并排序
    public static void mergeSort(int[] arr){
            // 创建一个临时数组，用来存储合并之后的数组
            int[] temp =new int[arr.length];
            internalMergeSort(arr, temp, 0, arr.length-1);
    }

    private static void internalMergeSort(int[] a, int[] b, int left, int right){
            // 当left==right的时，已经不需要再划分了
            if (left<right){
                int middle = (left+right)/2;
                internalMergeSort(a, b, left, middle);          //左子数组
                internalMergeSort(a, b, middle+1, right);       //右子数组
                mergeSortedArray(a, b, left, middle, right);    //合并两个子数组
            }
        }

    // 合并两个有序子序列 arr[left, ..., middle] 和 arr[middle+1, ..., right]。temp是辅助数组。
    private static void mergeSortedArray(int arr[], int temp[], int left, int middle, int right){
            int i=left;     
            int j=middle+1;
            int k=0;
      // 这里的核心就是合并两个有序数组，谁大放进新的数组
            while ( i<=middle && j<=right){
                if (arr[i] <=arr[j]){
                    temp[k++] = arr[i++];
                }
                else{
                    temp[k++] = arr[j++];
                }
            }
      // 左边的还有剩余
            while (i <=middle){
                temp[k++] = arr[i++];
            }
      // 右边的还有剩余
            while ( j<=right){
                temp[k++] = arr[j++];
            }
      // 把数据复制回原数组
            for (i=0; i<k; ++i){
                arr[left+i] = temp[i];
            }
        }
    ```

  * **希尔排序又称缩小增量排序**

    ``` java
    public static void shellSort(int[] arr){
        int temp;
        for (int delta = arr.length/2; delta>=1; delta/=2){                              //对每个增量进行一次排序
            for (int i=delta; i<arr.length; i++){             
                for (int j=i; j>=delta && arr[j]<arr[j-delta]; j-=delta){ //注意每个地方增量和差值都是delta
                    temp = arr[j-delta];
                    arr[j-delta] = arr[j];
                    arr[j] = temp;
                }
            }//loop i
        }//loop delta
    }
    ```

  * 堆排序 http://flyingcat2013.blog.51cto.com/7061638/1283090

  * 选择排序

    设现在要给数组arr[]排序，它有n个元素。

    1.对第一个元素（Java中，下标为0）和第二个元素进行比较，如果前者大于后者，那么它一定不是最小的，**但是我们并不像冒泡排序一样急着交换**。我们可以设置一个临时变量a，存储这个目前最小的元素的下标。然后我们把这个目前最小的元素继续和第三个元素做比较，如果它仍不是最小的，那么，我们再修改a的值。如此直到和最后一个元素比较完，可以肯定a存储的一定是最小的元素的下标。

    2.如果a的值不为0（初始值，即第一个元素的下标），交换下标为a和0的两个元素。

    3.重复上述过程，这次从下标为1的元素开始比较，因为下标为0的位置已经放好了最小的元素了。

    4.如此直到只剩下最后一个元素，可以肯定这个元素就是最大的了。

    5.排序完成。

    ```java
    public static void selectionSort(int[] arr) {
            int temp, min = 0;
            for (int index = 0; index < arr.length - 1; ++index) {
                min = index;
                // 循环查找最小值
                for (int j = index + 1; j < arr.length; ++j) {
                   // 如果比当前的小，就交换，不是的话就算了，继续
                    if (arr[min] > arr[j]) {
                        min = j;
                    }
                }
                // 把最小的与 index 位置元素交换
                if (min != index) {
                    temp = arr[index];
                    arr[index] = arr[min];
                    arr[min] = temp;
                }
            }
        }
    ```

  * 冒泡排序 http://flyingcat2013.blog.51cto.com/7061638/1279334

    设现在要给数组arr[]排序，它有n个元素。

    1.如果n=1：显然不用排了。

    2.如果n>1：

    1)我们从第一个元素开始，把每两个相邻元素进行比较，如果前面的元素比后面的大，那么在最后的结果里面前者肯定排在后面。所以，我们把这两个元素交换。然后进行下两个相邻的元素的比较。如此直到最后一对元素比较完毕，则第一轮排序完成。可以肯定，最后一个元素一定是数组中最大的（因为每次都把相对大的放到后面了）。

    2)重复上述过程，这次我们无需考虑最后一个，因为它已经排好了。

    3)如此直到只剩一个元素，这个元素一定是最小的，那么我们的排序可以结束了。显然，进行了n-1次排序。

    上述过程中，每次（或者叫做“轮”）排序都会有一个数从某个位置慢慢“浮动”到最终的位置（画个示意图，把数组画成竖直的就可以看出来），就像冒泡一样，所以，它被称为“冒泡排序法”。

    ``` java
    public static void bubbleSort(int[] arr) {
        int temp = 0;
        for (int i = arr.length - 1; i > 0; --i) { // 每次需要排序的长度
            for (int j = 0; j < i; ++j) { // 从第一个元素到第i个元素
                if (arr[j] > arr[j + 1]) {
                    temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }//loop j
        }//loop i
    }// method bubbleSort
    ```

  * **快速排序**

    ``` java
    public static void quickSort(int[] arr){
        qsort(arr, 0, arr.length-1);
    }
    private static void qsort(int[] arr, int low, int high){
        if (low < high){
            int pivot=partition(arr, low, high);        //将数组分为两部分
            qsort(arr, low, pivot-1);                   //递归排序左子数组
            qsort(arr, pivot+1, high);                  //递归排序右子数组
        }
    }
    private static int partition(int[] arr, int low, int high){
        int pivot = arr[low];     //枢轴记录
        while (low<high){
            while (low<high && arr[high]>=pivot) --high;
            arr[low]=arr[high];             //交换比枢轴小的记录到左端
            while (low<high && arr[low]<=pivot) ++low;
            arr[high] = arr[low];           //交换比枢轴小的记录到右端
        }
        //扫描完成，枢轴到位,注意这时候 low 已经前进到中间的位置了
        arr[low] = pivot;
        //返回的是枢轴的位置
        return low;
    }
    ```

  * 插入排序

    1.把待排序的数组分成已排序和未排序两部分，初始的时候把第一个元素认为是已排好序的。

    2.从第二个元素开始，在已排好序的子数组中寻找到该元素合适的位置并插入该位置。

    3.重复上述过程直到最后一个元素被插入有序子数组中。

    4.排序完成。

    ``` java
    public static void insertionSort(int[] arr){
      // 注意 i = 1 开始排序的，即从第二个元素开始
        for (int i=1; i<arr.length; ++i){
           // 每次拿的未排序的数组中的一个元素，来与前面的比较
            int value = arr[i];
            int position;
            // 依次与前面的每个元素进行比较
            for (position=i; position>0 ; --position){
              	// 如果比前面的元素小，前面的元素就自动向后面退一位
                if (arr[position-1] > value){
                    arr[position]=arr[position-1];
                }
                // 如果比前面的元素大，前面的元素不用动，这次循环结束
                else{
                    break;
                }
            }
            // 将未排序的元素插入到指定的位置
            arr[position] = value;
        }
    }
    ```



### Java 相关

- final 关键字作用

  1. final 关键字提高了性能。JVM 和 Java 应用都会缓存 final 变量。
  2. final 变量可以安全的在多线程环境下进行共享，而不需要额外的同步开销。
  3. 使用 final 关键字，JVM 会对方法、变量及类进行优化。

- string 类是不可变的

  1. **只有当字符串是不可变的，字符串池才有可能实现**。字符串池的实现可以在运行时节约很多heap空间，因为不同的字符串变量都指向池中的同一个字符串。但如果字符串是可变的，那么String interning将不能实现(译者注：[String interning](http://en.wikipedia.org/wiki/String_interning)是指对不同的字符串仅仅只保存一个，即不会保存多个相同的字符串。)，因为这样的话，如果变量改变了它的值，那么其它指向这个值的变量的值也会一起改变。
  2. **如果字符串是可变的，那么会引起很严重的安全问题**。譬如，数据库的用户名、密码都是以字符串的形式传入来获得数据库的连接，或者在socket编程中，主机名和端口都是以字符串的形式传入。因为字符串是不可变的，所以它的值是不可改变的，否则黑客们可以钻到空子，改变字符串指向的对象的值，造成安全漏洞。
  3. **因为字符串是不可变的，所以是多线程安全的**，同一个字符串实例可以被多个线程共享。这样便不用因为线程安全问题而使用同步。字符串自己便是线程安全的。
  4. 类加载器要用到字符串，不可变性提供了安全性，以便正确的类被加载。譬如你想加载java.sql.Connection类，而这个值被改成了myhacked.Connection，那么会对你的数据库造成不可知的破坏。
  5. **因为字符串是不可变的，所以在它创建的时候hashcode就被缓存了，不需要重新计算。这就使得字符串很适合作为Map中的键**，字符串的处理速度要快过其它的键对象。这就是HashMap中的键往往都使用字符串。

- Java 中内部类为什么可以访问外部类。主要 java 的内部类持有外部类的引用。

  1 编译器自动为内部类添加一个成员变量， 这个成员变量的类型和外部类的类型相同， 这个成员变量就是指向外部类对象的引用；

  2 编译器自动为内部类的构造方法添加一个参数， 参数的类型是外部类的类型， 在构造方法内部使用这个参数为1中添加的成员变量赋值；

  3 在调用内部类的构造函数初始化内部类对象时， 会默认传入外部类的引用。

- 100 万个数据找出 100 个最大的

  先把100w个数分成100份，每份1w个数。先分别找出每1w个数里面的最大的数，然后比较。找出100个最大的数中的最大的数和最小的数，**取最大数的这组的第二大的数，与最小的数比较**。。。。


- 什么是volatile？

​      关键字volatile是Java虚拟机提供的最轻量级的同步机制。当一个变量被定义成volatile之后，
​      具备两种特性：

​	1:保证此变量对所有线程的可见性。当一条线程修改了这个变量的值，新值对于其他线程是可以立即得知的。		    而普通变量做不到这一点。

​	2: 禁止指令重排序优化。普通变量仅仅能保证在该方法执行过程中，得到正确结果，但是不保证程序代码的执行顺序。

- 为什么基于volatile变量的运算在并发下不一定是安全的？

​       volatile变量在各个线程的工作内存，不存在一致性问题（各个线程的工作内存中volatile变
​      量，每次使用前都要刷新到主内存）。但是Java里面的运算并非原子操作，导致volatile变量
​       的运算在并发下一样是不安全的。

- 为什么使用volatile？

​      在某些情况下，volatile同步机制的性能要优于锁（synchronized关键字），但是由于虚拟机
​      对锁实行的许多消除和优化，所以并不是很快。
​      volatile变量读操作的性能消耗与普通变量几乎没有差别，但是写操作则可能慢一些，因为它
​     需要在本地代码中插入许多内存屏障指令来保证处理器不发生乱序执行。 

-   ConcurrentHashMap

    是由Segment数组结构和HashEntry数组结构组成。**Segment是一种可重入锁ReentrantLock**，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值对数据。一个ConcurrentHashMap里包含一个Segment数组，Segment的结构和HashMap类似，是一种数组和链表结构， 一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素， 每个Segment守护者一个HashEntry数组里的元素,当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。

-   Java 注解的作用

      a. 标记，用于告诉编译器一些信息b. 编译时动态处理，如动态生成代码 c. 运行时动态处理，如得到注解信息
       标准 Annotation 元 Annotation 自定义 Annotation

-   **有了解什么是IntentService么？**

      intentService 是继承自 Service 并处理异步请求的一个类，在 IntentService 内有一个工作线程来处理耗时操作，当任务执行完后，IntentService 会自动停止，不需要我们去手动结束。如果启动 IntentService 多次，那么每一个耗时操作会以工作队列的方式在 IntentService 的 onHandleIntent 回调方法中执行，依次去执行，执行完自动结束。

-   缓存算法的种类

    - （1）LRU即Least RecentlyUsed，近期最少使用算法。

    也就是当内存缓存达到设定的最大值时将内存缓存中近期最少使用的对象移除，有效的避免了OOM的出现。

    - （2）Least Frequently Used（LFU）

    对每个缓存对象计算他们被使用的频率。把最不常用的缓存对象换走。

    - （3）First in First out（FIFO）

    这是一个低负载的算法，并且对缓存对象的管理要求不高。通过一个队列去跟踪所有的缓存对象，最近最常用的缓存对象放在后面，而更早的缓存对象放在前面，当缓存容量满时，排在前面的缓存对象会被踢走，然后把新的缓存对象加进去。

-   Java 如何进行 GC ，判断对象回收的条件是什么  http://www.jianshu.com/p/424e12b3a08f

                JVM进行次GC的频率很高,但因为这种GC占用时间极短,所以对系统产生的影响不大。更值得关注的是主GC的触发条件,因为它对系统影响很明显。总的来说,有两个条件会触发主GC:

                　　1：当应用程序空闲时,即没有应用线程在运行时,GC会被调用。因为GC在优先级最低的线程中进行,所以当应用忙时,GC线程就不会被调用,但以下条件除外。

                　　2：Java堆内存不足时,GC会被调用。当应用线程在运行,并在运行过程中创建新对象,若这时内存空间不足,JVM就会强制地调用GC线程,以便回收内存用于新的分配。若GC一次之后仍不能满足内存分配的要求,JVM会再进行两次GC作进一步的尝试,若仍无法满足要求,则 JVM将报“out of memory”的错误,Java应用将停止。

                由于是否进行主GC由JVM根据系统环境决定,而系统环境在不断的变化当中,所以主GC的运行具有不确定性,无法预计它何时必然出现,但可以确定的是对一个长期运行的应用来说,其主GC是反复进行的。

-   垃圾对象的判定即什么时候进行回收？


    - 引用计数分析算法
    - 可达性算法
    - 对象标记的死亡过程
    - 正确理解引用

-   如何进行垃圾的回收？

    - 标记-清除算法[先标记出所有要回收的对象，标记完成之后统一回收]

      * 标记和清除过程效率都不高
      * 标记清除之后容易产生空间碎片

    - 复制算法[将可用的内存按容量分为大小相等的两块，每次只使用其中的一块。当这一块用完了

      就将存活着的对象复制到另一块上面，然后再把已经使用过的内存一次清理掉]

      * 简单，不用考虑内存碎片
      * 内存缩小为原来的一半，在对象存活率较高时候，需要多次复制，导致效率不高

    - 标记-整理算法[让所有的存活对象向一端移动，然后直接清理掉边界以外的内存]

    - 分代收集算法[将 java 堆分为新生代和老年带，根据各个年代的特点进行设计合适的算法]


-   java 的类加载机制

      class 文件描述的各种信息，都要加载到虚拟机之后才能运行，虚拟机把描述类的数据从 class 文件加载

      到内存，并对数据进行校验，转换解析和初始化，最终形成可以被虚拟机直接使用的 java leixing

      这就是类加载机制。

      双亲委派模型（Parents Delegation Model）要求除了顶层的启动类加载器外，其余加载器
      都应当有自己的父类加载器。**类加载器之间的父子关系，通过组合关系复用。**
      工作过程：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是
      把这个请求委派给父类加载器完成。每个层次的类加载器都是如此，因此所有的加载请求最终
      都应该传送到顶层的启动类加载器中，只有到父加载器反馈自己无法完成这个加载请求（它的
      搜索范围没有找到所需的类）时，子加载器才会尝试自己去加载。 

      Java类随着它的类加载器一起具备了一种带优先级的层次关系。比如java.lang.Object，它存
      放在rt.jar中，无论哪个类加载器要加载这个类，最终都是委派给启动类加载器进行加载，因此
      Object类在程序的各个类加载器环境中，都是同一个类。
      如果没有使用双亲委派模型，让各个类加载器自己去加载，那么Java类型体系中最基础的行为
      也得不到保障，应用程序会变得一片混乱。

-   运行时的数据区域包括哪些

    * 程序计数器(线程私有)
    * Java 虚拟机栈(线程私有)
    * 本地方法栈(线程私有)
    * java 堆(线程共享)
    * 方法区(线程共享)
    * 运行时常量池

-   Java 的内存模型

      ​

-   内存泄露的原因是什么？内部类为什么会持有外部类的引用

      内部类在构建时候，产生了 外部类名.this 的对象，从而导致依赖外部类。下面的实例是静态内部类和弱引用解决 handler 引起的内存泄漏。


    ​```java
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
    ​```

-   **如何实现一个线程池（线程池原理），优点是什么？**

    1. 线程池管理器（ThreadPoolManager）:用于创建并管理线程池
    2. 工作线程（WorkThread）: 线程池中线程
    3. 任务接口（Task）:每个任务必须实现的接口，以供工作线程调度任务的执行。
    4. 任务队列:用于存放没有处理的任务。提供一种缓冲机制。


-   作用在静态方法上的同步锁和作用在非静态方法上的同步锁有什么区别

    **所有的非静态同步方法用的都是同一把锁——实例对象本身**，也就是说如果一个实例对象的非静态同步方法获取锁后，该实例对象的其他非静态同步方法必须等待获取锁的方法释放锁后才能获取锁，可是别的实例对象的非静态同步方法因为跟该实例对象的非静态同步方法用的是不同的锁，所以毋须等待该实例对象已获取锁的非静态同步方法释放锁就可以获取他们自己的锁。

    **而所有的静态同步方法用的也是同一把锁——类对象本身**，这两把锁是两个不同的对象，所以静态同步方法与非静态同步方法之间是不会有竞态条件的。但是一旦一个静态同步方法获取锁后，其他的静态同步方法都必须等待该方法释放锁后才能获取锁，而不管是同一个实例对象的静态同步方法之间，还是不同的实例对象的静态同步方法之间，只要它们同一个类的实例对象！

-   七种单列模式 http://wuchong.me/blog/2014/08/28/how-to-correctly-write-singleton-pattern/

      一般来说，单例模式有五种写法：懒汉、饿汉、双重检验锁、静态内部类、枚举。下面这种是静态内部类机制。这种写法仍然使用JVM本身机制**保证了线程安全问题**；由于 SingletonHolder 是私有的，除了 getInstance() 之外没有办法访问它，**因此它是懒汉式的**；同时读取实例的时候不会进行同步，没有性能缺陷；也不依赖 JDK 版本。


```java
public class Singleton {  
        private static class SingletonHolder {  
            private static final Singleton INSTANCE = new Singleton();  
        }  
        private Singleton (){}  
        public static final Singleton getInstance() {  
            return SingletonHolder.INSTANCE; 
        }  
    }
```

​     下面是双重锁，注意 volatile 关键字

``` java
public class Singleton {
    private volatile static Singleton instance; //声明成 volatile
    private Singleton (){}

    public static Singleton getSingleton() {
        if (instance == null) {                         
            synchronized (Singleton.class) {
                if (instance == null) {       
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

-   Error

    `Error`是`Throwable`的子类，**用于指示合理的应用程序不应该试图捕获的严重问题**。大多数这样的错误都是异常条件。和`RuntimeException`一样， 编译器也不会检查`Error`。当资源不足、约束失败、或是其它程序无法继续运行的条件发生时，就产生错误，程序本身无法修复这些错误的。 　

-   Exception

      `Exception`类及其子类是`Throwable`的一种形式，它**指出了合理的应用程序想要捕获的条件**。对于可以恢复的条件使用被检查异常（Exception的子类中除了`RuntimeException`之外的其它子类），对于程序错误使用运行时异常。

-   static 关键字

      在程序中任何变量或者代码都是在编译时由系统自动分配内存来存储的。`static`修饰符表示静态的，在类加载时`JVM`**会把它放到方法区，被本类以及本类的所有实例所共用**。在编译后所分配的内存会一直存在，直到程序退出内存才会释放这个空间。如果一个被所有实例共用的方法被申明为`static`，那么就可以节省空间，不用每个实例初始化的时候都被分配到内存。我们比较常见的`static`修饰是在静态变量和静态方法中。它们可以直接通过类名来访问。

-   wait() 和sleep() 之间的区别





-   Java 中的几种引用类型： 强引用，软引用，弱引用，虚引用

     **软引用**用来描述一些**还有用但是并非必须的对象**，在Java中用java.lang.ref.SoftReference类来表示。对于软引用关联着的对象，只有在内存不足的时候JVM才会回收该对象。因此，这一点可以很好地用来解决OOM的问题，并且这个特性很适合用来实现缓存：比如网页缓存、图片缓存等。

    弱引用**与软引用的区别在于**：只具有弱引用的对象拥有更短暂的生命周期**。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象。

### 数据结构相关

- ArrayList 的实现原理 与 LinkedList 的区别

  ArrayList 内部实现机制就是数组，其各种操作就是对数组的操作。特点就是动态扩容，当内部机制长度已经不够用的时候，就会自动扩容为原来的 1.5 倍。

  第一**：在容量进行扩展的时候，其实例如整除运算将容量扩展为原来的1.5倍加1，而jdk1.7是利用位运算，从效率上，jdk1.7就要快于jdk1.6。**

  第二：在算出newCapacity时，其没有和ArrayList所定义的MAX_ARRAY_SIZE作比较，为什么没有进行比较呢，原因是jdk1.6没有定义这个MAX_ARRAY_SIZE最大容量，也就是说，其没有最大容量限制的，但是jdk1.7做了一个改进，进行了容量限制。

   1.ArrayList是实现了基于**动态数组**的数据结构，LinkedList基于**链表**的数据结构。 
   2.对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要**移动指针**，而数组只用通过下标		 访问即可。 
   3.对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据，同理链表与数组的区别。

- HashMap 的实现原理，特点等

  HashMap和Hashtable都实现了Map接口，但决定用哪一个之前先要弄清楚它们之间的分别。主要的区别有：线程安全性，同步(synchronization)，以及速度。

  1. HashMap几乎可以等价于Hashtable，除了HashMap是非synchronized的，并可以接受null(HashMap**可以接受为null的键值(key)和值(value)，而Hashtable则不行**)。
  2. HashMap是非synchronized，而Hashtable是synchronized，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable；而如果没有正确的同步的话，多个线程是不能共享HashMap的。Java 5提供了ConcurrentHashMap，它是HashTable的替代，比HashTable的扩展性更好。
  3. 另一个区别是HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM。这条同样也是Enumeration和Iterator的区别。
  4. 由于Hashtable是线程安全的也是synchronized，所以在单线程环境下它比HashMap要慢。如果你不需要同步，只需要单一线程，那么使用HashMap性能要好过Hashtable。
  5. HashMap不能保证随着时间的推移Map中的元素次序是不变的。

  1) sychronized意味着在一次仅有一个线程能够更改Hashtable。就是说任何线程要更新Hashtable时要首先获得同步锁，其它线程要等到同步锁被释放之后才能再次获得同步锁更新Hashtable。

  2) Fail-safe和iterator迭代器相关。如果某个集合对象创建了Iterator或者ListIterator，然后其它的线程试图“结构上”更改集合对象，将会抛出ConcurrentModificationException异常。但其它线程可以通过set()方法更改集合对象是允许的，因为这并没有从“结构上”更改集合。但是假如已经从结构上进行了更改，再调用set()方法，将会抛出IllegalArgumentException异常。

  3) 结构上的更改指的是删除或者插入一个元素，这样会影响到map的结构。

  HashMap可以通过下面的语句进行同步：
  Map m = Collections.synchronizeMap(hashMap);

- 重写对象的 `hashCode()`后还应该重写什么方法？为什么？http://blog.csdn.net/gao_chun/article/details/44834691

  JAVA中重写equals()方法为什么要重写hashcode()方法?

  > 关于hashCode的作用  　　总的来说，Java中的集合（Collection）有两类，一类是List，再有一类是Set。前者集合内的元素是有序的，元素可以重复；后者元素无序，但元素不可重复。         要想保证元素不重复，可两个元素是否重复应该依据什么来判断呢？这就是Object.equals方法了。但是，如果每增加一个元素就检查一 次，那么当元素很多时，后添加到集合中的元素比较的次数就非常多了。也就是说，如果集合中现在已经有1000个元素，那么第1001个元素加入集合时，它 就要调用1000次equals方法。这显然会大大降低效率。         于是，Java采用了哈希表的原理。哈希算法也称为散列算法，是将数据依特定算法直接指定到一个地址上。这样一来，当集合要添加新的元素时，先调用这个元素的hashCode方法，就一下子能定位到它应该放置的物理位置上。如果这个位置上没有元素，它就可以 直接存储在这个位置上，不用再进行任何比较了；如果这个位置上已经有元素了，就调用它的equals方法与新元素进行比较，相同的话就不存了；不相同，也就是发生了Hash key相同导致冲突的情况,那么就在这个Hash key的地方产生一个链表,将所有产生相同hashcode的对象放到这个单链表上去,串在一起。所以这里存在一个冲突解决的问题（很少出现）。这样一来实际调用equals方法的次数就大大降低了，几乎只需要一两次。         **所以，Java对于eqauls方法和hashCode方法是这样规定的：             1、如果两个对象相等，那么它们的hashCode值一定要相等；             2、如果两个对象的hashCode相等，它们并不一定相等(在同一个链表上)。** 

  在上述第二种情况下 ，**若要判断两个对象是否相等，就要去重写 equals() 方法了**。简单来讲 hashCode() 是用来查找用的，equals() 是用来判断两个对象是否相等用的。

#### Android 相关

* InstantRun 原理
  * hotSwap 最快的，应用不需要重启或者退出，甚至连 activtiy 都不需要重启
  * wramSwap 重启 activity，可以看到屏幕会很快的重新闪动一下
  * coldSwap 重启应用 这种是最慢的。cold swap相对而言就要更慢一些了，Android Studio会自动记录我们项目的每次修改，然后将修改的这部分内容打成一个dex文件发送到手机上，尽管这种swap类型仍然不需要去安装一个全新的APK，但是为了加载这个新的dex文件，整个应用程序必须进行重启才行。另外，cold swap的工作原理是基于multidex机制来实现的，在不引入外部library的情况下，只有5.0及以上的设备才支持multidex，因此，如果你使用了5.0以下的设备，那么cold swap就无法工作了，这种情况会执行最原始的完整APK安装过程。


* 说说Service的生命周期？

  > 启动Service的方式有两种，各自的生命周期也有所不同。
  > 一、通过startService启动Service：onCreate、onStartCommand、onDestory。
  > 二、通过bindService绑定Service：onCreate、onBind、onUnbind、onDestory。


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

  线程局部变量并不是Java的新发明，很多语言（如IBM IBM XL FORTRAN）在语法层面就提供线程局部变量。在Java中没有提供在语言级支持，而是变相地通过ThreadLocal的类提供支持。

  所以，在Java中编写线程局部变量的代码相对来说要笨拙一些，因此造成线程局部变量没有在Java开发者中得到很好的普及。

* 在Android开发中Serializable和Parcelable的用法

  我们推荐使用Parcelable，理由大致有3个：

  1. **Parcelable是Android 框架提供给我使用的**，Google提供了比较好的接口和文档支持，例如上面的putExtra，就有对Parcelable数组的重载方法。
  2. **Parcelable效率更高，Parcelable底层实现是内存的copy，速度很快，Serializable是IO操作，而且会用到反射，相对比较慢，国外有人测试过，Parcelable比Serializable从序列化到传输到反序列化，平均要快10倍左右。**
  3. 最后一个原因也是最重要的原因，Parcelable要序列化哪些字段，我们完全可以控制，而且还可以在其中加入各种转换，修饰，因为写接口暴露给我们了，我们可以自由定制，而Serializable就显的比较笨拙，而且需要一些额外的字节来存储类的信息，当然Serializable使用起来要更简单。

* socket 通信机制

  socket 是一套完成 TCP/UDP 通信协议的接口，而 http 都是基于 socket。HTTP是轿车，提供了封装或者显示数据的具体形式；Socket是发动机，提供了网络通信的能力。

  建立Socket连接至少需要一对套接字，其中一个运行于客户端，称为ClientSocket ，另一个运行于服务器端，称为ServerSocket 。
  套接字之间的连接过程分为三个步骤：服务器监听，客户端请求，连接确认。
  1。服务器监听：服务器端套接字并不定位具体的客户端套接字，而是处于等待连接的状态，实时监控网络状态，等待客户端的连接请求。
  2。客户端请求：指客户端的套接字提出连接请求，要连接的目标是服务器端的套接字。为此，客户端的套接字必须首先描述它要连接的服务器的套接字，指出服务器端套接字的地址和端口号，然后就向服务器端套接字提出连接请求。
  3。连接确认：当服务器端套接字监听到或者说接收到客户端套接字的连接请求时，就响应客户端套接字的请求，建立一个新的线程，把服务器端套接字的描述发给客户端，一旦客户端确认了此描述，双方就正式建立连接。而服务器端套接字继续处于监听状态，继续接收其他客户端套接字的连接请求。

* Activity 和 Fragment 的生命周期

  - Activity——onCreate->onStart->onResume->onPause->onStop->onDestroy
  - Fragment——onAttach->onCreate->onCreateView->onActivityCreated->onStart->onResume->onPause->onStop->onDestroyView->onDestroy->onDetach

- 经典的一个`Activity A`启动一个`Activity B`，他们的生命周期

  **BACK键：**当我们按BACK键时，我们这个应用程序将结束，这时候我们将先后调用onPause()->onStop()->onDestory()三个方法。再次启动App时，会执行onCreate()->onStart()->onResume()

  **HOME键:**当我们打开应用程序时，比如浏览器，我正在浏览NBA新闻，看到一半时，我突然想听歌，这时候我们会选择按HOME键，然后去打开音乐应用程序，而当我们按HOME的时候，Activity先后执行了onPause()->onStop()这两个方法，**这时候应用程序并没有销毁**。**不可见时候，都执行了 onStop() 这个方法**。

  而当我们从桌面再次启动应用程序时，则先后分别执行了**onRestart()**->onStart()->onResume()三个方法。

  补充一点，当前Activity产生事件弹出Toast和AlertDialog的时候Activity的生命周期不会有改变

  Activity运行时按下HOME键(跟被完全覆盖是一样的)：onSaveInstanceState --> onPause --> onStop，再次进入激活状态时： onRestart -->onStart--->onResume

- startActivityForResult是哪个类的方法

  1,startActivity(Intent intent);启动其他Activity

  2,startActivityForResult(Intent intent，int requestCode)：以指定指定的请求码（requestCode）启动Activity，并且程序将会等到新启动Activity的结果(通过重写onActivityResult()方法来获取)

- 启动模式，各自的特点，问了几个场景 http://blog.csdn.net/mynameishuangshuai/article/details/51491074

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

- Handler 的原理（可以扯深一点，对象池和消息插队等）

  为了不让主线程执行一些耗时的操作，利用 handler 通信机制解决线程之间的通信问题。Android 基本的 Handler 机制就是背后有一个 looper 、messagequenue以及 message。创建一个与当前线程相关的 looper 对象，去 mq 中取得 msg 对象，让 msg 的发送者去处理它。

- RecyclerView 和 ListView 各自有什么特点？

- ListView 优化

  1. 从文件系统中加载图片也没有内存中加载那么快，甚至可能内存中加载也不够快。因此在ListView中应设立busy标志位，当ListView滚动时busy设为true，停止各个view的图片加载。否则可能会让UI不够流畅用户体验度降低。
  2. 文件加载图片放在子线程实现，否则快速滑动屏幕会卡
  3. 开启网络访问等耗时操作需要开启新线程，应使用线程池避免资源浪费，最起码也要用AsyncTask。
  4. Bitmap从网络下载下来最好先放到文件系统中缓存。这样一是方便下一次加载根据本地uri直接找到，二是如果Bitmap过大，从本地缓存可以方便的使用Option.inSampleSize配合Bitmap.decodeFile(ui, options)或Bitmap.createScaledBitmap来进行内存压缩

- **android:layout_gravity 和 android:gravity 的区别**

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
  （HandlerThread 是一个Thread的 子类，HandlerThread 内部 有点线我们的UI线程，内部一个Looper loop循环一直轮询消息 获得消息 处理消息。）而IntentService, 内部有一个Handler子类 ServiceHandler，它的Looper用的是这个HandlerThread 的Looper,IntentSerivce 在onStart()通过发送Message,ServiceHandler在处理Message 调用的是 onHandleIntent。 **所以简单的说一个IntentService,内部就创建了一个线程，通过Android提供的 Handler Message Looper,这些消息处理的类 构成了一个消息处理的模型。所以IntentService 的onHandleIntent 这个方法其实是在IntentService 中开辟的一个子线程中处理的。**

- 广播分为几类？

  安卓广播分为两类:
  1.**普通广播**， broadcast,广播发出之后所有满足条件的应用都能获取到广播里面的数据，缺点是应用获取广播中的数据修改之后不能传递给其它接收广播的应用；
  2.**有序广播**，orderbroadcast,广播发出之后各应用根据应用的优先级依次接收广播，优先级高的应用接收广播之后修改的数据也可以传递给后来的接受者，优先级高的应用也可以调用abrotbroascast方法停止该广播的向下传播，优先级靠应用的android:prioioty属性控制，该值的取值区间为-1000到1000，值越大优先级越高。

- Intent 的匹配规则？action，category，data

  只有action、data、category三方都匹配，Intent才算是匹配成功，进而才能打开相应的Component。一个Component若声明了多个Intent Filter，只需要匹配任意一个即可启动该组件。

- 触摸事件分发机制，举了几个特定的例子

  https://gold.xitu.io/entry/589d11c4128fe1006cd94173

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

- Picasso,Glide,Fresco 各自有什么特点？比较一下

  * http://www.trinea.cn/android/android-image-cache-compare/

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

- tcp 和 udp 的区别，详细描述一下 tcp 的三次握手

  **TCP与UDP的区别** **TCP协议是一种可靠的、面向连接的协议，保证通信主机之间有可靠的字节流传输，完成流量控制功能，协调收发双方的发送与接收速度，达到正确传输的目的。** **UDP是一种不可靠、无连接的协议，其特点是协议简单、额外开销小、效率较高，但是不能保证传输是否正确。** UDP是面向无连接的、不可靠的数据报服务；TCP是面向连接的、可靠的字节流服务。

  首先Client端发送连接请求报文，Server端接收连接请求后回复ACK报文，并为这次连接分配资源。Client端接收到ACK报文后也向Server端发生ACK报文，并分配资源，这样TCP连接就建立了。

  **连接断开：四次握手** 假设Client端发起中断连接请求，也就是发送FIN报文。Server端接到FIN报文后，意思是说"我Client端没有数据要发给你了"，但是如果你还有数据没有发送完成，则不必急着关闭Socket，可以继续发送数据，所以你先发送ACK，"告诉Client端，你的请求我收到了，但是我还没准备好，请你继续等我的消息"。这个时候Client端就进入**FIN_WAIT**状态，继续等待Server端的FIN报文。当Server端确定数据已发送完成，则向Client端发送FIN报文，"告诉Client端，好了，我这边数据发完了，准备好关闭连接了"。Client端收到FIN报文后就知道可以关闭连接了，但是它还是不相信网络，怕Server端不知道要关闭，所以发送ACK后进入**TIME_WAIT**状态，如果Server端没有收到ACK则可以重传。Server端收到ACK后，就知道可以断开连接了。Client端等待了2MSL后依然没有收到回复，则证明Server端已正常关闭，那好，Client端也可以关闭连接了。TCP连接就这样关闭了！

- 进程和线程的关系

  操作系统的设计，因此可以归结为三点：

  （1）**以多进程形式，允许多个任务同时运行**；

  （2**）以多线程形式，允许单个任务分成不同的部分运行**；

  （3）提供协调机制，一方面防止进程之间和线程之间产生冲突，另一方面允许进程之间和线程之间共享资源。

  简而言之,一个程序至少有一个进程,一个进程至少有一个线程. 
  **线程的划分尺度小于进程，使得多线程程序的并发性高。**
  另外，**进程在执行过程中拥有独立的内存单元，而多个线程共享内存**，从而极大地提高了程序的运行效率。
  线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
  从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

  进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位.
  **线程是进程的一个实体,是CPU调度和分派的基本单位**,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源.
  一个线程可以创建和撤销另一个线程;同一个进程中的多个线程之间可以并发执行.

  **进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程**


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

- 如何保证线程同步的安全性

  问：synchronized即可修饰非静态方式，也可修饰静态方法，还可修饰代码块，有何区别
  答：synchronized修饰非静态同步方法时，锁住的是当前实例；synchronized修饰静态同步方法时，锁住的是该类的Class对象；synchronized修饰静态代码块时，锁住的是`synchronized`关键字后面括号内的对象。

  问：既然锁和synchronized即可保证原子性也可保证可见性，为何还需要volatile？
  答：synchronized和锁需要通过操作系统来仲裁谁获得锁，开销比较高，而volatile开销小很多。因此在只需要保证可见性的条件下，使用volatile的性能要比使用锁和synchronized高得多。

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

- SparseArray 的特点

  SparseArray只能存储key为int类型的数据，同时，SparseArray在存储和读取数据时候，使用的是二分查找法

  SparseArray比HashMap更省内存，在某些条件下性能更好，主要是因为它避免了对key的自动装箱（int转为Integer类型），它内部则是通过两个数组来进行数据存储的，一个存储key，另外一个存储value，为了优化性能，它内部对数据还采取了压缩的方式来表示稀疏数组的数据，从而节约内存空间

- ArrayMap 的特点

  这个api的资料在网上可以说几乎没有，然并卵，只能看文档了 
  ArrayMap是一个<**key,value**>映射的[数据结构](http://lib.csdn.net/base/datastructure)，它设计上更多的是考虑内存的优化，内部是使用两个数组进行数据存储，一个数组记录key的hash值，另外一个数组记录Value值，它和SparseArray一样，也会对key使用二分法进行从小到大排序，在添加、删除、查找数据的时候都是先使用二分查找法得到相应的index，然后通过index来进行添加、查找、删除等操作，所以，应用场景和SparseArray的一样，如果在数据量比较大的情况下，那么它的性能将退化至少50%。


-    Java 静态方法可以被重写吗？

     如果从重写方法会有什么特点来看，我们是不能重写静态方法的。虽然就算你重写静态方法，编译器也不会报错。也就是说，如果你试图重写静态方法，Java不会阻止你这么做，但你却得不到预期的结果（重写仅对非静态方法有用）。重写指的是根据运行时对象的类型来决定调用哪个方法，而不是根据编译时的类型。让我们猜一猜为什么静态方法是比较特殊的？因为它们是类的方法，所以它们在编译阶段就使用编译出来的类型进行绑定了。使用对象引用来访问静态方法只是Java设计者给程序员的自由。我们应该直接使用类名来访问静态方法，而不要使用对象引用来访问。

-    如何解决 GC 循环引用带来的问题

        GC root，几乎所有的 jvm 都是采用引用遍历的方法来解决循环引用潜在的问题

-    TCP 如何保证传输可靠

        有超时重传机制，丢失重传机制

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

-    Java NIO  https://github.com/GeniusVJR/LearningNotes/blob/master/Part2/JavaConcurrent/NIO.md

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

        1、打包资源文件，生成R.java文件

        2、处理aidl文件，生成相应java 文件

        3、编译工程源代码，生成相应class 文件

        4、转换所有class文件，生成classes.dex文件

        5、打包生成apk

        6、对apk文件进行签名

        7、对签名后的apk文件进行对其处理

-    Java 中的异常

     - **检查性异常：**最具代表的检查性异常是用户错误或问题引起的异常，这是程序员无法预见的。例如要打开一个不存在文件时，一个异常就发生了，这些异常在编译时不能被简单地忽略。

     - **运行时异常：** 运行时异常是可能被程序员避免的异常。与检查性异常相反，运行时异常可以在编译时被忽略。

     - **错误：** 错误不是异常，而是脱离程序员控制的问题。错误在代码中通常被忽略。例如，当栈溢出时，一个错误就发生了，它们在编译也检查不到的。

     - finally 关键字用来创建在 try 代码块后面执行的代码块。

       无论是否发生异常，finally 代码块中的代码总会被执行。

       在 finally 代码块中，可以运行清理类型等收尾善后性质的语句。

     - **throws 用于抛出方法层次的异常**， 
       并且直接由些方法调用异常处理类来处理该异常， 
       所以它常用在方法的后面。比如 
       public static void main(String[] args) throws SQLException

       **throw 用于方法块里面的代码**，比throws的层次要低，比如try...catch ....语句块，表示它抛出异常， 
       但它不会处理它， 而是由方法块的throws Exception来调用异常处理类来处理。

       throw用在程序中，明确表示这里抛出一个异常。   
       throws用在方法声明的地方，表示这个方法可能会抛出某异常。

       throw是抛出一个具体的异常类，产生一个异常。
       throws则是在方法名后标出该方法会产生何种异常需要方法的使用者捕获并处理。

-    Butterknife 最新的实现原理，主要是利用 APT 在编译期生成相关代码  https://joyrun.github.io/2016/07/19/AptHelloWorld/

        APT(Annotation Processing Tool)是一种处理注释的工具,它对源代码文件进行检测找出其中的Annotation，使用Annotation进行额外的处理。
        Annotation处理器在处理Annotation时可以根据源文件中的Annotation生成额外的源文件和其它的文件(文件具体内容由Annotation处理器的编写者决定),APT还会编译生成的源文件和原来的源文件，将它们一起生成class文件。

-    otto 与 eventbus 出现的理由以及实现原理

        为了简化并且更加高质量的在Activities, Fragments, Threads, Services等等之间的通信，同时解决组建之间的高耦合还能继续高效的通信。事件总线设计出现了。
        总线，在计算机组成原理中遇到过io总线。总线的思路就是负责传递某种object到指定的地方。
        在Android内置的Intent和BroadcastReceiver就是采用了类似事件总线的设计思路。这两者都可以起到跟事件总线类似的效果。注册广播接收器和单纯发一个intent就可以唤起其他组件，提醒其他组件更新，这是非常方便的，同时也是本文提到的两个开源方案所做不到的。但也有不好地方，它们内部的实现都需要 IPC，单从传递效率上来讲，可能并不太适合上层的组件间通信。

-    ContentProvider 的相关说法

        **ContentProvider的作用** 1.封装数据：对数据进行封装，提供统一的接口 2.跨进程的数据共享

        **ContentProvider数据访问限制** android:exported 属性用于指示该服务是否能够被其他应用程序组件调用或跟它交互。如果设置为true，则能够被调用或交互。如果设置为false时，只有同一个应用程序的组件或带有相同用户ID的应用程序才能启动或绑定该服务。**对于需要开放的组件应设置合理的权限，如果只需要对同一个签名的其它应用开放content provider，则可以设置signature级别的权限。**

        **ContentProvider运行的进程和线程** ContentProvider可以在AndroidManifest.xml中配置一个叫做android:multiprocess 的属性，默认值是false，表示ContentProvider是单例的，无论哪个客户端应用的访问都将是同一个ContentProvider对象。如果设为true，系统会为每一个访问该ContentProvider的进程创建一个实例。

        问：每个ContentProvider的操作是在哪个线程中运行的呢（其实我们关心的是UI线程和工作线程）？比如我们在UI线程调用getContentResolver().query查询数据，而当数据量很大时（或者需要进行较长时间的计算）会不会阻塞UI线程呢？ 答：**ContentProvider和调用者在同一个进程，ContentProvider的方法（query/insert/update/delete等）和调用者在同一线程中；****ContentProvider和调用者在不同的进程，ContentProvider的方法会运行在它自身所在进程的一个Binder线程中。**

        问：ContentProvider是如何在不同应用程序之间传输数据的？ 答：**ContentResolver虽然是通过Binder进程间通信机制打通了应用程序之间共享数据的通道，但Content Provider组件在不同应用程序之间传输数据是基于匿名共享内存机制来实现的。**

-    SurfaceView与View的区别

        (1)View主要适用于主动更新的情况下，而SurfaceView主要适用于被动更新，例如频繁地刷新； (2)View在主线程中对画面进行刷新，而SurfaceView通常会通过一个子线程来进行页面刷新； (3)View在绘图时没有使用双缓冲机制，而SurfaceView在底层实现机制中就已经实现了双缓冲机制。

-    onPause、onStop

        onpause() **当系统即将恢复另一个activity的时候，此方法通常用于提交一些未保存的持久性数据**，停止动画和一些可能会消耗CPU的东西，它应该尽一切可能快的完成，因为接下来的Activity在OnPause()返回之前不会被恢复。

        onstop()  当activity不**再对用户可见时调用此方法**，这方法可能被调用，因为它即将被销毁，或者因为另外一个Activity（可能是一个已经存在的Activity或者一个新的Activity）已经被恢复然后覆盖了这个activity。
        接下来如果这个activity 即将再次与用户交互（我觉得是再次对用户可见的意思），就会调用onRestart()方法，如果这个Activity即将死亡的话，就会调用onDestroy()方法。

-    java 创建匿名内部类的原因以及有什么作用

        1、匿名内部类就是没有名字的内部类。这是Java为了方便我们编写程序而设计的一个机制。因为有时候有的内部类只需要创建一个它的对象就可以了，以后再不会用到这个类，这时候使用匿名内部类就比较合适，而且也免去了给它取名字的烦恼。

        2、如果满足下面的一些条件，使用匿名内部类是比较合适的： 
        ·只用到类的一个实例。 
        ·类在定义后马上用到。 
        ·类非常小（SUN推荐是在4行代码以下） 
        ·给类命名并不会导致你的代码更容易被理解。 
        在使用匿名内部类时，要记住以下几个原则： 
        ·匿名内部类不能有构造方法。 
        ·匿名内部类不能定义任何静态成员、方法和类。 
        ·匿名内部类不能是public,protected,private,static。 
        ·只能创建匿名内部类的一个实例。 
        ·一个匿名内部类一定是在new的后面，用其隐含实现一个接口或实现一个类。 
        ·因匿名内部类为局部内部类，所以局部内部类的所有限制都对其生效。 

-    Service 能不能弹出 Dialog ？

        一般情况下 Dialog 的显示必须要依附于 Window，但是有时候可以申请一些权限，来弹出系统级别的 Dialog，所以是可以实现的

-    WindowManager 的相关知识 

        WindowManager主要用来管理窗口的一些状态、属性、view增加、删除、更新、窗口顺序、消息收集和处理等。

        通过Context.getSystemService(Context.WINDOW_SERVICE)的方式可以获得WindowManager的实例.

        WindowManager继承自ViewManager，里面涉及到窗口管理的三个重要方法，分别是：

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

- 比较全的面试题 http://www.jianshu.com/p/13786463635d

- 单利模式双重判断是否为空以及加上两种锁的原因

  强烈推荐 http://www.jianshu.com/p/4586cb11c62d

- 理解 Java 垃圾回收机制

  http://jayfeng.com/2016/03/11/%E7%90%86%E8%A7%A3Java%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6/

- 把虚拟机运行时包含的几个数据区和执行引擎画了下，包括方法区、虚拟机栈、本地方法栈、堆和程序计数器，然后介绍每个区域有什么作用，最后讲ClassLoader的类加载机制，还顺便说了下双亲委派机制。

- 什么是深拷贝和浅拷贝

  浅拷贝：使用一个已知实例对新创建实例的成员变量逐个赋值，这个方式被称为浅拷贝。

  深拷贝：当一个类的拷贝构造方法，不仅要复制对象的所有非引用成员变量值，还要为引用类型的成员变量创建新的实例，并且初始化为形式参数实例值。这个方式称为深拷贝

- bitmap对象的理解

- Bitmap压缩和缓存

  http://blog.csdn.net/jijiaxin1989/article/details/45063279 这篇文章主要讲述了如何进行 Bitmap 的压缩和缓存

  http://jayfeng.com/2016/03/22/Android-Bitmap%E9%9D%A2%E9%9D%A2%E8%A7%82/


- 位操作

  ![](http://ww1.sinaimg.cn/large/b10d1ea5ly1fd3pf13bobj20mj04uq2z)

- webview 控件的使用

  https://www.kymjs.com/code/2015/05/03/01/

- 堆排序原理

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

  *   程序计数器

      一块小的内存空间，属于线程私有的，可以这样理解，当线程恢复线程需要一些记录信息，这就是他的作用，每条线程都有一个独立的程序计数器

  *   Java 虚拟机栈

      也是线程私有，就是传统意义上的栈。存储一些局部变量表，操作数栈，方法入口等等

  *   本地方法栈

      与虚拟机栈的作用非常相似，但是其执行的 Native 方法

  *   Java 堆

      是内存区域管理中最大的一块，其为线程贡献，垃圾回收的主要场所，不用必须在物理上连续

  *   方法区

      与 java 堆差不多，也是线程共享的内存区域，它用于已经被存储的类信息、常量、静态变量，习惯上被称为永久带。

  *   运行时常量池

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