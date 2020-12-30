```{=html}
<!DOCTYPE html>
<html>

<head>
    <title>Untitled router -- dramforever</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="../../styles/default.css">
    <link rel="stylesheet" href="../../styles/post.css">
</head>

<body>

<div id="site-title">
    <h1 id="site-title-main"><a href="/">
        dramforever
    </a></h1>
    <div id="site-title-sub">a row of my life</div>
</div>

<article>
<div id="post-title">
    <h1 id="post-title-main">Untitled router</h1>
    <span id="post-title-sub">2020-??-??</span>
</div>
```

*(UNRELASED VERSION, WORK IN PROGRESS)*

## 一次命运转折的约饭

2020 年秋季开学之前的什么时候，在一次约饭的时候，正好和一些计算机系专业课的助教说到这学期要参加的一些课程的事情。

根据课程的安排（可参见[也谈课改——以网原为例][cs-course-reform]一文），两门比较硬核的专业课：计算机组成原理和计算机网络原理（简称**计原**和**网原**而不是什么乱七八糟的东西）作为两门实验项目改动比较大的专业课，现在大概是如下的情况：

[cs-course-reform]: https://harrychen.xyz/2020/06/20/cs-course-reform/

- 从 2020 年开始，计原课程的“奋战三星期，造台计算机”与国际先进技术接轨，又原来的 MIPS 架构改为 RISC-V 架构。当然造机这件事是不变的，三人一组做一个软核。这里有两个小故事：
    - 之前假期的时候在 ICFP 的时候在神秘的 Coffee break room 聊的时候，有人就提到 MIPS 架构已经凉了。虽然还能买到 MIPS 的芯片和产品，但是已经不再有新的发展了。
    - 有一位同学在上课之前尝试翻了下往年题目，发现了关于分支延迟槽的问题，于是在群里问这个可以在哪儿学到。这位同学可能需要一个时光机。
- 从 2019 年开始，网原课程的大实验是自己造路由器，分为软件实验和硬件实验两个 track。2020 年的具体情况如下：
    - 软件实验是每组 3 位同学，每人做一个路由器，然后测试时使用在线平台的 Raspberry Pi 运行，通过交换机的虚拟连接测试在给定网络拓扑下的性能和功能。
    - 硬件实验是每组 3 位同学，一起做一个路由器。今年的要求是在一个 FPGA 上完成大部分转发相关的内容，然后在计原实验里造的软核 CPU 上运行 RIPv2 动态路由协议相关的处理，也就是说相当于三个人一起做了两个课的实验，当然要求显然是高于软件实验的。因为包含了计原和网原两个课的内容，所以被称为“计网联合实验”。

其中一位助教建议我参加联合实验。考虑到我之前并没有参加前面的硬件相关的数字逻辑课程，我一再说这可能是误解了我的水平，但是看来这位助教对我还是很有信心的。

后来和找到的两位朋友 akyuu 和 pwe 组成了小组，说了这些事情之后，我们按照课程要求报了名，通过一个（看来比较主观但并不是没有道理的）“选拔”，正式加入了联合实验的队伍。

## 摸鱼和赶工的几周

联合实验是每周的一个晚上开一次会，讨论上周的工作，布置下周的任务。佛系的我们最开始是每周大概约一个研讨间待上个好久，后来直接在宿舍的活动室工作了。

由于摸鱼比较严重，我们经常是赶在晚上开会当天才做完，最神奇的一次是从研讨间出来之后，已经还有大概只半个小时左右就要开会了，只能飞奔赶往。

暂时先不说技术细节了，感觉没什么特别的，可能以后再讲吧。

感觉在造机之前的那段时间里，其实是最开始的时候做那些比较基础的功能的时候比较头疼，因为确实没有做过什么硬件和网络相关的东西，需要熟悉整个流程、AXI Stream 协议，一些 workflow 相关的事情等，以及做出来的东西的可见性较差，不太能直观感受到进度。

在我们完成转发引擎的那个晚上，我提议跑一个 HTTP 服务器通过我们的 FPGA 路由下载一个文件。当我们看到 `python3 -m http.server` 的页面，以及成功下载了一个文件的时候，至少我当时非常高兴，感觉到自己至少做了一个可以在一定程度上接入现有的环境中作为 infrastructure 的一个东西。

或者简单来说就是，它通了。

那天晚上在宿舍活动室，我们组的三人对着一个 effectively 就是一根超级贵的网线的板子，庆祝了好久。

当然，我们也有不太顺利的时候。中间有一段时间，我们发现转发部分的逻辑有性能问题，十分困惑，改的时候也很头疼，越改性能越差，改着改着干脆就不通了。试了很多种方式才最终搞定。

## 造机的三周

硬件实验，造机还是得造的。

在硬件的设计方面，我们做的是一个 [Classic RISC pipeline][risc-pipeline] 的五级流水线设计，这点上没有什么特别的。在具体的一些细节上实际上参考了之前路由器的数据处理流水线，以及喵喵的一些建议，没有按照传统的方式设计控制器，而是使用 `ready/valid` 握手和总线仲裁协调各个流水线级的工作。在之后的一些调整设计、修改实现的过程中，虽然不是很明显，但是还是能感受到这样的一个设计所带来的方便，因此我觉得这是一个比较科学的做法。

[risc-pipeline]: https://en.wikipedia.org/wiki/Classic_RISC_pipeline

总线我们使用了一个相当于 [Wishbone Classic][wishbone] 的总线协议，当然没有使用现有的组件，而是自己实现了访存、外设、仲裁、互联的模块。本来最开始想做的是 Pipelined 模式的总线，但是实在是没做出来就放弃了。在总线相关的事情上，我也在和一些课程的编外人员（“不是助教”）讨论，要不要在硬件相关的实验中加入总线协议的内容，使大家的设计可以互联互通。

[wishbone]: https://www.wishbone-interconnect.org

不知道是不是 Stolkhome Syndrome 的原因，我现在真的非常喜欢 RISC-V 的指令集。

如果更客观的来说，我之前曾经也想过自己做一个 CPU。之前我曾经做过一个 6502 的模拟器，可以运行一个简单的 BASIC 解释器；也尝试用 Clash 做过一些神秘的小指令集的 CPU，但是由于设计方面实在是不知道如何下手最终搁置了。

而 RISC-V 实际上是一个美妙的机会，三周实现的 RISC-V RV32I 的大部分指令，实际上相当于已经是一个 `riscv32-none` 的裸机，可以使用现有的工具链。RISC-V 的活跃的社区意味着这些工具不是祖传的 legacy，而是最新的成果。

或许这就是开放的社区的意义所在吧。

## 意外顺利的又几周

当然，说是意外顺利，可能更恰当的说法是有些意外，有些顺利。

CPU 与路由器的集(feng)成(he)从把 CPU 的总线和基于 AXI Stream 的数据平面连接开始。在这点上折腾了很久都没搞定，最后发现是别的地方写出锅了。之后逐渐的我们把路由部分的逻辑中需要软件访问的部分，以及收发以太网帧的都作为 MMIO 接入到了总线上。

队友也参考提供好的模板完成了 RIP 协议的软件，在本地测试了一下，看起来可以和 BIRD 互操作。

（有一次聊天的时候意外发现，VGA 用的 framebuffer 直接复制过来就是以太网 framebuffer，名字都不用改。）

这段时间我的状态也不是很好，主要还是靠两位队友 carry 的大部分硬件工作和全部软件工作，作为组长的 dram 就一直在划水。这里我要真诚感谢一下队友，感谢一下助教和不是助教，也要感谢此时以及支持我的所有人。

## 验收工作

随着千兆网络测速仪的研制成功，我们还是发现了一些大大小小的 bug，但是修起来还不是那么难。有两个到现在也没太搞明白的问题，一个是软件部分有一段似乎不太能正常运行，需要加上一些没用的代码，另一块是软件接收到的以太网帧出现被截断的问题，在换了一些写法之后似乎没有再遇到相同的现象，所以也没有追究。

到最后验收还是花了不少时间，不过比较高兴的是，最终测试环境的各种网络拓扑下的路由器工作都比较顺利。其中有一个测试是将三个路由器接成一个环，然后移除其中两个路由器之间的链路，过一段时间再恢复链路，来测试 RIP 动态路由协议的工作状况。由于之前说到我一直在划水，其实我是不知道软件是个什么情况的。连软件担当的队友都表示没想到写的东西能正常完成错误恢复。

验收完成之后，我三人一起去吃了一顿烤肉。

## 造机的答辩

2020 年 12 月 19 号是造机的答辩，作为第 5 组，在别的组答辩的时候，我们组的群里依次出现了 `v3` 到 `v10` 的 slides，页数从 25 增长到了 31。

答辩的整体过程都是我在说，不知道是紧张还是怕 31 页 10 分钟时间不够的原因，我的语速特别快。

答辩的全程好像有人来录像，以及听说有造机队伍来采访了某位队友。看来这些写新闻的对造机的工作不是很了解吧，至少我觉得最后写出来的推送并没有展现出我们实际完成了什么工作，从评论区也可以看出来大家没太理解我们在做什么。评论区有一个家长自称孩子 11 岁，想找个指导老师做一下这个三周造机之类的。倒不一定是技术难度问题，给这年纪孩子做点什么不好，来造机干啥……

另外，在答辩的最后，我插播了一段私货。事实上，这段私货的内容是我前一天的晚上才准备好的，当时事实上紧急叫了几个朋友帮忙看看有没有什么写的不太妙的地方，做了一些细微修改。这段在演讲的时候，内容我自己有所发挥，但是大致的文字内容是前一天写好的，在此：

> Claire Wolf 是 RISC-V B 扩展指令规范的主编，我们每个组的三个扩展指令就选自这里。她除此之外对开源的数字逻辑工具有很大贡献，包括开源的 HDL 工具 Yosys 等。
>
> 大家可能注意到我们下发的 bitmanip 规范的待定稿的作者的名字写的是 Clifford Wolf。如果大家有关注过这个人的话，就会知道这位 RISC-V 社区的重量级人物是一位跨性别者，在去年的大概这个时候正式以女性的身份出现在大家面前，包括将自己的名字改为 Claire。
>
> 我本人至少是非常敬佩她的。我想起不记得在哪里看到的一句话：RISC-V 给大家一个公平的起跑线。我希望这个公平的起跑线，不仅在技术上给大家一个开放的环境，不被当前 CPU 核心设计领域当前的近乎垄断的现状限制住，而且能让我们这些搞科技和学术的不被那些无关的事情所困扰，能够真正展现出自己的水平。
>
> 近来发生的一些跨性别者受到不正当对待事情，大家可能已经听说过了。我其实一直比较愧疚，没有做什么事情来给予帮助。希望今天我所说的至少能引起大家对这些群体以及这些事件的一些关注和思考。我觉得对于大部分人来说，尽量去理解，就已经是很好地在支持了。

结束之后，一位助教指出，网络测试仪在两个 FPGA 之间通信所使用的协议 [PonyLink][ponylink] 也是 Claire Wolf 的作品。大部分人可能并不能理解这样一个身份所带来的痛苦，但是至少我们作为技术人员，能够谈论的是一个人所做的贡献，而不是一些无关的处境或者身份问题，这其实正是我想看到的。

[ponylink]: https://github.com/cliffordwolf/PonyLink

## 最终答辩

最终的答辩计划在 2021 年 1 月 12 号进行，那时可能会进行组间的联通测试。没准这次可以做到 10 个路由器连成一个环？

```{=html}
</article>

</body>
</html>
```