---
title: Git常用操作总结
date: 2022-02-28 16:58:57
tags: Git
index_img: /img/page/git.png
---

## 批量删除Git分支

### 删本地分支

git branch 输出分支列表， 通过管道过滤出包含关键词 keyname分支名的分支，传递给git branch -D 本地分支强制删除命令中，实现**批量删除包含keyname字符串的本地分支**

```
git branch |grep 'keyname' | xargs git branch -D
```

#### 删远程分支

git branch 输出分支列表， 通过管道过滤出包含关键词 keyname分支名的分支，传递给git push origin --delete 远端分支强制删除命令中，实现**批量删除包含keyname字符串的远端分支**

```
git branch |grep 'keyname' | xargs git push origin --delete
```

## 误删分支找回

git reflog查看所有分支的commit记录,根据commit记录创建分支

```
git branch newBranchName HEAD@{**}
```

切换到新分支后，push到远端即可恢复远端分支

![找回分支](/img/findback-branch.png)

## Git快捷命令配置（别名）

git bash窗口中vi /etc/bash.bashrc，在尾部添加如下代码

```
alias gst='git status'
alias gdiff='git diff'
alias gadd='git add'
alias gci='git commit -m "hello world"'
alias gthis='git rev-parse --abbrev-ref HEAD'
alias gpush='git push origin `gthis`'
alias gco='git checkout'
alias gbr='git branch'
alias gname='vi /etc/bash.bashrc'
```

保存后重启bash窗口，生效快捷命令映射

若保存时，遇到[/etc/bashrc Permission denied，则用管理员权限打开git bash重试即可

![管理员权限运行bash](/img/bash-run.png)

## 记录Git用户名密码

git 默认每次pull等操作时要求输入密码，比较麻烦，可通过如下命令永久保存密码，再输入一次后就无需输入了。

```
git config credential.helper store
```



