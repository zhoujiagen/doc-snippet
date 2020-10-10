# MySQL Explain


- [MySQL 5.7 Explain Output Format](https://dev.mysql.com/doc/refman/5.7/en/explain-output.html)

## 输出列

|输出列|名称|含义|
|:---|:---|:---|
|id|select_id|SELECT标识|
|select_type|-|SELECT类型|
|table|table_name|输出行中引用的表|
|partitions|partitions|匹配的分区|
|type|access_type|连接类型/访问类型|
|possible_keys|posible_keys|可能选择的索引|
|key|key|实际选择的索引|
|key_len|key_length|选择的键的长度|
|ref||ref|与索引比较的列|
|rows|rows|需要检查的行的估计|
|filtered|filtered|按表条件过滤出的行百分比|
|Extra|-|额外信息|


## id

查询中SELECT的序列号, 如果行引用了其他行的UNION结果, 该值为NULL.

## select_type

|select_type|含义|
|:---|:---|
|SELECT|简单的SELECT, 没有使用UNION或子查询.|
|PRIMARY|最外层的SELECT.|
|UNION|UNION中第二个或后续的SELECT语句.|
|DEPENDENT UNION|UNION中第二个或后续的SELECT语句, 依赖于外层查询.|
|UNION RESULT|UNION的结果.|
|SUBQUERY|子查询中第一个SELECT.|
|DEPENDENT SUNQUERY|子查询中第一个SELECT, 依赖于外层查询.|
|DERIVED|生成的表.|
|MATERIALIZED|物化的子查询.|
|UNCACHEABLE SUBQUERY|不可缓存的子查询: 子查询的结果不能被缓存, 对外层查询的每行必须重新计算.|
|UNCACHEABLE UNION|属于不可缓存的子查询的UNION中第二个或后续SELECT.|

## table

- 输出行中引用的表
- `<unionM,N>`: id值M和N的行的并
- `<derivedN>`: id值N的行的生成表结果, 生成表可以是FROM子句中子查询生成的.
- `<subqueryN>`: id值N的物化子查询的结果.

## partitions

匹配查询的记录的分区.

## type

访问类型/连接类型, 由最佳到最差排列:


|select_type|含义|
|:---|:---|
|system|表只有一行. 作为const的特例.|
|const|表最多只有一行匹配, 该行在查询开始时读取.|
|eq_ref|与前面的表组合时, 从该表中读取一行. 当索引整体被用于连接, 且索引是主键或非空唯一索引时.|
|ref|与前面的表组合时, 从该表中读取所有匹配索引值的行. 当连接使用了键的前缀部分或键不是主键/唯一索引时.|
|fulltext|连接使用了全文索引.|
|ref_or_null|与ref类似, 但额外搜索包含NULL值的行.|
|index_merge|使用索引合并优化.|
|unique_subqury|完整替换了IN子查询的唯一索引查找.|
|index_subqury|完整替换了IN子查询的非唯一索引查找.|
|range|检索出在特定范围内的行, 使用索引选择行. ref列为空. 当列与常量的比较操作符是`=, <>, >, >=, <, <=, IS NULL, BETWEEN, LIKE , IN()`时.|
|index|与ALL类似, 但扫描索引树: 使用了覆盖索引时, 或者按索引序执行全表扫描.|
|ALL|与前面的表组合时, 执行全表扫描.|


- 索引合并优化: 合并单个表的索引扫描, 将有多个范围扫描的结果行合并.

## possible_keys

可以选择用于查找表中行的索引.

## key

实际选择用于查找表中行记录的索引.

## key_len

使用的键的长度, 用于确定多部分键中用了哪些部分.

## ref

显示那些列或常量用于与key列中索引比较来选择表中的行.

## rows

执行查询需要检查的行数.

## filtered

按表条件将过滤出的表行数的百分比估计. 最大值为100, 表示没有过滤出现的行. rows X filtered表示将于后续表连接的行数.

## Extra

解析查询的额外信息.


- Child of 'table' pushed join@1

NDB中下推连接时使用.

- const row not found

查询结果为空.

- deleting all rows

MyISAM支持简单快速的删除所有表行.

- Distinct

检索不同的值, 已找到第一个匹配的行后不再检索其他行.

- FirstMatch(tbl_name)

半连接FirstMatch连接短路策略.

- Full scan on NULL key

子查询优化时无法使用索引查询的退化方法.

- Impossible HAVING

HAVING子句总为false.

- Impossible WHERE

WHERE子句总为false.

- Impossible WHERE noticed after reading const tables

读取了所有const和system表, 发现WHERE子句总为false.

- LooseScan(m..n)

使用半连接LiiseScan策略.

- No matching min/max row

查询`SELECT MIN(...) FROM ... WHERE condition`无法满足.

- no matching row in const table

连接中的查询是一个空表或表中行不满足唯一性索引条件.

- No matching rows after partition pruning

分区剪枝后, DELETE或UPDATE时没有匹配的行.

- No tables used

没有FROM子句, 或有FROM DUAL子句.

- Not exists

发现一行满足LEFT JOIN条件时, 不做额外的处理.

- Plan isn't ready yet

EXPLAIN FOR CONNECTION时, 优化器未完成创建语句的执行计划.

- Range checkef for each record (index map: N)

没有找到合适的索引, 但发现前面的表已知时, 可以使用range或index_merge检索前面的表行.

- Scanned N databases

查询INFORMATION_SCHEMA是扫描了多少个目录.

- Select tables optimized away

在优化阶段可以读出所需的表行, 执行阶段不需要读取.

- Skip_open_table, Open_frm_only, Open_full_table

查询INFORMATION_SCHEMA的文件打开优化.

- Start temporary, End temporary

使用了临时表处理半连接去重策略.

- unique row not found

没有行满足表的唯一性索引或主键的条件.

- Using filesort

执行额外的一趟以按序排列检索出的行.

- Using index

从索引中读出查询的列, 不需要磁盘访问实际的表行.

- Using index condition

先访问索引, 根据索引确定是否要读取所有表行.

- Using index for group-by

类似于Using index, 可以使用索引检索出GROUP BY或DISTINCT查询中所有列, 不需要磁盘访问实际的表行.

- Using join buffer(Block Nested Loop), Using join buffer(Batched Key Access)

之前连接的表被部分读取到连接缓冲中, 用于执行与当前表的连接.

- Using MRR

使用多范围读优化(Multi-Range Read Optimization, MRR): 先只扫描索引, 再按主键顺序访问表行.

- Using sort_union(...), Using union(...), Using intersect(...)

index_merge连接时使用的算法.

- Using temporary

为解析查询, 需要创建临时表以持有结果.

- Using where

使用`WHERE`子句限制与下一个表匹配或发送给客户端的行数.

- Using where with pushed condition

NDB Cluster使用条件下推优化.

- Zero limit

语句中有`LIMIT 0`
