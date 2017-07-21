---
layout: post  
title: Mysql执行计划  
category: program  
tags: [mysql]  

--- 

#Explain语法

执行计划包含的信息如下：

|id|select_type|table|type|possible_keys|key|key_len|ref|rows|Extra|  
|:--|-----------|-----|----|-------------|---|-------|---|----|--:|  

##ID

包含一组数字，表示查询中执行select子句或操作表的顺序。ID相同，顺序执行；ID不同，id值越大优先级越高，越先被执行  

|id|select_type|table|type|possible_keys|key|key_len|ref|rows|Extra|  
|:--|----------|-----|----|-------------|---|-------|---|----|--:|   
| 1 |  SIMPLE  |  a  |index|   NULL     |PRIMARY| 4 |NULL|221|Using index|  
| 1 |  SIMPLE  |  b  | ALL| NULL        |NULL|NULL  |NULL|57544|Using join buffer (flat, BNL join)|  

##select_type

| id | select_type | desc 
|----|:------------ | :------ | 
| 1  |    SIMPLE   | 查询中不包含子查询或者UNION 
| 2  | PRIMARY     | 查询中若包含任何复杂的子部分，最外层查询则被标记为：PRIMARY
| 3  | SUBQUERY    |在SELECT或WHERE列表中包含了子查询，该子查询被标记为：SUBQUERY
| 4  | DERIVED     |在FROM列表中包含的子查询被标记为：DERIVED（衍生）
| 5  | UNION       |若第二个SELECT出现在UNION之后，则被标记为UNION；若UNION包含在  FROM子句的子查询中，外层SELECT将被标记为：DERIVED
| 6  | UNION RESULT|从UNION表获取结果的SELECT被标记为：UNION RESULT

##type

表示MySQL在表中找到所需行的方式，又称“访问类型”，常见类型如下：

all|index|range|ref|eq_ref|const,system|NULL
---|-----|-----|---|------|------------|----

从左至右，由最差到最好。

* all:全表扫描，MySql将遍历全表以找到匹配的行；
* index:全索引扫描，index与all区别为index类型只遍历索引树；
* range:索引范围扫描，对索引的扫描开始于某一点，返回匹配值域的行，常见于between、<、>等查询；
* ref:非唯一性索引扫描，返回匹配某个单独值的所有行。常见于使用非唯一索引即唯一索引的非唯一前缀进行的查找；
* eq_ref:唯一性索引，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描
* const、system：当mysql对查询某部分进行优化，并转换为一个常量时，使用这些类型访问。如将主键置于where列表中，mysql就能将该查询。system是const类型的特例，当查询的表只有一行的情况下，使用system
* NULL：MySql在优化过程中分解语句，执行时甚至不用访问表或索引

##possible_keys

指出MySQL能使用哪个索引在表中找到行，查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询使用

##key

显示MySQL在查询中实际使用的索引，若没有使用索引，显示为NULL。查询中若使用了覆盖索引，则该索引仅出现在key列表中。

##key_len

表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。key_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检索出的

##ref

表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值

##rows

表示MySQL根据表统计信息及索引选用情况，估算的找到所需的记录所需要读取的行数

##Extra

包含不适合在其他列中显示但十分重要的额外信息







