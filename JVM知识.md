* java 垃圾回收机制

* JVM 类加载

* ART 和 Dalvik 之间的区别
http://blog.csdn.net/jason0539/article/details/50440669

 * JAVA虚拟机运行的是JAVA字节码，Dalvik虚拟机运行的是Dalvik字节码
 * Dalvik可执行文件体积更小
 * JVM基于栈，DVM基于寄存器
 * Dalvik虚拟机执行的是dex字节码，ART虚拟机执行的是本地机器码

* ART优点：
  
  * 系统性能显著提升
  * 应用启动更快、运行更快、体验更流畅、触感反馈更及时
  * 续航能力提升
  * 支持更低的硬件

* ART缺点
 
 * 更大的存储空间占用，可能增加10%-20%
 * 更长的应用安装时间

总的来说ART就是“空间换时间”