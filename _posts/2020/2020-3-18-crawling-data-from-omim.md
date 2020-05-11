---

layout: post  
title: 抓取omim数据库数据
date: 2020-03-18
tags: [python,scrapy]

---



# 简介

OMIM 全称叫做Online Mendelian Inheritance in Man， 是一个不断更新的人类孟德尔遗传病的数据库，这个数据库主要关注人类基因变异和表型性状之间的关系。

OMIM官网网址为：`https://www.omim.org/`

![](/images/omim-20200318.png)



OMIM的注册用户可以下载或者使用API获取数据。这里我们尝试使用爬虫来抓取**Phenotype-Gene Relationships**数据。

# 使用scrapy抓取数据

## 创建项目

```
scrapy startproject omimScrapy
cd omimScrapy
scrapy genspider omim omim.org
```

## 配置item信息

```

import scrapy

class OmimscrapyItem(scrapy.Item):
    # define the fields for your item here like:
    geneSymbol = scrapy.Field() 
    mimNumber = scrapy.Field()
    location = scrapy.Field()
    phenotype = scrapy.Field()
    phenotypeMimNumber = scrapy.Field()
    nheritance = scrapy.Field()
    mappingKey = scrapy.Field()
    descriptionFold = scrapy.Field()
    diagnosisFold = scrapy.Field()
    inheritanceFold = scrapy.Field()
    populationGeneticsFold = scrapy.Field()
```

## 制作爬虫

我们是通过mim2gene.txt这个文件的内容依次抓取，所以需要对对该文件进行解析。

```
    '''
        解析omim mim2gene.txt的文件
    '''
    def readMim2Gene(self,filename):
        filelist = []
        with open(filename,"r") as f:
            for line in f.readlines():
                tempList = []
                strs = line.split()
                mimNumber = strs[0]
                mimEntryType = strs[1]
                geneSymbol = "."
                if(len(strs)>=4):
                    geneSymbol = strs[3]
                if(mimEntryType in ["gene","gene/phenotype"]):
                    tempList.append(mimNumber)
                    tempList.append(mimEntryType)
                    tempList.append(geneSymbol)
                    filelist.append(tempList)     
        return filelist
```

解析该文件后，爬虫抓取的入口需要动态的生成。我们需要通过`start_requests`方法来动态生成抓取的url。然后基于该url进行抓取，获取相应的内容。

注意：此阶段可以同时对html内容进行解析，抽取所需要的内容，也可以先把html内容保存起来，后续统一处理。此处没有对抓取到的html内容进行解析，而是直接把html内容保存成html文件，文件名以`mimNumber`来命名，后缀后`.html`。

```
# -*- coding: utf-8 -*-
import scrapy
from bs4 import BeautifulSoup
from omimScrapy.items import OmimscrapyItem

class OmimSpider(scrapy.Spider):
    name = 'omim'
    allowed_domains = ['omim.org']
    #start_urls = ['http://omim.org/']

    def start_requests(self):
        filelist = self.readMim2Gene("mim2gene.txt")
        for row in filelist:
            item = OmimscrapyItem()
            item['mimNumber'] = row[0]
            item['geneSymbol'] = row[2]
            url = "https://www.omim.org/entry/"+row[0]
            yield scrapy.Request(url,method='GET',callback=self.saveHtml,meta={'item':item})

    def saveHtml(self, response):
        item = response.meta['item']
        html = response.body.decode("utf-8")
        with open("/root/data/entry/"+item['mimNumber']+".html",'w+') as f:
            f.write(html)
            f.flush()
```

## 爬虫设置

[OMIM robots.txt](https://omim.org/robots.txt) 设置了爬虫策略，只允许 微软必应 bingbot 和谷歌 googlebot 爬虫获取指定路径内容。主要关注一下几个方面的配置即可。

```
BOT_NAME = 'bingbot'

# Crawl responsibly by identifying yourself (and your website) on the user-agent
USER_AGENT = 'bingbot (+https://www.bing.com/bingbot.htm)'

# Obey robots.txt rules
ROBOTSTXT_OBEY = True

# Configure a delay for requests for the same website (default: 0)
DOWNLOAD_DELAY = 4

# Disable cookies (enabled by default)
COOKIES_ENABLED = False
```

## 执行

接下来就可以执行抓取操作了，这个过程比较慢，估计需要一天的时间。之后所有的html页面即被保存成本地的html页面了。

```
scrapy crawl omim
```

## 后续提取

基于本地的html的提取操作就很简单了，可以使用`BeautifulSoup`来进行提取。提取的核心操作如下：

```
'''
    解析Phenotype-Gene Relationships表格
'''
def parseHtmlTable(html):
    soup = BeautifulSoup(html,"html.parser")
    table = soup.table
    location,phenotype,mimNumber,nheritance,mappingKey,descriptionFold,diagnosisFold,inheritanceFold,populationGeneticsFold="","","","","","","","",""
    if not table:
        result = "ERROR"
    else:
        result = "SUCCESS"
        trs = table.find_all('tr')
        for tr in trs:
            tds = tr.find_all('td')
            if len(tds)==0:
                continue
            elif len(tds)==4:
                phenotype = phenotype + "|" + (tds[0].get_text().strip() if tds[0].get_text().strip()!='' else '.' )
                mimNumber = mimNumber + "|" + (tds[1].get_text().strip() if tds[1].get_text().strip()!='' else '.')
                nheritance = nheritance + "|" + (tds[2].get_text().strip() if tds[2].get_text().strip()!='' else '.')
                mappingKey = mappingKey + "|" + (tds[3].get_text().strip() if tds[3].get_text().strip()!='' else '.')
            elif len(tds)==5:
                location = tds[0].get_text().strip() if tds[0].get_text().strip()!='' else '.'
                phenotype = tds[1].get_text().strip() if tds[1].get_text().strip()!='' else '.'
                mimNumber = tds[2].get_text().strip() if tds[2].get_text().strip()!='' else '.'
                nheritance = tds[3].get_text().strip() if tds[3].get_text().strip()!='' else '.'
                mappingKey = tds[4].get_text().strip() if tds[4].get_text().strip()!='' else '.'
            else:
                result = "ERROR"
        
        descriptionFoldList = soup.select("#descriptionFold")
        descriptionFold = "." if len(descriptionFoldList)==0 else descriptionFoldList[0].get_text().strip()
        
        diagnosisFoldList = soup.select("#diagnosisFold")
        diagnosisFold = "." if len(diagnosisFoldList)==0 else diagnosisFoldList[0].get_text().strip()
        
        inheritanceFoldList = soup.select("#inheritanceFold")
        inheritanceFold = "." if len(inheritanceFoldList)==0 else inheritanceFoldList[0].get_text().strip()
        
        populationGeneticsFoldList = soup.select("#populationGeneticsFold")
        populationGeneticsFold = "." if len(populationGeneticsFoldList)==0 else populationGeneticsFoldList[0].get_text().strip()
```

至于最终保存成什么格式，就看个人需要了。