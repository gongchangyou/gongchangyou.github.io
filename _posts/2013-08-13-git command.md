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

