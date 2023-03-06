---
title: 高频VIM命令
date: 2021-11-15 11:09:13
tags: Linux
index_img: /img/page/vim.png
---

## VIM命令

#### 查找

+ normal模式下用/
+ 用n查找下一个，N查找上一个
+ 尾部加\c表示大小写不敏感，\C敏感，如/foll\C
+ 按下*，可查找光标所在单词（前后有空白），g\*则可查光标所在字符序列

#### 替换

+ ```
  :{作用范围}s/{目标}/{替换}/{替换标志}
  ```

+ ```
  :%s/foo/bar/gc 
  # %表示作用全文
  替换标志中g表示替换所有，没有表示只替换第一个
  替换标志中c表示需确认
  ```

  

#### 复制粘贴

+ **从windows复制到vim**，需先按a/i/insert键进入插入模式，再右键粘贴，否则在命令模式下将识别为vim命令直至遇到i/a

+ **复制选中区**，光标在复制开始地方按v进入可视模式，结束地方按y复制，直待粘贴处按p粘贴

+ 复制整行，yy

+ 剪切整行，dd

+ 粘贴,p， 粘贴在光标前为P

  

#### 光标跳转

+ 文件首： gg
+ 文件尾： shift + g
+ 文件指定行： 行号（命令行模式）
+ 行首： home键
+ 行尾： end键



#### 查历史命令

窗口中ctrl + R，输入关键词如`git commit`，将出现以该关键词为首的历史命令，通过多次ctrl + R来选择切换历史命令，然后通过右箭头选中命令

![历史命令](/img/linux-cmd.png)

#### tab设置

+ 显示tab  命令模式下`set list`

+ 设置tab为4空格，且自动缩进， /etc/vimrc中添加

  ```
  set ts=4
  set softtabstop=4
  set shiftwidth=4
  set expandtab
  set autoindent
  ```

  