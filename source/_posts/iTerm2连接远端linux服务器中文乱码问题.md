---
title: iTerm2连接远端linux服务器中文乱码问题
date: 2018-03-31 11:41:36
tags:
    - Mac
    - iTerm2
    - 中文乱码
    - oh-my-zsh
---

**这个问题一般是因为终端和服务端的字符集不匹配，Mac下默认的是utf8字符集**

*因为我在本地和服务端都是用zsh替代了bash，而且使用了oh-my-zsh，而默认的.zshrc没有设置utf-8编码，所以本地和服务端都要在.zshrc文件中设置，设置步骤如下*

* 1.在MAC终端下修改字符集

  * 如果是zsh

    `vi ~/.zshrc`

  * 如果是bash

    `vi ~/.bashrc`

  <!--more-->

* 2.在文件中添加

  ```
  export LC_ALL=en_US.UTF-8
  export LANG=en_US.UTF-8
  ```

* 3.重启终端，或者输入重新加载配置文件

  `source ~/.zshrc` 或者`source ~/.bashrc`