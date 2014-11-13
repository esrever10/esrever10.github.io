---

layout: post
categories: [System]
tags: [Xcode ,duplicate symbol, link, 静态库]

---

项目里用到不少静态库, 编译通过了, link的时候真机没问题, 模拟器却死活link不过, 错误就是坑爹的duplicate symbol.
我特么google好顿搜索, 什么合并静态库什去掉link选项之类的都试过, 还是不好用, 郁闷. 自己冷静分析一下原因, 
如果有重复符号存在, 为何真机能link过, 模拟器link不过, 有两种可能性.

1. 我使用的模拟器i386库存在重复符号, 而真机armv7库不存在重复符号.
2. link的时候, xcode在link i386库和armv7的时候使用了不同的link选项.

so, 接下来就是排除问题, 我用nm命令查看了i386和armv7库, 发现都存在重复符号, 那么第一个可能性被排除, 
然后我又去比较了xcode在link两个库时候的link选项, 发现armv7库在link的时候多出了dead_strip一项选项, 眼前一亮, what's this?
google一下, 发现确实和重复符号有关, 不管怎么样先试一下, 于是在other link flag 中添加了-dead_strip一项, 果不其然, 
这会模拟器也link通过了.

搜了一下dead_strip, 应该是删除多余的库符号.
