---
title: 如何在macOS Mojave中恢复无法使用的Safari扩展
date: 2018-10-03 12:11:48
tags:
    - macOS Mojave
    - safari extensions
---

与 Chrome 一样，Safari 也有不少扩展。你可以在 Safari 浏览器、Mac App Store 和开发者网站下载所需的扩展。不过在升级 macOS Mojave 后，你可能会发现，文中介绍的扩展已经无法继续使用。

![Safari 扩展无法使用](https://cdn.sspai.com/2018/09/27/60d3de825c57e9ea8740159e32449943.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

<!-- more -->

Safari 扩展无法使用

这一问题源于与 macOS Mojave 一同发布的 Safari 12。**新版的 Safari 不再支持未经苹果官方审核的扩展，并启用了全新的扩展 API。**相比原有的 API，这一新的 API 减少了扩展可以访问的内容。这也就意味着，一些扩展即使通过了官方审核，也可能无法实现原有的功能。

不过，这也不意味着我们只能放弃这些扩展。借助本文中的方法，你可以继续像往常一样使用它们。

首先，我们需要定位到已安装的扩展。打开访达后，点击菜单栏中的「前往」，按住 `⌥option`后，选择「资源库」。

![img](https://cdn.sspai.com/2018/09/27/52d0f200313910cca5f5549a3f8fee9d.gif?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

在打开的窗口中，依次选择「Safari - Extensions」。在这一文件夹中，将你希望使用的扩展拖到桌面。为了方便接下来的操作，你可以将它们存放在一个文件夹中。

![img](https://cdn.sspai.com/2018/09/27/0c283a8cf1be49ce1e0b029ba84b9894.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

完成这一步骤后，打开终端 App，输入 `cd `。然后，将刚刚创建的文件夹拖到终端的窗口，按下回车键，即可让终端定位到目标位置。如果你在上一步中没有创建文件夹，也可以参考少数派之前的[文章](https://sspai.com/post/45668) ，在访达窗口上显示当前路径，并将其输入至终端。

![img](https://cdn.sspai.com/2018/09/27/ff856b0dc0f39185692bbd65af9d9e22.gif?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

随后，在终端中输入 `xar -xf 扩展的名称`，比如 `xar -xf NoMoreiTunes.safariextz`，按下回车键。扩展所在的位置将产生一个与该扩展的名称相同的文件夹。你需要对每一个扩展重复上述的操作。

![img](https://cdn.sspai.com/2018/09/27/60c6776117431646ae0e0f22036cc312.gif?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

完成这一步骤后，打开 Safari，在「偏好设置 - 高级」中确认勾选了「在菜单栏中显示『开发』菜单」一项。

![img](https://cdn.sspai.com/2018/09/27/cb27455db6aa1a94dc21c0470b0985a7.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

然后，依次点击菜单栏中的「开发 - 显示扩展构建器」。初次打开时，系统会弹窗要求确认，点击「继续」即可。

![img](https://cdn.sspai.com/2018/09/27/b088b9db43d2852ee18fd03d6e714950.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

在打开的扩展构建器窗口中，点击左下角的「+」，选择「添加扩展」。选中并添加由终端生成的、以扩展名命名的文件夹即可。

![img](https://cdn.sspai.com/2018/09/27/495c71da4278af7ff929345924ef1e72.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

添加完毕后，点击「运行」，输入密码，就完成了这一扩展的安装。之后，你就可以在 Safari 的偏好设置中正常地启用该扩展。

![img](https://cdn.sspai.com/2018/09/27/643d77e9cbd9e6238d5c4353370437a6.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

不过，这一解决方案也并非完美。首先，**未经苹果审核的扩展可能存在着安全风险**。因此，建议你仅通过这一方式恢复你已经长期使用、可以信赖的扩展，而不是用来继续安装新的未审核扩展。此外，这只是一个**临时性的方法**，究竟能够有效多久还是未知数。而且，每次系统升级后你都可能需要重新进行上述操作。

🔗 参考文章：《[Give new life to old extensions in Safari 12](https://sixcolors.com/post/2018/09/give-new-life-to-old-extensions-in-safari-12/)》

文章转自于：少数派，原文地址：[链接](https://sspai.com/post/47303)。

如果个人博客中涉及他人文章我会尽力注明出处，但受限于能力并不能保证所有引用之处均能够注明出处，如有冒犯，请您及时邮件 (todaycoder@163.com)告知以便修改，并于此提前向您道歉。