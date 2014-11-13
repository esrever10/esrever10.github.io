---

layout: post
categories: [Game]
tags: [Cocos2d-x 优化 翻译]

---
[原文地址](http://www.cocos2d-x.org/projects/cocos2d-x/wiki/How_to_optimise_memory_usage)  
为了练习英语= =, 翻译了一篇cocos2d-x内存优化的文章, 发现文章本身并没有什么杀手级的优化手段, 所以请欣赏我精湛的翻译技巧.(逃...  
不了解cocos2d-x的不用看了, 这不是一篇一般性的优化指导.  
这是我第一次翻译英文文章, 有不懂的地方请参照原文.  

# 怎样优化内存使用  
    - 内存优化原则  
      + 了解瓶颈所在, 把它们显示出来~.  
      + 拒绝过度优化.  
    - 内存优化的层次  
      + cocos2d-x 客户端层  
      + cocos2d-x 引擎层  
      + c++ 语言层次  
    - 提示 & 技巧  
    - 推荐阅读  

## 内存优化原则  
为了优化程序的内存使用, 你应该知道是什么消耗了你的程序中大部分的内存. 答案就是*纹理*!  
纹理几乎占据了游戏中90%的内存. 所以我们应该尽最大的努力缩减我们程序中纹理的内存使用, 
否则当系统处于低内存警告时就会杀掉你的程序.  
这里我讲给出关于cocos2d-x游戏内存优化原则的两个一般性指导.  

### 了解瓶颈所在, 把它们显示出来  
哪种类型的纹理占据了大部分的程序内存? 这些纹理占据了多少内存?  
你可不要数着手指头胡乱猜测.  
恰好有这样一个工具集为你而准备的. 它们是苹果公司出品的工具, Allocations和Leaks.  
你可以在Xcode中长按Run键然后选择Profile选项来选择这两个工具.  
下面是截图:  
![](https://raw.github.com/ellochen/Img-store/master/instruments.jpeg)  
你可以使用Allocation工具来监控你的程序内存使用情况, 并使用Leaks来观察内存泄露.  
你仍然可以使用一些代码来获取关于程序内存使用的有用信息.  
给出代码:  

        CCTextureCache::sharedTextureCache()->dumpCachedTextureInfo();  

当你调用这行代码并在DEBUG运行你的游戏时, 你将在Xcode的Console窗口看到一些格式化的log.  
它们就像这样:  

        Cocos2d: cocos2d: "cc_fps_images" rc=5 id=3 256 x 32 @ 16 bpp => 16 KB
        Cocos2d: cocos2d: "XXX/hd/actor.pvr.ccz" rc=1059 id=4 2048 x 2048 @ 32 bpp => 16384 KB
        Cocos2d: cocos2d: CCTextureCache dumpDebugInfo: 2 textures, for 16400 KB (16.02 MB)
        
就像你看到的, 它显示了纹理的名字, 引用计数, id, size以及每像素有多少位(bit). 另外非常重要的一点,   
它显示了纹理的内存使用情况.在这里*cc_fps_images*占据16KB的内存, 而actor.pvr.ccz却占据了16M内存之多.  

### 拒绝过度优化  
这是一个一般性的优化原则. 你在做内存优化的时候需要做适当的权衡. 因为有时图片质量和图片内存是对立的.  
永远不要去过度优化!  (译注: 作者这么激动,我猜他是想说否则美术MM会发怒的!)  

## 内存优化的层次  
这里我们将cocos2d-x的内存优化划分为3个层次. 在每一个层次上, 我们有不同的观点和策略也包括一小点变化.  

### cocos2d-x 客户端层  
这是最重要的优化层次, 我们应该引起注意. 我们是在cocos2d-x的引擎之上构建我们的程序的, 所以引擎自身多少提供了一些优化选项.  
在这个层次, 我们的付出将会回报很多.  
简言之, 我们可以优化诸如纹理, 音频, 字体以及粒子系统的内存使用.  

- 首先, 让我们优化下纹理.  
为了优化纹理的使用, 我们必须知道神马因素导致纹理内存的大量使用.  
这里有3个因素, 它们是纹理的格式(压缩的或者未压缩的), 色深和大小.  
我们可以使用PVR格式的纹理来减小内存使用. 推荐的纹理格式是pvr.ccz. 
纹理每像素用到的位(bit)越多, 图片的质量越好. 但是它将同样占据更多的内存.  
所以我们使用纹理色深RBGA4444来代替RBGA8888, 前者耗费的内存是后者的一半.  
我们也发现大的纹理也会引起内存相关的问题.所以你最好使用适度大小的纹理.  
- 其次, 让我们做一些关于音频的工作  
同样也有三个因素影响音频文件的内存使用. 它们是音频文件的格式, 比特率(bit rate)和抽样率(sample rate).  
我们喜欢mp3格式的音频文件. 因为它同时支持android和ios平台. mp3是一种压缩的格式并且可被硬件加速.  
你应该保持你的背景音乐大小在800KB以下. 最简单的方式是减少背景音乐的时间并且在程序中重复播放.  
你应该保持你的音频抽样率在96-128kbps之前, 比特率有44kHz也足够了.  
- 最后, 我们来谈谈关于字体和粒子系统的优化.  
这里我们给出2个小建议: 当使用BMFont来显示Game分数时, 应该最小化你图片中的字符数量. 比如, 
如果你只想显示数字, 就将图片中所有的字母都去掉.  
至于粒子, 我们应该通过减少粒子的数量来减少内存使用.  

### cocos2d-x 引擎层  
如果你不是OpenGLES和游戏引擎高手, 你可以将这一层次的优化交给我们.  
cocos2d-x是开源的游戏引擎, 如果你多少有一些引擎层次的优化, 请让我们知道!  
我们欢迎任何改进和代码提交.  

### C++ 语言层次  
在这一层次, 我建议使用cocos2d-x提供的内存管理方式并尽你最大的努力避免内存泄露.  

## 提示 & 技巧  
1. 一帧一帧地读取游戏资源.  
2. 减少draw的调用: 使用CCSpriteBatchNode.  
3. 从大到小加载纹理.  
4. 留意内存峰点.  
5. 使用闪屏来提前加载游戏资源.  
6. 及时释放不用的资源.  
7. 在内存警告的时候释放缓存的资源.  
8. 使用texturePacker来优化纹理的大小, 格式, 色深等等.  
9. 小心使用JPGs!  
10. 使用16-bit, RBGA4444色深的纹理.  
11. 使用NPOT纹理代替POT纹理.  
12. 避免加载过大的纹理.  
13. 相对于原始PNG文件优先使用1024 * 1024的NPOT pvr.ccz纹理.  

## 推荐阅读
[Steffen Itterheim's cocos2d memory optimization tutorials](http://www.learn-cocos2d.com/2012/11/optimize-memory-usage-bundle-size-cocos2d-app/)  
[Apple's developer guide for reducing memory usage](https://developer.apple.com/library/ios/documentation/3DDrawing/Conceptual/OpenGLES_ProgrammingGuide/TechniquesForWorkingWithTextureData/TechniquesForWorkingWithTextureData.html)  
