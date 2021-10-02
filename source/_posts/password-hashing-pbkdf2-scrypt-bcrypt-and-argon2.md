---
title: '[译] 密码哈希的方法：PBKDF2，Scrypt，Bcrypt 和 ARGON2'
date: 2020-03-20 19:17:01
tags:
	- hash password
	- encrypt
	- PBKDF2
	- Scrypt
	- Bcrypt
	- ARGON2

---


关于如何安全的存储密码以及使用何种算法总是有很多的争论：MD5、SHA1，SHA256、PBKDF2，Bcrypt、Scrypt、Argon2、明文？？

因此，我试图分析并总结了最新的合理选择：Scrypt、Bcrypt 和 Argon2 是符合条件的，MD5、SHA1、SHA256 就不太适合存储密码！😉

![](https://blog-private.oss-cn-shanghai.aliyuncs.com/20200309210312.png)

<!-- more -->

> * 原文地址：[Password Hashing: PBKDF2, Scrypt, Bcrypt and ARGON2](https://medium.com/@mpreziuso/password-hashing-pbkdf2-scrypt-bcrypt-and-argon2-e25aaf41598e)
> * 原文作者：[Michele Preziuso](https://medium.com/@mpreziuso)
> * 译文出自：[掘金翻译计划](https://github.com/xitu/gold-miner)
> * 本文永久链接：[https://github.com/xitu/gold-miner/blob/master/TODO1/password-hashing-pbkdf2-scrypt-bcrypt-and-argon2.md](https://github.com/xitu/gold-miner/blob/master/TODO1/password-hashing-pbkdf2-scrypt-bcrypt-and-argon2.md)
> * 译者：[司徒公子](https://github.com/todaycoder001)
> * 校对者：[xionglong58](https://github.com/xionglong58)、[GJXAIOU](https://github.com/GJXAIOU)



#### 总结

在 2015 年，我就已经发布了‘[密码哈希：PBKDF2、Scrypt、Bcrypt](https://medium.com/@mpreziuso/password-hashing-pbkdf2-scrypt-bcrypt-1ef4bb9c19b3)’文章，来作为对朋友问题的延伸回答。

概括的说：

> 攻击者通常拥有与我们不同的、更专业（强大）的硬件
>
> 攻击者之所以使用专业的硬件，是因为它能根据某些算法进行定制，定制化的硬件允许某些算法能比非专业硬件（CPU）上运行的更快，而且 —— 总体而言 —— 某些算法还可以并行化；
>
> 我们依赖慢哈希方法来对密码进行哈希，从而实现你采用的 CPU/GPU 处理器与攻击者的 GPU/FPGA/ASIC 处理器在计算能力上处于同一水准。

**以上这些都是正确的**，然而，数字加密货币的竞争又上升到另一个层面：数十亿美元的市值，
基于软件/硬件，以最快的速度来实现一种数字加密货币的底层算法，这相比于其他矿工来说也是一种优势，因此，也是最赚钱的一个。

在比特币使用 SHA256 来作为其底层加密方法的时候（因此，可以在已经优化的硬件上对其进行极大的优化，使其对矿工来说成为一种‘不公平’的数字货币），但其他加密货币的创建者试图通过依赖内存的方法来使新的数字加密货币能被更加公平的开采：[莱特币](https://litecoin.org/)（[Scrypt](https://en.wikipedia.org/wiki/Scrypt)）  是早期的示例，[Zcash](https://z.cash/)（[Equihash](https://en.wikipedia.org/wiki/Equihash)）是最近的示例。

这意味着用于密码哈希的慢方法正在被用来保护数百万甚至数十亿美元市值的数字加密货币，这使得慢哈希方法的最快实现是有意义的，而且通常这也是[公开可用的](https://github.com/tpruvot/ccminer)。

#### 那么今天什么是安全的呢？

原理还是一样的：我们需要一个加密社区审核过的慢函数并且依然未被破解。

**PBKDF2** 已经存在很长时间了，像[之前文章](https://medium.com/@mpreziuso/password-hashing-pbkdf2-scrypt-bcrypt-1ef4bb9c19b3)讨论的一样，它有点过时了：轻松的在多核系统（GPU）上实现并行，这对于定制系统（FPGA/ASIC）来说微不足道。所以我拒绝了它。

虽然在 1999 年 **BCrypt** 就产生了，并且在对抗 GPU/ASIC 方面要优于 PBKDF2，但是我还是不建议你在新系统中使用它，因为它在离线破解的威胁模型分析中表现并不突出。
尽管有一些数字加密货币依赖于它（即：NUD），但它并没有因此获得较大的普及，因此，FPGA/ASIC 社区也并没有足够的兴趣来构建它的硬件实现。
话虽如此，[Solar Designer](https://twitter.com/solardiz)（OpenWall）、Malvoni 和 Knezovic（萨格勒布大学）在 2014 年撰写了一篇论文，这篇文章描述了一种混合使用 ARM/FPGA 的单片系统来攻击该算法。

**SCrypt**  在如今是一个更好的选择：比 BCrypt设计得更好（尤其是关于内存方面）并且已经在该领域工作了 10 年。另一方面，它也被用于许多加密货币，并且我们有一些硬件（包括 FPGA 和 ASIC）能实现它。
尽管它们专门用于采矿，也可以将其重新用于破解。

## Argon2

写完[我的第一篇文章](https://medium.com/@mpreziuso/password-hashing-pbkdf2-scrypt-bcrypt-1ef4bb9c19b3)后不久，Argon2 在 2015 年 7 月赢得了密码哈希竞赛。

#### 密码哈希竞赛

该竞赛于 2012 年秋季启动，2013 年第一季度，竞赛委员会发布了征集参赛作品的通知，截止日期为 2014 年 3 月底。作为比赛的一部分，小组成员对提交的参赛作品进行了全面审核，并发布了一份初步的简短报告，其中描述了他们的选择标准和理由。

#### 介绍

Argon2 有两个主要的版本：**Argon2i** 是对抗侧信道攻击的最安全选择，而 **Argon2d** 是抵抗 GPU 破解攻击的最安全选择。

源代码可以在 [Github](https://github.com/p-h-c/phc-winner-argon2) 上获得，使用兼容 C89 的 C 语言编写，并在知识共享许可协议下获取许可，并且可以在大多数 ARM、x86 和 x64 架构的硬件上编译。

#### 基于 AES 实现

Argon2 基于 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)  实现，现代的 x64 和 ARM 处理器已经在指令集扩展中实现了它，从而大大缩小了普通系统和攻击者系统之间的性能差距，

#### 参数调整

两个版本的算法都可以实现参数化：

* **时间**开销，它定义了执行的时间
* **内存**开销，它定义了内存的使用情况
* **并行**程度，它定义了线程的数量

这意味着你可以分别调整这些参数，并根据你的用例、威胁模型和硬件规范来量身定制安全约束。

#### 权衡攻击

除此之外，Argon2 尤其能抵挡**排名权衡攻击**，这使得在现场可编程逻辑门阵列上进行低成本攻击变得更加困难：虽然，最近的现场可编程逻辑门阵列已经嵌入 RAM 区块，但是，内存带宽仍然是一个限制，并且为了减少内存带宽要求，攻击者必须为了 Argon2 使用更多的计算资源。

[规范](https://github.com/P-H-C/phc-winner-argon2/blob/master/argon2-specs.pdf)（见第 5 章）和同一作者的另外[一篇文章](https://orbilu.uni.lu/bitstream/10993/20043/1/Tradeoff%20Cryptanalysis.pdf)中讨论了这些攻击手段和相似的攻击手段，并将其与 scrypt 进行比较。

#### Argon2id

以下是来自 [Argon2 互联网工程任务组草案](https://datatracker.ietf.org/doc/draft-irtf-cfrg-argon2/)的引用/释义。

> **Argon2d** 使用依赖数据的内存访问，这使得它很适合用于加密数字货币和工作量证明的应用程序，而不会受到侧信道定时攻击的威胁。**Argon2i** 使用与数据无关的内存访问，这是密码哈希的首选方法。**Argon2id** 在内存第一次迭代的前半部分充当 Argon2i，其余部分则充当 Argon2d。因此，基于时间 —— 空间的平衡，它既提供了侧信道攻击保护也节约了暴力开销。Argon2i 对内存进行了更多的传递，以防止权衡攻击的发生。

![来源： news.mit.edu](https://blog-private.oss-cn-shanghai.aliyuncs.com/20200309210325.png)

如果你担心侧信道攻击（例如：[恶意数据缓存加载/幽灵漏洞](https://meltdownattack.com/)，它允许通过基于缓存的侧信道读取同一硬件上其他正在运行的进程的私有内存数据），你应该使用 Argon2i，否则使用 Argon2d。
如果你不确定或你对混合方法感到满意，你可以使用 Argon2id 来获得两个方面的优势。

## 结论

在 2019 年，我建议你以后**不要使用** PBKDF2 或 BCrypt，并强烈建议将 Argon2（最好是 Argon2id）用于最新系统。

Scrypt 是当 Argon2 不可用时的不二选择，但要记住，它在侧侧信道泄露方面也存在相同的问题。
