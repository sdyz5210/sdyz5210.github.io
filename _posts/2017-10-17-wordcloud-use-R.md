---
layout: post  
title: 使用R语言进行分词，画词云
date: 2017-10-17 
tags: R
--- 


### 环境安装问题

之前安装了解过R的安装，因为要用到分词，所以需要安装rJava和Rwordseg两个包，rJava可以安装成功，但是第二个包一直报错，问了度娘和谷歌都没能解决，最后协助原有的R统一安装最新版的R，解决该问题。具体安装不啰嗦了，网上很多。

### 词云包

以下的代码使用的是wordcloud版本，没有使用wordcloud2，两个在用法还是有区别的，没有具体研究wordcloud2的使用。

### 例子说明

本例子来自学校的R语言和统计分析的论文的作业，老师要求做一个小例子。例子是基于企业的工商注册数据进行分析，同时增加了每企业的SEO的关键词数据，结合公司的经营范围、搜索关键词、SEO关键词进行分词分析。最终形成的效果如图：

![经营范围](/images/posts/R_wordcloud_1.png)

![搜索关键词](/images/posts/R_wordcloud_2.png)

![SEO关键词](/images/posts/R_wordcloud_3.png)

### R程序


```
R.version
#调入分词的库
library("rJava")
library("Rwordseg")

#调入绘制词云的库
library("RColorBrewer")
library("wordcloud")
#library("wordcloud2")

#读入数据(特别注意，read.csv竟然可以读取txt的文本)
#这里只读取文件的第十列进行操作
myfile<-read.csv(file.choose(),header=TRUE,colClasses=c("NULL", "NULL","NULL","NULL","NULL","NULL","NULL","NULL","NULL", "character","NULL"))

#预处理，这步可以将读入的文本转换为可以分词的字符，没有这步不能分词
myfile.res <- myfile[myfile!=" "]


#装载分词词典（如果需要的话，我这里没有装载，下面有说明）

#分词，并将分词结果转换为向量
myfile.words <- unlist(lapply(X = myfile.res,FUN = segmentCN))

#剔除URL等各种不需要的字符，还需要删除什么特殊的字符可以依样画葫芦在下面增加gsub的语句
myfile.words <- gsub(pattern="http:[a-zA-Z\\/\\.0-9]+","",myfile.words)
myfile.words <- gsub("\n","",myfile.words)
myfile.words <- gsub("　","",myfile.words)
myfile.words <- gsub("有限公司","",myfile.words)

#去掉停用词
data_stw=read.table(file=file.choose(),colClasses="character")
stopwords_CN=c(NULL)
for(i in 1:dim(data_stw)[1]){
  stopwords_CN=c(stopwords_CN,data_stw[i,1])
}
for(j in 1:length(stopwords_CN)){
  myfile.words <- subset(myfile.words,myfile.words!=stopwords_CN[j])
}
#过滤掉1个字的词
myfile.words <- subset(myfile.words, nchar(as.character(myfile.words))>1)

#统计词频
myfile.freq <- table(unlist(myfile.words))
myfile.freq <- rev(sort(myfile.freq))
myfile.freq <- data.frame(word=names(myfile.freq), freq=myfile.freq);

#按词频过滤词，过滤掉只出现过一次的词，这里可以根据需要调整过滤的词频数
myfile.freq2=subset(myfile.freq, myfile.freq$freq.Freq>=2)

write.csv(myfile.freq2, file = "/Users/mac/Downloads/beihang/keyword.txt", row.names = F, quote = F,fileEncoding="UTF-8")

#绘制词云
#设置一个颜色系：
mycolors <- brewer.pal(8,"Dark2")
#设置字体
par(family=("微软雅黑"))
#画图
wordcloud(myfile.freq2$word,myfile.freq2$freq.Freq,random.order=FALSE,random.color=FALSE,ordered.colors=F,colors=mycolors)
#wordcloud2(myfile.freq2$word,myfile.freq2$freq.Var1)

```