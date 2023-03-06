---
title: shell语法总结（1）
date: 2022-05-14 21:35:11
tags: Linux
index_img: /img/page/shell.png
---

参考资料：[https://www.cnblogs.com/jingmoxukong/p/7867397.html](https://www.cnblogs.com/jingmoxukong/p/7867397.html)
# 一、简介
> - Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问 Linux 内核的服务。  
> - Shell 脚本（shell script），是一种为 shell 编写的脚本程序，一般文件后缀为 .sh。
# 二、shell语法
### 1. #!指定解释器 
``` shell
# 指定bash解释器
#!/bin/bash  

# 指定sh解释器
#!/bin/sh
```
**注**：不指定，执行的时候会提示没有权限
>Shell 的解释器种类众多，常见的有：

- sh - 即 Bourne Shell。sh 是 Unix 标准默认的 shell。
- bash - 即 Bourne Again Shell。bash 是 Linux 标准默认的 shell。
- fish - 智能和用户友好的命令行 shell。
- xiki - 使 shell 控制台更友好，更强大。
- zsh - 功能强大的 shell 与脚本语言。
### 2. 注释
   - 单行注释：#开头
   - 多行注释：“:<<EOF"开头，”EOF"结尾
### 3. echo：输出
``` shell
# 输出hello，world!
echo "hello, world!"
# \转义字符，输出“hello”
echo "\"hello\""
# 输出到文件test.txt,没有则新建
echo "test" > test.txt
```
**注**：一个echo, 后面自动换行
### 4. printf输出
``` shell
# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出
printf "%s %s %s\n" a b c d
#  Output:
#  a b c
#  d 
# 如果没有参数，那么 %s 用 NULL 代替，%d 用 0 代替
printf "%s and %d \n"
#  Output:
#   and 0
```
**注**：需手动添加换行
### 5. 变量
> 变量无需声明，直接赋值就会创建  
> 访问方式，${变量} 或 $变量
``` shell
dword="hello"  # 声明变量
echo ${dword}  # 输出变量值
# Output: hello

readonly dword # 定义为只读
# dword="bye"  # 如果放开注释，执行时会报错

unset dword    # 删除变量
echo ${dword}
# Output: （空）
```
### 6. 条件语句写在[[]]中
``` shell
x=10
y=20
if [[ ${x} > ${y} ]]; then
   echo "${x} > ${y}"
elif [[ ${x} < ${y} ]]; then
   echo "${x} < ${y}"
else
   echo "${x} = ${y}"
fi
# Output: 10 < 20
```
### 7. Debug
- -f: 禁止文件名展开（globbing）
- -i: 让脚本以 交互 模式运行
- -n: 读取命令，但不执行（语法检查）
- -t: 执行完第一条命令后退出
- -v: 在执行每条命令前，向stderr输出该命令 
- -x: 在执行每条命令前，向stderr输出该命令以及该命令的扩展参数
``` shell
# 写法1
#!/bin/bash -x
#写法2,使用-启用选项，+禁用选项
set -x
```

