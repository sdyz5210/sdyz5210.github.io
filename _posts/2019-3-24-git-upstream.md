---
layout: post
title: git的frok后远程更新
category: program
tags: [git,github]
---

### 从fork的仓库中更新

```

# 查看远程信息
git remote -v

# 添加远程仓库
git remote add upstream git@github.com:xxx/xxx.git

# 从远程仓库更新到个人库
git fetch upstream

# 本地库合并
git merge upstream/master

```

### 更新远程仓库

` git push origin master `