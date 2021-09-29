# IOTDB操作指南

## 操作相关

```sql
//显示版本
show version

//新建存储组
SET STORAGE GROUP TO root.test

//显示已经创建的存储组
SHOW STORAGE GROUP

//删除存储组
delete storage group root.test

//创建时间序列
create timeseries root.ln.wf01.wt01.s0 with datatype=BOOLEAN,encoding=PLAIN

//删除时间序列
delete timeseries root.ln.wf01.wt01.status
delete timeseries root.ln.wf01.wt01.*

//修改时间序列
alter timeseries root.test.wf01.wt01.s0 DROP tag1, tag2
alter timeseries root.test.wf01.wt01.s0 SET tag1=t3, attr1=a3


//显示时间序列
show timeseries root.kafkadata

//模糊查询设备下的序列
show timeseries root.kafkadata.t*

//加条件的查询时间序列
show timeseries root.kafkadata where tag1= t1

//统计时间序列
count timeseries root.kafkadata.*

//按照层级进行统计
count timeseries root.kafkadata group by level = 1

//统计存储组对应的层级下有几个节点
count nodes root.kafkadata level=2

//查看存储组下的设备
show devices root.test

//显示某一层下的子路径
show child paths root.ln
```

## CRUD

```sql
//插入记录
insert into root.kafkadata(timestamp,name) values(1,"txt")

//删除记录
delete from root.kafkadata where time < now()

//查询记录
select temperature from root.kafkadata where time > 0 and time < 10
select count(*) from root.kafakadata
select max_value(temperature) from root.ln.wf01.wt01
select sum(temperature) from root.ln.wf01.wt01
select avg(temperature) from root.ln.wf01.wt01

//排序
select * from root.kafaka order by time desc limit 10

//分组
//   每隔5秒统计一次name的个数
select count(name) from root.kafkadata group by([1,100000],5s)
//   每隔1分钟，前半分钟有数据个数
select count(name) from root.kafkadata group by([1,100000],30s,1m)

//分页
select * from root.kafaka limit 3 offset 0

//选择最后一条记录
select last temperature from root.kafakadata
```



