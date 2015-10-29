---
layout: post
title: Mac上安装生物信息工具samtools
category: program
tags: [samtools,biosoft]
---

在生物信息分析工作中，经常会遇到序列格式的转换，SAM格式和BAM格式的转换。所以我们需要安装samtools工作。但是在MAC下面安装和windows下面有些不同。这里我们使用brew进行安装。
具体安装步骤如下：

```
brew tap homebrew/science
brew install samtools
```

这里和普通的安装不同，我们使用brew安装wget等类似命令时，直接使用`brew install wget`即可。但是安装samtools需要执行前面的命令。
