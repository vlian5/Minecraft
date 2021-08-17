---
layout: post
title:  "Minecraft服务器启动参数"
categories: 教程
mathjax: true
---

## 介绍

在研究了 JVM、标志和测试各种组合数周后，我为 Minecraft 提出了一组高度调整的垃圾收集标志。我在我的服务器上测试了这些，并且已经使用了多年。然后我向公众宣布了我的研究，直到今天，许多服务器多年来一直在使用我的标志建议，并报告垃圾收集行为的巨大改进。

这些标志是大量努力的结果，也是在各种服务器大小、插件列表和服务器类型的生产环境中看到的结果。他们已经反复证明了自己。

我强烈建议使用这些标志来启动您的服务器。这些标志有助于使您的服务器保持一致运行，而不会出现任何大的垃圾收集峰值。 CPU 可能略高，但您的服务器总体上会更加可靠和稳定 TPS。

如果这些标志对您的服务器有帮助，请考虑捐赠！


## 要使用的 JVM 启动标志 – MC 1.15（Java 8+、MC 1.8+）更新

准确使用这些标志，只改变Xmx和Xms。这些标志在任何大小的内存中都能工作并有相应的规模，甚至是500MB，但1.15在如此低的内存中不会做得很好...)
启动参数
>java -Xms10G -Xmx10G * -XX:+UseG1GC * -XX:+ParallelRefProcEnabled * -XX:MaxGCPauseMillis=200 * -XX:+UnlockExperimentalVMOptions * -XX:+DisableExplicitGC * -XX:+AlwaysPreTouch * -XX:G1NewSizePercent=30 * -XX:G1MaxNewSizePercent=40 * -XX:G1HeapRegionSize=8M * -XX:G1ReservePercent=20 * -XX:G1HeapWastePercent=5 * -XX:G1MixedGCCountTarget=4 * -XX:InitiatingHeapOccupancyPercent=15 * -XX:G1MixedGCLiveThresholdPercent=90 * -XX:G1RSetUpdatingPauseTimePercent=5 * -XX:SurvivorRatio=32 * -XX:+PerfDisableSharedMem * -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=https://mcflags.emc.gs -Daikars.new.flags=true -jar paperclip.jar nogui
更新于2020年4月25日

推荐所有版本的 Minecraft 使用这些标志！ 1.8 一直到 1.15+，用这套。

## 重要 - 阅读 - 不要用尽你的内存！！

在设置 Xms 和 Xmx 值时，如果您的主机说您有 8000M 内存，请不要使用 8000M！ Minecraft（和 Java）需要在 Xmx 参数之上的额外内存。建议将您的 Xmx/Xms 减少约 1000-1500M，以避免内存不足或“OOMKiller”攻击您的服务器。这也为操作系统也使用内存留下了空间。

有8000M内存？使用 6500M 以确保安全。但是您也可以询问您的主机是否会为您支付这些开销并改为给您 9500M。一些主机会！就问吧。

## 推荐的服务器

如果您还没有使用Paper，Spigot的极大改进版本，您真的需要切换了！ Paper是Spigot 的更快版本，具有巨大的性能改进。它还带有错误和漏洞修复、许多新功能和大量新 API，供开发人员使用并获得更好的插件体验。

Paper是Spigot的替代品，每个Bukkit/Spigot插件的工作原理都是一样的。切换没有任何坏处。

Paper 是运行具有良好性能的 1.15 服务器的唯一方法！

下载地址：[Paper](https://papermc.io/)

## 推荐内存

我建议至少使用 6-10GB，不管玩家有多少！如果您负担不起 10GB 的内存，请提供尽可能多的内存，但也要确保为操作系统留出一些内存。 G1GC 运行得更好，内存越大。
如果您使用 12GB 或更少的内存运行 MC，则不应调整这些参数。

## 最佳内存

如果您拥有并使用超过 12GB 的内存，请调整以下内容：
>-XX:G1NewSizePercent=40
-XX:G1MaxNewSizePercent=50
-XX:G1HeapRegionSize=16M
-XX:G1ReservePercent=15
-XX:InitiatingHeapOccupancyPercent=20

这些变化的解释：
基本标志集的目标是 30/40，以降低空间问题的风险。有了更多的内存，问题就更少了。我们可以以 40/50 为新一代提供更多，并降低储备百分比，因为默认储备已经更大。
区域大小增加有助于减少大量分配，并加快注释速度。我们需要在较小的堆中使用较小的区域大小以确保有足够数量的区域可用
我们可以开始寻找年老代内存以在 IHOP 为 20 时有更多延迟来回收，因为我们有更多的年老代内存可用于 CPU 上的空间。

## Java GC 日志记录

你有这些标志的老问题吗？帮我帮你！根据您的 java 版本添加以下标志以启用 GC 日志记录：

* Java 8-10:

>-Xloggc:gc.log -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCTimeStamps -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=1M

* Java 11+:

>-Xlog:gc*:logs/gc.log:time,uptime:filecount=5,filesize=1M

一旦您开始在 Timings 中看到老年代集合，请获取 logs/gc.log 文件（与您的 latest.log 相同的位置）并将其通过 Paper Discord 发送给我进行分析。
GC 日志记录不会损害您的性能，并且可以始终保持开启状态。文件不会占用太多空间（5MB）

#### 标志的技术说明：

1.**-Xms matching -Xmx – Why**：您永远不应该在 -Xmx 可以完全耗尽内存的情况下运行您的服务器。您的服务器应该总是使用整个 -Xmx！
然后，您应该确保操作系统在 Xmx 之上有额外的内存用于非 MC/OS 级别的事情。因此，你永远不应该使用 -Xmx 设置运行 MC，如果 java 使用它你不能支持它。现在，这意味着如果 -Xms 低于 -Xmx -YOU HAVE UNUSED MEMORY！未使用的内存是浪费内存。G1（可能还有甚至 CMS 达到某个阈值，但我只是在说明我确定的内容）在提供更多内存的情况下运行得更好。 G1 自适应地选择给每个区域多少内存以优化暂停时间。如果你的内存超过了达到最佳暂停时间所需的内存，G1 只会将额外的内存推送到老年代，它不会伤害你（CMS 可能不是这种情况，但 G1 是这种情况）。改进 GC 行为的基本思想是确保生命周期较短的对象很年轻就死去并且永远不会被提升。 G1 拥有的内存越多，您就越能确保对象不会过早地提升到老年代。G1 的操作方式与以前的收集器不同，并且能够更有效地处理更大的堆。
如果它不需要给它的内存，它就不会使用它。整个引擎以不同的方式运行并且不会受到太大堆的影响，这是行业广泛接受的信息，在 G1 下保持 Xms 和 Xmx 相同！

2.**UnlockExperimentalVMOptions**  需要以下一些选项

3.**G1NewSizePercent** :这些是重要的。在 CMS 和其他代中，调整 New Generation 导致 FIXED SIZE New Gen 并且通常通过使用 -Xmn 的显式大小设置来完成。使用 G1，情况会更好！您现在可以为新一代指定总体所需范围的百分比。通过这些设置，我们告诉 G1 不要为新一代使用其默认的 5%，而是给它 40%！**Minecraft 具有极高的内存分配率，在 30 名玩家的服务器上至少达到每秒 800 兆字节！这主要是短寿命的对象（块位置）**
现在，这意味着 MC 真的需要更多地关注 New Generation 才能支持这种分配率。如果你的新生代太小，你每秒会运行新生代集合1-2+次，这真的很糟糕。你会有很多暂停，TPS有遭受痛苦的风险，服务器将无法保持加上 GC 的成本。然后结合对象现在将更快地提升的事实，导致您的 Old Gen 增长得更快。给定更多的 NewGen，我们能够减慢 Young Gen 收集的间隔，从而有更多时间让生命周期较短的对象更年轻地死去，并且整体上更有效的 GC 行为。

4.**G1MixedGCLiveThresholdPercent**:控制何时在 Young GC 集合中包含混合 GC 中的区域，保持 Old Gen 整洁而不进行正常的 Old Gen GC 收集。当你的记忆低于这个百分比时，老一代甚至不会被包含在“混合”集合中。混合不像完整的旧集合那么重，因此对旧的进行小的增量清理可以保持内存使用量较少。
默认值是 65 到 85，具体取决于 Java 版本，我们设置为 90 以确保我们尽快回收旧代中的垃圾以保留尽可能多的空闲区域。我的旧标志设置为 35，这是一个错误。我把这个标志的意图颠倒了，因为我认为 35 是 65 的作用。您不应该为此数字使用 35。

5.**G1ReservePercent=20**:最新版本中的 MC 内存分配率真的很疯狂。我们冒着可怕的“空间耗尽”的风险，没有足够的可用内存来移动数据。这确保有更多内存等待用于此操作。默认值是 10，所以我们再给它 10。

6.**MaxTenuringThreshold=1**:Minecraft 的内存分配率非常高。在那段记忆中，大部分是在伊甸园一代中回收的。然而，瞬态数据会溢出到幸存者中。最初玩完全删除 Survivor 并获得了不错的结果，但确实导致瞬态数据进入 Old ，这是不好的。 Max Tenuring 1 确保我们不会将瞬态数据提升到老年代，但任何能在 2 次垃圾传递中幸存下来的东西收集只是将被假定为更长寿。
这样做极大地减少了年轻集合中的暂停时间，因为在幸存者空间中为终身对象复制数据多达 15 次，对于实际的旧内存来说确实需要很多时间。理想情况下，GC 引擎会跟踪对象的平均年龄并更快地保留数据，但这不是它的工作原理。
考虑到平均 GC 速率是 10 秒到每个年轻集合的分钟数以上，这不会导致任何“垃圾”被提升，只会延迟在混合 GC 中收集更长寿命的内存。

7.**SurvivorRatio=32**: 因为我们大幅降低了 MaxTenuringThreshold，我们将大幅减少幸存者空间的使用。这会释放更多区域供 Eden 使用。

8.**AlwaysPreTouch**:AlwaysPreTouch 在进程启动时获取内存设置和保留，确保它是连续的，从而更多地提高它的效率。这提高了操作系统的内存访问速度。强制使用透明大页面

9.**+DisableExplicitGC**:许多插件认为他们知道如何控制内存，并尝试调用垃圾收集。执行此操作的插件会触发完整的垃圾收集，从而引发巨大的滞后峰值。此标志禁止插件尝试执行此操作，从而保护您免受其不良代码的影响。

10.**MaxGCPauseMillis=200**:此设置控制在为新一代指定的最小和最大范围之间使用的内存量。这是您希望服务器暂停收集的“目标”。 200 的目标是最多损失 4 个滴答声。这将导致短暂的 TPS 下降，但是服务器可以立即弥补这一下降，这意味着它不会对您的 TPS 产生有意义的影响。 200 毫秒比玩家可以识别的要低。在测试中，将此值限制为更低的数字会导致 G1 无法足够快地重新收集内存，并且可能会耗尽旧代，从而触发完整收集。仅仅因为这个数字是 200 并不意味着每个集合都是 200。它意味着如果它真的需要它最多可以使用 200，并且我们需要让它在有内存时收集它的工作。

11.**+ParallelRefProcEnabled**: 优化 GC 过程，使用多线程进行弱引用检查。不知道为什么这不是默认的......

12.**G1RSetUpdatingPauseTimePercent=5**：默认为暂停更新 Rsets 期间花费的时间的 10%，将其减少到 5% 以使其更多并发以减少暂停持续时间。

13.**G1MixedGCCountTarget=4**：默认为 8。因为我们的目标是收集速度较慢，使用较少的旧代，请尝试更快地回收旧代内存以避免耗尽旧代。

14.**G1HeapRegionSize=8M+**:默认为自动计算。对于 Minecraft 来说非常重要，尤其是 1.15，因为在内存不足的情况下，默认计算在大多数情况下会太低。任何此大小（4MB）的任何内存分配都将被视为“巨大的”并直接提升到老年代并且更难释放。如果您允许 java 使用默认值，您将被销毁，因为您的大量内存被视为 Humongous。

15.**+PerfDisableSharedMem**:导致 GC 写入文件系统，如果磁盘 IO 很高，这可能会导致严重延迟 – 请参阅 [https://www.evanjones.ca/jvm-mmap-pause.html](https://www.evanjones.ca/jvm-mmap-pause.html)

## 使用大页面

同样对于大页面 - 使用 -Xms = -Xmx 更为重要！大页面需要为其指定所有内存，否则您最终可能没有收益。无论如何，操作系统不会使用此内存，因此请使用它。
另外使用这些标志（Metaspace 仅适用于 Java 8，不要用于 Java7）：
代码：
>-XX:+UseLargePagesInMetaspace

## 透明大页面

有争议的功能，但如果您无法为真正的 HugeTLBFS 配置主机，则可能可用。尝试添加 -XX:+UseTransparentHugePages 但非常重要的是您还设置了 AlwaysPreTouch。否则 THP 可能会伤害您。我还没有测量过 THP 对 MC 的作用及其对 AlwaysPreTouch 的影响，所以本节是为想要体验的高级用户准备的。

参考资料：
[](https://mcflags.emc.gs/)
[](https://aikar.co/2018/07/02/tuning-the-jvm-g1gc-garbage-collector-flags-for-minecraft/)