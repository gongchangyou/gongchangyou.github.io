---
layout: post
title: "git 常用命令"
date: 2013-08-13 16:25:06 +0800
comments: true
category: git
tag: [git]
---

<h3>git ignore</h3>
不想上传的文件 除了首次建立仓库可以修改.gitignore文件以外，还可以如下命令

% git update-index –assume-unchanged /path/to/file  无视这个文件上的修改 不上传

% git update-index –really-refresh /path/to/file

% git update-index –skip-worktree /path/to/file 不更新这个文件

git update-index –no-assume-unchanged


<h3>git patch</h3>
<ul>
<h4>生成patch:</h4>
<li>
1.先commit
</li>
<li>	
2.git format-patch version/HEAD^
</li>
<h4>使用patch:</h4>
git am XXX.patch
</ul>

<h3>git 删除所有tag</h3>
git tag |xargs git tag -d


<h3>git cherry-pick</h3>
git cherry-pick c2f3d52e7^..d0c9463f4

注意 “..”前面的那个hash是不被包括在内的，如果要c2f3d52e7这个commit 那么后面要跟上^表示之前的一个提交。

<h3>git 添加文件到最近的一次commit中</h3>
漏掉了一些文件，但是已经提交了怎么办？

git add forgotten_files

git commit -\-amend

<h3>在A branch查看 B branch的log</h3>
有时候merge 中出现conflict 又想看看各个branch中这个文件的修改记录

只需要 git log -p branch_name path/to/file

<h3>git diff 无视win和macos 换行的</h3>
git config –global core.autocrlf true

<h3>git 查找 注释 文本</h3>
git log -S “test”  
git log -\-grep=“test”

<h3>git st 中文乱码问题</h3>
git config -\-global core.quotepath false

<h3>git 将tag push到远程</h3>
git push origin -\-tags

<h3>查看远程标签</h3>
git ls-remote -\-tags

<h3>删除远程标签</h3>
git push origin -\-delete tag v0.1
或 git push origin :refs/tags/v0.1

<h3>删除本地标签</h3>
git tag -d [标签名]
