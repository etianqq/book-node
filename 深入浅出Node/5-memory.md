## V8的垃圾回收机制与内存限制

垃圾回收会引发JavaScript线程暂停执行。

### 1. 垃圾回收

v8使用分代式垃圾回收机制，这和JAVA的回收算法类似。

这种回收机制将内存区分为**年轻代和老年代**。两个里面存放的对象生命周期不一样。两种分别使用不同的回收算法。

* 新生代存放的对象生命周期很短（比如局部变量）
* 老年代存放的对象生命周期相当长（比如全局变量）

#### 年轻代回收算法scavenge

**它会将堆内存一分为二，在这两个空间里面**，只有一个会使用。另一个闲置，分别是from/to 空间，当我们分配对象时，会在From空间中进行分配，在回收时，会检查from里面存活的对象，然后复制到to空间，非存活的对象会被释放掉。完成后，两个空间的功能会做一个切换。下图为很经典的图

![](https://img-blog.csdn.net/20140725225453475?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcWluZ2h1YTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

年轻代晋升到老年代的条件：

1. 对象是否经历过scavenge回收
2. To空间的内存占用比超过限制（25%？）

#### 老年代回收算法：Mark-Sweep &Mark-Compact:标记-清除

**Mark-Sweep标记清除**：在标记阶段遍历堆中所有对象，并标记活着的对象，在随后的清除阶段中，只清除没有被标记的对象。

缺点：在进行一次标记清除之后，内存空间会出现不连续的状态。

为了解决Mark-Sweep的内存碎片问题，Mark-Compact被提出来。

**Mark-Compact**在Mark-Sweep基础上演变而来，差别在于，在整理对象时，将活着的对象往一端移动，移动完成后，直接清理掉边界外的内存。

![](https://img-blog.csdn.net/20140726075101387?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcWluZ2h1YTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

| 回收算法     | Mark-Sweep   | Mark-Compact | scavenge           |
| ------------ | ------------ | ------------ | ------------------ |
| 速度         | 中等         | 最慢         | 最快               |
| 空间开销     | 少（有碎片） | 少（无碎片） | 双倍空间（无碎片） |
| 是否移动对象 | 否           | 是           | 是                 |

#### 增量标记

为了降低全堆垃圾回收带来的停顿时间，V8先从标记入手，将原本要一口气停顿完成的动作改为增量标记，也就是拆分为许多小“进步”，每做完一“步进”，就让JavaScript应用逻辑执行一小会儿，垃圾回收与应用逻辑交替执行直到标记阶段完成。



### 2. 高效实用内存

在正常的javascript执行中，无法立即回收的内存有**闭包**和**全局引用**两种情况。

### 3. 内存泄漏

慎将内存当做缓存！一旦一个对象被当做缓存来使用，那就意味着它将会常驻在老生代中。

如果要使用大量缓存，比较好的解决方案是采用进程外的缓存，比如Redis。

### 4. 大内存应用

利用`stream`模块处理大文件。

```javascript
fs.createReadStream();
fs.createWriteStream();
```

