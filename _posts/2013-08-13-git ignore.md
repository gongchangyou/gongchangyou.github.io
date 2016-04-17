---
layout: post
title: "git ignore"
date: 2013-08-13 16:25:06 +0800
comments: true
category: git
tag: [git]
---

不想上传的文件 除了首次建立仓库可以修改.gitignore文件以外，还可以如下命令

% git update-index –assume-unchanged /path/to/file  无视这个文件上的修改 不上传

% git update-index –really-refresh /path/to/file

% git update-index –skip-worktree /path/to/file 不更新这个文件

git update-index –no-assume-unchanged