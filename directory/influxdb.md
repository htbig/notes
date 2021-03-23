# influxdb结构体
* series
```
type Series struct {
	Name string
	Tags Tags
	id uint64
}
type Tags struct{
	id string
	m map[string]string
}
Series是measurement（类似表）和tags（类似数据库里索引的列）的组合，tags是tag key 和tag value的map,然后id是tag key和value编码得到.
series是共同retention policy，measurement和tag set的集合
```
* Row
```
type Row struct{
	Time int64
	Series Series
	Values []interface{}
}
Row表示查询结果中每一行，其中Values表示返回Fields的集合
```
* 在InfluxDB中，timestamp标识了在任何给定数据series中的单个点。就像关系型数据库中的主键
* point：point是具有相同timestamp、相同series（measurement，rp，tag set相同）的field。这个点在此时刻是唯一存在的。 相反，当你使用与该series中现有点相同的timestamp记将新point写入同一series时，该field set将成为旧field set和新field set的并集,类似于行
* influxdb中不能没有field，可以没有tag
* retention policy：单个measurement可以有不同的retention policy
* series cardinality
在InfluxDB实例上唯一database，measurement和tag set组合的数量,measurement有两个tag key：email和status。如果有三个不同的email，并且每个email的地址关联两个不同的status，那么这个measurement的series cardinality就是6(3*2=6),在某些情况下，简单地执行该乘法可能会高估series cardinality。 依赖的tag是由另一个tag限定的tag并不增加series cardinality。 如果我们将tagfirstname添加到上面的示例中，则系列基数不会是18（3 * 2 * 3 = 18）。 它将保持不变为6，因为firstname已经由email覆盖了
* shard
shard包含实际的编码和压缩数据，并由磁盘上的TSM文件表示。 每个shard都属于唯一的一个shard group。多个shard可能存在于单个shard group中。每个shard包含一组特定的series。给定shard group中的给定series上的所有点将存储在磁盘上的相同shard（TSM文件）中。
* shard duration决定了每个shard group跨越多少时间。具体间隔由retention policy中的SHARD DURATION决定。
例如，如果retention policy的SHARD DURATION设置为1w，则每个shard group将跨越一周，并包含时间戳在该周内的所有点。
shard group是shard的逻辑组合。shard group由时间和retention policy组织。包含数据的每个retention policy至少包含一个关联的shard group。给定的shard group包含shard group覆盖的间隔的数据的所有shard。每个shard group跨越的间隔是shard的持续时间
* GROUP BY子句后面可以跟用户指定的tags或者是一个时间间隔
GROUP BY *：对结果中的所有tag作group by。

GROUP BY <tag_key>：对结果按指定的tag作group by。

GROUP BY <tag_key>,<tag_key>：对结果数据按多个tag作group by，其中tag key的顺序没所谓。
* ORDER BY TIME DESC
默认情况下，InfluxDB以升序的顺序返回结果; 返回的第一个点具有最早的时间戳，返回的最后一个点具有最新的时间戳。 ORDER BY time DESC反转该顺序，使得InfluxDB首先返回具有最新时间戳的点
