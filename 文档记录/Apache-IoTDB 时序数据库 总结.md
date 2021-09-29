# Apache-IoTDB 时序数据库 总结

## 一、前言

`IoTDB` 是一款时序数据库，相关竞品有 `Kairosdb`，`InfluxDB`，`TimescaleDB`等，主要使用场景是在**物联网**相关行业，如：车联网、风力发电、地铁、飞机监控等等。

这章解决的问题：

1. 为什么重复造轮子，从物联网行业的数据特点到 `IoTDB` 的发展过程
2. 这个轮子造的怎么样，`IoTDB` 和竞品测试对比

### 时序数据

时序数据是基于时间维度的同一个物体或概念的值构成的一个序列数据。在传统关系型数据库中，例如 `MySQL`，我们通常会放置一个自增的 `Id` 列作为主键标识，如下：

| id   | 人名 | 体温 | 测量时间|
| :--- | :--- | ---- | :------------------ |
| 1    | 张三 | 36.5 | 2020-02-06 9:00:00  |
| 2    | 李四 | 36.7 | 2020-02-06 9:00:00  |
| 3    | 王五 | 35.6 | 2020-02-06 9:00:00  |
| 4    | 张三 | 36.8 | 2020-02-06 11:00:00 |

上面的表结构就是一个时序数据，将表结构做个变形更容易理解：

| 时间戳     | 人名 | 体温 |
| ---------- | ---- | ---- |
| 1580950800 | 张三 | 36.5 |
| 1580950800 | 李四 | 36.9 |
| 1580950800 | 王五 | 36.7 |
| 1580952600 | 张三 | 36.3 |
| 1580958000 | 张三 | 36.9 |

如果把时间作为一个唯一键对齐展示，能够更像时序数据一些，这也是 `IoTDB` 中查询结果的展示方式：

| 时间戳     | 张三 | 李四 | 王五 |
| ---------- | ---- | ---- | ---- |
| 1580950800 | 36.5 | 36.9 | 36.7 |
| 1580952600 | 36.3 | NULL | NULL |
| 1580958000 | 36.9 | NULL | NULL |

### 物联网

物联网的特点是都会存在一个或多个设备，他们以各种各样的形式组织到一起，用来观测或记录同一时间里相同环境所产生的数据。

#### 1.基本存储

一个公司，对外播报北京、天津、上海三地的温度数据，从而实现盈利。

| 时间戳     | 北京 | 天津 | 上海 |
| ---------- | ---- | ---- | ---- |
| 1580950800 | 20.5 | 22.9 | 21.7 |
| 1580952600 | 20.5 | NULL | 22.9 |
| 1580958000 | 20.5 | 21.7 | 22.9 |

#### 2.保证数据质量

数据保证的质量是多方面的，一步一步介绍。

**2.1 更多设备**

首先可以看到上面数据是存在 `NULL` 值的，这个 `NULL` 值有可能是因为当时设备所在的区域停电了，所以并没有办法上报当时的情况，这样客户如果想获取`1580952600` 这个时间戳对应的天津的数据的时候，肯定是拿不到了，所以传统思维上，我们应该增加一个容灾设备，保证一个设备在坏掉、停电、人为损坏等等的情况的时候，依然能够有**数据上报**回来。基于这样的思想，以上的表结构就会变成：

| 时间戳     | 北京 1 | 北京 2 | 天津1 | 天津2 | 上海1 | 上海2 |
| ---------- | ------ | ------ | ----- | ----- | ----- | ----- |
| 1580950800 | 20.5   | 20.9   | 21.7  | 20.9  | 20.7  | 21.7  |
| 1580952600 | 21.5   | 21.0   | NULL  | 21.7  | 21.7  | 21.7  |
| 1580958000 | 22.5   | 22.7   | 22.9  | 22.7  | NULL  | NULL  |

**2.2 更高采样频率**

这时候依然存在问题，` 1580958000` 这一刻两个设备都没有数据，有可能是放置设备的区域同时出现了断网或者断电，这种情况下，我们可以采用提高采集数据的频率或者补传数据来解决(*补传暂不讨论*)。

我们将每天数据分为3组，每组采样3次，间隔为1个小时，假如时间分布为：上午（7、8、9）、中午（12、13、14）、下午（18、19、20）。当增加了采样频率之后，即便某一刻出现了 NULL 数据，我们也可以采用临近时间做为补充。为了方便对应，下表数据中增加时间点列辅助查看。

| 时间点 | 时间戳     | 北京 1 | 北京 2 | 天津1 | 天津2 | 上海1 | 上海2 |
| ------ | ---------- | ------ | ------ | ----- | ----- | ----- | ----- |
| 7点    | 1580943600 | 20.5   | 20.9   | 21.7  | 20.9  | 20.7  | 21.7  |
| 8点    | 1580947200 | 21.5   | 21.0   | NULL  | 21.7  | 21.7  | 21.7  |
| 9点    | 1580950800 | 22.5   | 22.7   | 22.9  | 22.7  | NULL  | NULL  |
| 12点   | 1580961600 | 20.5   | 20.9   | 21.7  | 20.9  | 20.7  | 21.7  |
| 13点   | 1580965200 | 21.5   | 21.0   | NULL  | 21.7  | 21.7  | 21.7  |
| 14点   | 1580968800 | 22.5   | 22.7   | 22.9  | 22.7  | NULL  | NULL  |
| 18点   | 1580983200 | 20.5   | 20.9   | 21.7  | 20.9  | 20.7  | 21.7  |
| 19点   | 1580986800 | 21.5   | 21.0   | NULL  | 21.7  | 21.7  | 21.7  |
| 20点   | 1580990400 | 22.5   | 22.7   | 22.9  | 22.7  | NULL  | NULL  |

可以看到经过各种各样的需求之后，上传的数据是成倍增长的，不难想象如果这个温度数据希望精准的获取到每个县城的温度，那么中国有 2854 个县城 * 2 个温度设备 * 9 条数据 = 1 天产生的数据总量 = 51372 条，那么一个月就是 1541160 条。

### 数据实时性及总量

假如上面的数据我们继续提高频率到每1分钟每个设备上报一次，那么数据量就会成为 2854 * 2 * 60 * 24 = 246585600 条/天。在这样的数据量下，实时插入实时做一些聚合计算，应该传统数据库就有些处理不过来了。

### IoTDB 的前身

某公司在实际业务中，20 万设备保存了 3 年的数据，TB级别的数据使得 Oracle 被拖的根本吃不消。关键的问题点还不仅仅是存量数据大，新增数据依然以非常快的速度在增长。后来公司联系到了 `IoTDB` 的第一批开发者，但是当时的方案还是基于 Cassandra 来做设计，当时规划了 5 台机器的集群，性能刚满足，但随着时间推移设备总量在增加，业务系统的查询请求量在增加。Cassandra 在经过大量的努力之后，最后发现如果再改可能就需要大面积的重构 Cassandra 数据的代码了，最终决定重新设计一个存储方式，来解决物联网场景下的***时序数据高效写入、低延迟读取、高压缩比持久化***。

### 性能对比

测试工具使用的是由清华大学大数据实验室开发的[iotdb-benchmark](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fthulab%2Fiotdb-benchmark)

#### 1.写入性能对比

| 数据集2     | 客户端 | 存储组 | 设备 | 变量 | batchsize | LOOP    | 数据量 | 写入速度(point/s) | 硬盘数据大小 |
| ----------- | ------ | ------ | ---- | ---- | --------- | ------- | ------ | ----------------- | ------------ |
| IoTDB       | 10     | 10     | 10   | 10   | 1000      | 1000000 | 1E+11  | 24750321.93       | 38306092     |
| InfluxDB    | 10     | 10     | 10   | 10   | 1000      | 1000000 | 1E+11  |                   | 304682932    |
| TimescaleDB | 10     | 10     | 10   | 10   | 1000      | 1000000 | 1E+11  | 737689.22         | 1610219064   |

| 数据集1     | 客户端 | 存储组 | 设备 | 变量 | batchsize | LOOP   | 数据量      | 写入速度(point/s) | 硬盘数据大小 |
| ----------- | ------ | ------ | ---- | ---- | --------- | ------ | ----------- | ----------------- | ------------ |
| IoTDB       | 10     | 10     | 10   | 10   | 1000      | 100000 | 10000000000 | 20706345.15       | 3599732      |
| InfluxDB    | 10     | 10     | 10   | 10   | 1000      | 100000 | 10000000000 | 1729907.81        | 30546560     |
| TimescaleDB | 10     | 10     | 10   | 10   | 1000      | 100000 | 10000000000 | 715857            | 161026468    |
| KairosDB    | 10     | 10     | 10   | 10   | 10000     | 10000  | 10000000000 | 24924.97          | 76263380     |

上面一组数据可以看出写入性能高于同款数据库10倍有余，单机写入速度高达到每秒2千万。且硬盘占用是最小的，这在数据比较大的线上业务中，可能每个月会差出来 1 到 2 块硬盘。

#### 2. 查询性能对比

原始数据查询

|             | 客户端 | 存储组 | 设备 | 序列-数据量 | 变量 | 查询点数 | LOOP | 速度(point/s) | AVG     | MIN     |
| ----------- | ------ | ------ | ---- | ----------- | ---- | -------- | ---- | ------------- | ------- | ------- |
| IoTDB       | 10     | 10     | 10   | 1.00E+09    | 1    | 1000000  | 100  | 12942984.85   | 740.27  | 457.04  |
| InfluxDB    | 10     | 10     | 10   | 1.00E+09    | 1    | 1000000  | 100  | 1779606.4     | 5591    | 4666.39 |
| TimescaleDB | 10     | 10     | 10   | 1.00E+09    | 1    | 1000000  | 100  | 3781467.52    | 2345.69 | 1193.78 |

聚合数据查询

|               | 客户端 | 存储组 | 设备 | 序列-数据量 | 变量 | LOOP | 范围   | 速度(point/s) | AVG     | MIN     |
| ------------- | ------ | ------ | ---- | ----------- | ---- | ---- | ------ | ------------- | ------- | ------- |
| IoTDB-1       | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.0001 | 49.75         | 27.87   | 18.03   |
| IoTDB-2       | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.001  | 49.75         | 49.14   | 19.87   |
| IoTDB-3       | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.01   | 49.76         | 48.69   | 22.32   |
| IoTDB-4       | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.1    | 48.68         | 99.14   | 25.56   |
| IoTDB-5       | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 1      | 14            | 595.61  | 45.54   |
| InfluxDB-1    | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.0001 | 234.32        | 40.28   | 21.63   |
| InfluxDB-2    | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.001  | 28.88         | 341.9   | 238.1   |
| InfluxDB-3    | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.01   | 3.07          | 3226.87 | 2664.86 |
| TimescaleDB-1 | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.0001 | 42.39         | 220.57  | 120.5   |
| TimescaleDB-2 | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.001  | 5.8           | 1502.9  | 754.15  |
| TimescaleDB-3 | 10     | 10     | 10   | 1.00E+09    | 1    | 100  | 0.01   | 1.02          | 9711.55 | 7148.69 |

#### 3. 对比图

![硬盘占用及速度对比](https://oscimg.oschina.net/oscnet/up-f307ebb85f21b9032d11ca6abf0bc95a972.png)

![查询响应延迟对比](https://oscimg.oschina.net/oscnet/up-bcf58cee62965891ac5ad872b72607e1463.png)

整体来看 `IoTDB` 无论在写入、原始数据查询还是聚合查询，都几乎是10倍的性能于竞品数据库，而且硬盘占用又小于同款数据库10倍

## 二、系统架构

### 车联网

为了让大家容易明白而编造的数据，但实际情况要复杂的多。

### 1. 系统架构

#### 1.1 系统简介

![车联网系统架构示意图](https://oscimg.oschina.net/oscnet/up-9baaa64947fb74e7cbbdad267aeb4362331.png)

以上示意图可能非常简单，但我觉得足够表明一个整体架构。 当一台设备、一辆车连接到协议网关后，便开始了真正的收发数据。一般通信的方式都是基于 `tcp`，搞一段二进制协议，所以协议网关基本要做的工作就是完成对连接的管理、完成对数据的收发及编解码。

当数据完成编解码之后一般会发往消息队列当中，一般都是 `Kafka` 之中。用来解耦生产和消费两端，提供一层缓冲，无论消费服务是死是活还是速度慢，包治百病，甚至还能治未病。

数据发往消息队列的过程中，或之后花活儿就多起来了。但主要的我认为无非还是三种处理方式：

1. 需要将**原始数据**保存入库，这里的原始数据包含二进制数据和解码后的二进制数据。
2. 流处理或批处理数据，在数据落到硬盘之前将能够提前计算的数据全部预先计算出来，这样做的好处是将来查询的时候如果和预计算的模型匹配，那就能非常快的得到结果。
3. 离线处理，这里的应用就太广泛了，一般来讲都是将耗时比较大的放置离线计算来做。但是这里要声明一点，离线计算依然是越快越好，不能因为他叫离线计算所以在设计或开发阶段就不关注时效。

#### 1.2 数据质量

上一章提到了基本的数据质量，但实际工作中，往往质量会出现各种意想不到的数据，下面是工程中影响数据质量的几个比较大的问题：

1. 数据丢失，不管是在采集，上报，数据流转环节，都可能会带来一定的数据丢失比例。
2. 数据乱序，数据在打包、上报、流转等环节均可能出现乱序，尤其是在补传数据中。
3. 数据重复，数据重复发送，尤其是在网络不好时。
4. 数据本身不准确，这个最突出的地方就是在 GPS 数据中，经常出现飘点、噪点等等。

### 2. 数据库的挑战

#### 2.1 数据项多

汽车里具有非常复杂的电路系统和传感器设备，我印象当中的粗略估算应该是有 120 项左右，并且这些数据项并不是车内数据的全部。随着自动驾驶的到来，汽车的传感器会越来越多，数据项就会更多。

如果按照传统的 Mysql 存储，那么由于行式存储，所以在取回数据时候就会非常影响效率，之后介绍到 IoTDB 的文件格式的时候再聊。

#### 2.2 存量数据大

我们按照宝马汽车 2019 销售量估计，252 万辆，我们假定 4 年前就已经具备了联网模块那就是 1000 万辆汽车。按照每条数据 1K，每天采样上传 1 次，应该是 每天 9G 数据。但因为车不可能一直都点火开，所以要假设一个 30% 的在线率，那就是 3G 数据。

3 年大约就会存储 3TB 数据，可能你觉得 3T 数据对于时下最热的大数据来讲并不是一个非常庞大的数字，但如果整个数据里面不包含任何图片、音视频甚至都没有文字，全部是由整数、浮点数堆积起来的，那你可以试想一下这个数据库里面到底有多少数据，如果你一个不小心执行了 `count(*)` 你觉得会卡死不？

#### 2.3 采集频率高

汽车不同于其他传感器的地方是，他是一个处在时刻运动当中的物体，如果需要做一些高阶的计算模型，比如说：碰撞检测、行驶轨迹纠偏等，那么相应的数据采集频率有可能要达到秒级。

当然我说完这句话，可能你感触并不是很深，但是结合前面说到的两点：120项数据、1000万辆车，将采集频率提高到 1 秒一采集，那么这个频率下，一天产生的数据大小就是 259T。这时候你找数据库管理员说，哥们我们的需求要 1 天写入 259T 数据，我觉得反正我是没脸找人家，让产品去跟数据库管理员聊吧。

#### 2.4 大数据分析需求

现有时序数据库都无法支持大数据分析框架，都需要通过数据库的 Api 把数据从数据库往数据仓库导出后再存一份，数据量直接翻倍。 举个例子，我如果需要对 Mysql 中的数据进行 MapReduce 计算，那么只能是将数据通过 JDBC 接口导出到 Hbase 或 Hdfs 上，然后执行计算，可能你觉得这很正常，但按照上面提到的数据量，数据复制之后你公司里可能就需要每天支撑 500T 数据。

#### 2.4 不同数据库遇到的问题

采用了多种尝试，从开始的 MySql 到 MongoDB 再到 Hbase 等等，它们总存在这一点或多点的让你觉得就是不满足的地方，如下图：

![不同数据库对比图](https://oscimg.oschina.net/oscnet/up-ae7b32336adc5bd1d55edcc604b7bc77b8a.png)

### IoTDB

到此为止，整体需求基本明确，作为一款物联网的时序数据库需要处理的问题:

1. 高速写入
2. 高效压缩
3. 多维度查询，降采样、时序分割查询等
4. 查询低延迟高效
5. 提高数据质量，乱序、空值等
6. 对接现有大数据生态

### IoTDB 功能特点

![IoTDB功能特点](E:\文档记录\img\up-a15abc210650702b2857cf217fda6696167.png)

IoTDB 完成了上述问题中的几乎所有功能，而且可以灵活对接多生态，高性能优势等。那么 IoTDB 是如何完成这些优势项，如何做到？

### IoTDB 架构描述

![IoTDB架构图](E:\文档记录\img\up-c3943bf8330088103d35e5f754e3821bc47.png)

对照上面的图，大致了解一下 IoTDB 的结构，逻辑上被分为 3 个大部分，其中：

1. Engine 是完整的数据库进程，负责 sql 语句的解析，数据写入、查询、元数据管理等功能。
2. Storage 是底层存储结构，类似于Mysql 的 idb 文件
3. Analyzing Layer 是各种连接器，暂不涉及细节。

Engine 和 Storage 中主要包含：

1. IoTDB Engine，也就是代码中的 `Server` 模块.
2. Native API，他是高效写入的基石，代码中的 `Session` 模块
3. JDBC，传统的 JDBC 连接调用方式，代码中的 `JDBC` 模块
4. TsFile，这是整个数据库的一个特色所在，传统的数据库如果使用 Spark 做离线分析，或者 ETL 都需要通过数据库进程对外读取，而 IoTDB 可以直接迁移文件，省去了来回转换类型的开销。TsFile 提供了两种读写模式，一种基于 HDFS，一种基于本地文件。

## 三、文件格式简介

这一章主要想聊一聊：

1. 行式存储、列式存储的区别
2. TsFile 的格式

### 行式与列式存储的区别

假如我们的逻辑上的数据表格式及数据为：

| 时间戳     | 人名 | 体温 |
| ---------- | ---- | ---- |
| 1580950800 | 张三 | 36.5 |
| 1580950800 | 李四 | 36.9 |
| 1580950800 | 王五 | 36.7 |

那么他出现在硬盘格式就是：

![硬盘行列存储差异图](E:\文档记录\img\up-0f16bb2ca4fc2f63ee052214ec3ee7778b9.png)

### 行式数据

行式数据是把**逻辑相关**的数据在硬盘上放到一起，比如上面的例子，我们可以称之为体温表，所以在逻辑上：时间、人、体温，就成为了逻辑上紧密相关的数据。

所以把相关的数据的硬盘上的组织方式也变成连续的，假如我需要取 `张三` 的数据，那么当你读出 R1 文件块的时候，就是读出了所有 `张三` 相关的数据。

### 列式数据

列式数据在我理解是将**物理相关**的数据放到一起，比如时间是一类(`long` 类型)、名字是一类(`string` 类型)、体温是一类(`float` 类型)。当然这种硬盘的组织方式，相比起行式数据库，在取拼回体温表的结构的时候，速度就慢了很多，因为你要分别取 C1、C2、C3 文件块，然后还要写个容器往里 `Set()`。那么列式数据存储方式相比于行式存储优势在哪里呢？

#### 1.1 取数据方式

有一种叫法是**只读投影列**，避免查询无关列的读取。列式存储的优势在于查询的列数远小于总属性数量，就能少读很多数据。可能读起来非常绕口，举个例子：比如我需要查体温大于 36 度的体温值，sql : `select 体温 FROM table WHERE 体温 > 36` 。这时候如果是列式存储只需要读出 C3 数据块就可以一次性查到所有数据。而行式数据库中，则需要读出 R1、 R2、 R3。在第二章中介绍到物联网中的时序数据的特点：**存量数据非常大**，如果遍历几百亿数据，时间差距明显就拉开了。

#### 1.2 数据编码和压缩

因为物理相关的数据他们类型相同，可以使用多种多样的编码方式，比如 IoTDB 中就提供了 8 种编码方式，这个不具体聊，等后面章节再说。

我们继续拿时间列举例子，我们可以把时间列改造为差值存储： 比如 C1 文件块中先存储基础值 1580950800 那么他后面的数据值只需要存储 0 就可以，存储的数字小了，那么占用的存储空间肯定也就小了，当数字特别大且差值比较小的时候，这用编码方式就非常有意义。当然还有很多好玩儿的编码方式，欢迎持续关注。

### TsFile 文件格式

为什么叫 TsFile ？我听意思应该是作为 TimeSeriresFile 的缩写，也就是**时序数据文件**的意思。

![chunk数据格式](E:\文档记录\img\up-4f341212c92a28a270217bb9bd4a7dd64d7.png)

这是一个数据被刷入磁盘后的缩减版 TsFile 格式，我们还拿上面的数据举例，用来直观的解释 TsFile 中出现的一些名词，假如我的数据为：

| 时间戳     | 人名 | 体温 | 心率 |
| ---------- | ---- | ---- | ---- |
| 1580950800 | 张三 | 36.5 | 70   |
| 1580950800 | 李四 | 36.9 | 80   |
| 1580950800 | 王五 | 36.7 | 100  |
| 1580950911 | 王五 | 36.6 | 90   |

上面的数据刷新到磁盘上后会对应关系如下：

![数据及名词对应关系](E:\文档记录\img\up-f6f68662e86748d5fb6c191f9fe0c516666.png)

看到这里应该能理解每个英文名词的意思：

1. ChunkGroup 代表了**设备**(逻辑概念上的一个集合)一段时间内的数据，在 IoTDB 中称为 Device。
2. Chunk 代表了测点数据(逻辑概念上的某一类数据的集合,如体温数据)，在 IoTDB 中称为 Measurement。
3. Page 中存储的是具体数据，包含一个时间序列、一个值序列。
4. PageStatistics 是保存的是Page当中数据的预聚合信息。
5. ChunkStatistics 是保存的是Chunk当中数据的预聚合信息。

> ChunkGroup 中包含多个 Chunk，Chunk 中包含多个 Page ，Page 中 包含多个 时间点和数据项

回想上面提到的 SQL : `select 体温 FROM 王五 WHERE 体温 > 36` , 在 TsFile 中，只要在文件中找到 `王五` 的 ChunkGroup ，并在 ChunkGroup 中找到 `体温` 的 Chunk，然后从第一个 Page 开始遍历就完成了。

## 四、文件数据块

这一章主要想聊一聊：

1. TsFile的文件概览
2. TsFile的数据块

### TsFile文件概览

![文件概览](E:\文档记录\img\up-19f3b1b90260a5105076db1919106706ab4.png)

一个完整的 TsFile 是由图中的几大块组成，图中的数据块与索引块之间使用 1 个字节的分隔符 `2` 来进行分隔，这个分隔符的意义是当 TsFile 损坏的时候，顺序扫描 TsFile 时，依然可以判断下一个是 MetaData 是什么东西。

#### 1. 识别符（Magic）

现在各种软件五花八门，很多软件都拥有自己的文件格式用来存储数据内容，但当硬盘上文件非常多的时候如何有效的识别是否为自己的文件，确认可以打开呢？经常用 windows 系统的朋友可能会想到用扩展名，但假如文件名丢失了，那我们如何知道这个文件是不是能被程序正确访问呢？

这时候通常会使用一个独有的字符填充在文件开头和结尾，这样程序只要访问 1 个固定长度的字符就知道这个文件是不是自己能正常访问的文件了，当然，TsFile 作为一个数据库文件，肯定需要在这个识别符上精心打造一番，它看起来是这样：

>  (decimal)  84 115 70 105 108 101  
>
>  (hex)         54  73   46 69   6c    65   
>
>  (ASCII)       T    s     F    i      l       e

非常 cool 。

#### 2.文件版本（Version）

再精妙的设计也难免产生一些问题，那么就需要升级，那么文件内容也一样，有时候当你的改动特别大了，就会出现完全不兼容的两个版本，这个很好理解不过多解释。TsFile 中采用了 6 个字节来保存文件版本信息，当前 0.9.x 版本看起来就是这样：

>    (decimal)  48  48  48  48  48  50
>
>    (hex)         30  30  30  30  30  32
>
>    (ASCII)       0    0    0    0    0     2

#### 3.数据块

![数据块概览](E:\文档记录\img\up-07de2592fac6bca62936b689d862f195322.png)

**3.1 ChunkGroup**

文件的数据块中包含了多个 ChunkGroup ，其中 ChunkGroup 的概念已经在上一章聊过，它代表了**设备**(逻辑概念上的一个集合)一段时间内的数据，在 IoTDB 中称为 Device。

在实际的文件中，ChunkGroup是由多个 Chunk 和一个 ChunkGroupFooter 组成。其中最后一个 Chunk 的结尾和 ChunkGroupFooter 之间使用 1 个字节的分隔符 `0` 来做区分，ChunkGroupFooter 没有什么具体作用，不做详细解释。

**3.2 Chunk**

一个 ChunkGroup 中包含了多个 Chunk，它代表了测点数据(逻辑概念上的某一类数据的集合,如体温数据)，在 IoTDB 中称为 Measurement。

在实际文件中 Chunk 是由 ChunkHeader 和多个 Page 组成，并被 1 个字节的分隔符 `1` 包裹。ChunkHeader中主要保存了当前 Chunk 的数据类型、压缩方式、编码方式、包含的 Pages 占用的字节数等信息。

**3.3 Page**

一个 Chunk 中包含多个 Page，它是一个数据组织方式，数据大小被限制在 64K 左右。

在实际文件中由 PageHeader 和 PageData 组成。其中 PageHeader 里主要保存了，当前 page 里的一些预聚合信息，包含了最大值、最小值、开始时间、结束时间等。他的存在是非常有意义的，因为当某些特定场景的读时候，不必要解开 page 的数据就能够得到结果，比如说 `selece 体温 from 王五 where time > 1580950800` ， 当读到 PageHeader 的时候，找到 startTime 和 endTime 就能判断是否可以使用当前 page。 这个聚合信息的结构同样出现在索引块中，下一章再具体聊这个聚合结构。

**3.4 PageData**

一个 Page 中包含了一个 PageData，里面有两个数组：时间数组和值数组，且这两个数组的下标是对齐的，也就是时间数组中的第一个对应值数组中的第一个。举个例子：

> timeArray: [1,2,3,4] 
>
> valueArray: ['a', 'b', 'c', 'd']

在page中就是这样保存的数据，其中 1 代表了时间 1970-01-01 08:00:00 后的 1 毫秒，对应的值就是 'a'。

### 数据块展示

我们继续使用上一章聊到的示例数据来展示真正的TsFile中是如何保存的。

| 时间戳     | 人名 | 体温 | 心率 |
| ---------- | ---- | ---- | ---- |
| 1580950800 | 王五 | 36.7 | 100  |
| 1580950911 | 王五 | 36.6 | 90   |

当数据被写入 TsFile 中，大概就是下面一个展示的情况，这里省略了索引部分。

```log
            POSITION|	CONTENT
            -------- 	-------
                   0|	[magic head] TsFile
                   6|	[version number] 000002
                  // 因为 6个字节的magic + 6个字节的 version 所以 chunkGroup 从 12 开始
|||||||||||||||||||||	[Chunk Group] of wangwu begins at pos 12, ends at pos 253, version:0, num of Chunks:2
                   // 这里展示的是 ChunkHeader 中保存的信息
                  12|	[Chunk] of xinlv, numOfPoints:1, time range:[1580950800,1580950800], tsDataType:INT32, 
                     	[minValue:100,maxValue:100,firstValue:100,lastValue:100,sumValue:100.0]
                    |		[marker] 1      //  chunk 的真正开始是从这个分隔符 1 开始的
                    |		[ChunkHeader]    // header 的数据在上面展示了
                    |		1 pages         //这里保存的具体数据
                    |		time:1580950800; value:100
                   // 下一个 chunk
                 121|	[Chunk] of tiwen, numOfPoints:1, time range:[1580950800,1580950800], tsDataType:FLOAT, 
                     	[minValue:36.7,maxValue:36.7,firstValue:36.7,lastValue:36.7,sumValue:36.70000076293945]
                    |		[marker] 1
                    |		[ChunkHeader]
                    |		1 pages
                    |		time:1580950800; value:36.7
                 230|	[Chunk Group Footer]
                    |		[marker] 0 // chunkFooter 和 chunk 使用 0 作为分隔
                    |		[deviceID] wangwu
                    |		[dataSize] 218
                    |		[num of chunks] 2
|||||||||||||||||||||	[Chunk Group] of wangwu ends
```

回想我们的查询语句` select 体温 from 王五` , 当经历过索引之后会得到 offset 的值等于 121 ，这时候我们只需要调用`reader.seek(121)`，从这里开始就是所有体温数据的开始点，从这里一直读到 230 的 ChunkGroupFooter 结构的时候，就可以返回给用户数据了。

有兴趣自己实验的朋友可以，引入 TsFile 的包，自行实验，下面给出测试代码：

``` maven
<dependency>
    <groupId>org.apache.iotdb</groupId>
    <artifactId>tsfile</artifactId>
    <version>0.9.1</version>
</dependency>
```

``` java
public static void main(String[] args) throws IOException, WriteProcessException {  
    MeasurementSchema chunk1 = new MeasurementSchema("tiwen", TSDataType.FLOAT, TSEncoding.PLAIN);  
    MeasurementSchema chunk2 = new MeasurementSchema("xinlv", TSDataType.INT32, TSEncoding.PLAIN);  

    Schema chunks = new Schema();  
    chunks.registerMeasurement(chunk1);  
    chunks.registerMeasurement(chunk2);  

    TsFileWriter writer = new TsFileWriter(new File("test"), chunks);  

    RowBatch chunkGroup = chunks.createRowBatch("wangwu");  

    long[] timestamps = chunkGroup.timestamps;  
    Object[] values = chunkGroup.values;  

    timestamps[0] = 1580950800;  
    float[] tiwen = (float[]) values[0];  
    int[] xinlv = (int[]) values[1];  

    // 写入王五的体温  
    tiwen[0] = 36.7f;  
    //写入王五的心率  
    xinlv[0] = 100;  
    chunkGroup.batchSize++;  

    timestamps[1] = 1580950800;  
    // 写入第二条王五的体温  
    tiwen[1] = 36.6f;  
    //写入第二条王五的心率  
    xinlv[1] = 90;  
    chunkGroup.batchSize++;  

    writer.write(chunkGroup);  
    writer.close();  
}
```

执行完成之后你可以使用 IoTDB 中的 TsFileSketchTool 来查看文件结构，得到文中示例的展示结果；或者使用 od 等工具查看，祝玩儿的开心。[IoTDB 0.9.1 版本下载](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fwww.apache.org%2Fdyn%2Fcloser.cgi%2Fincubator%2Fiotdb%2F0.9.1-incubating%2Fapache-iotdb-0.9.1-incubating-bin.zip)

这一章聊到了 TsFile 分为了 `数据块` 和 `索引块`，并且介绍了数据块的具体组成部分和查询逻辑。

## 五、文件索引块

这一章主要想聊聊：

1. TsFile索引块的组成
2. 索引块的查询过程
3. 索引块目前在做的改进项

### 索引块

![索引块结构图](E:\文档记录\img\up-15235f29ea818fc03f4b768e88ce7f2b578.png)

索引块由两大部分组成，其写入的方式是从左到右写入，也就是从文件头向文件尾写入。但读出的方式是先读出TsFileMetaData 再读出 TsDeviceMetaDataList 中的具体一部分。我们按照读取数据的顺序介绍：

#### TsFileMetaData

TsFileMetaData属于文件的 1 级索引，用来索引 Device 是否存在、在哪里等信息，其中主要保存了：

1. DeviceMetaDataIndexMap：Map结构，Key 是设备名，Value 是 TsDeviceMetaDataIndex ，保存了包含哪些 Device（逻辑概念上的一个集合一段时间内的数据，例如前几章我们讲到的：张三、李四、王五）以及他们的开始时间及结束时间、在左侧 TsDeviceMetaDataList 文件块中的偏移量等。
2. MeasurementSchemaMap：Map结构，Key 是测点的一个全路径，Value 是 measurementSchema ，保存了包含的测点数据(逻辑概念上的某一类数据的集合,如体温数据)的原信息，如：压缩方式，数据类型，编码方式等。
3. 最后是一个`布隆过滤器`，快速检测某一个 `时间序列` 是不是存在于文件内(这里等聊到 server 模块写文件的策略时候再聊)。我们知道这个过滤器的特点就是：没有的一定没有，但有的不一定有。为了保证准确性和过滤器序列化后的大小均衡，这里提供了一个 1% - 10% 错误率的可配置，当为 1% 错误率时，保存 1 万个测点信息，大概是 11.7 K。

我们再回想 SQL ：`SELECT 体温 FROM 王五 WHERE time = 1` 。读文件的过程就应该是：

1. 先用`布隆过滤器`判断文件内是否有`王五的体温`列，如果没有，查找下一个文件。
2. 从 DeviceMetaDataIndexMap 中找到`王五`的 TsDeviceMetaDataIndex ，从而得到了`王五`的 TsDeviceMetadata 的 offset，接下来就寻道至这个 offset 把`王五`的 TsDeviceMetadata 读出来。
3. MeasurementSchemaMap 不用关注，主要是给 Spark 使用的，ChunkHeader 中也保存了这些信息。

#### TsDeviceMetaDataList

TsDeviceMetaDataList 属于文件的 2 级索引，用来索引具体的测点数据是不是存在、在哪里等信息。其中主要保存了：

1. ChunkGroupMetaData：ChunkGroup 的索引信息，主要包含了每个 ChunkGroup 数据块的起止位置以及包含的所有的测点元信息（ChunkMetaData）。
2. ChunkMetaData ：Chunk 的索引信息，主要包含了每个设备的测点在文件中的起止位置、开始结束时间、数据类型和预聚合信息。

上面的例子中，从 TsFileMetadata 已经拿到了`王五`的 TsDeviceMetadataIndex，这里就可以直接读出王五的 TsDeviceMetadata，并且遍历里边的 ChunkGroupMetadata 中的 ChunkMetadata，找到`体温`对应的所有的 ChunkMetadata。通过预聚合信息对时间过滤，判断能否使用当前的 Chunk 或者能否直接使用预聚合信息直接返回数据(等介绍到 server 的查询引擎时候细聊)。

如果不能直接返回，因为 ChunkMetaData 包含了这个 Chunk 对应的文件的偏移量，只需要使用 `seek(offSet)` 就会跳转到数据块，使用上一章介绍的读取方法进行遍历就完成了整个读取。

### 预聚合信息（Statistics）

文中多次提到了`预聚合`在这里详细介绍一下它的数据结构。

``` java
// 所属文件块的开始时间
private long startTime;  
// 所属文件块的结束时间
private long endTime;
// 所属文件块的数据类型
private TSDataType tsDataType;
// 所属文件块的最小值
private int minValue;  
// 所属文件块的最大值
private int maxValue;  
// 所属文件块的第一个值
private int firstValue;  
// 所属文件块的最后一个值
private int lastValue;  
// 所属文件块的所有值的和
private double sumValue;
```

这个结构主要保存在 ChunkMetaData 和 PageHeader 中，这样做的好处就是，你不必从硬盘中读取具体的`Page` 或者 `Chunk` 的文件内容就可以获得最终的结果，例如：`SELECT SUM(体温) FROM 王五 `，当定位到 ChunkMetaData 时，判断能否直接使用这个 Statistics 信息（具体怎么判断，之后会在介绍 server 时具体介绍），如果能使用，那么直接返回 sumValue。这样返回的速度，无论存了多少数据，它的聚合结果响应时间简直就是 1 毫秒以内。

### 样例数据

我们继续使用上一章聊到的示例数据来展示。

| 时间戳     | 人名 | 体温 | 心率 |
| ---------- | ---- | ---- | ---- |
| 1580950800 | 王五 | 36.7 | 100  |
| 1580950911 | 王五 | 36.6 | 90   |

完整的文件信息如下：

``` log
            POSITION|	CONTENT
            -------- 	-------
                   0|	[magic head] TsFile
                   6|	[version number] 000002
                    // 数据块开始
|||||||||||||||||||||	[Chunk Group] of wangwu begins at pos 12, ends at pos 253, version:0, num of Chunks:2
                  12|	[Chunk] of xinlv, numOfPoints:1, time range:[1580950800,1580950800], tsDataType:INT32, 
                     	[minValue:100,maxValue:100,firstValue:100,lastValue:100,sumValue:100.0]
                    |		[marker] 1
                    |		[ChunkHeader]
                    |		1 pages
                 121|	[Chunk] of tiwen, numOfPoints:1, time range:[1580950800,1580950800], tsDataType:FLOAT, 
                     	[minValue:36.7,maxValue:36.7,firstValue:36.7,lastValue:36.7,sumValue:36.70000076293945]
                    |		[marker] 1
                    |		[ChunkHeader]
                    |		1 pages
                 230|	[Chunk Group Footer]
                    |		[marker] 0
                    |		[deviceID] wangwu
                    |		[dataSize] 218
                    |		[num of chunks] 2
|||||||||||||||||||||	[Chunk Group] of wangwu ends
                    // 索引块开始
                 253|	[marker] 2
                 254|	[TsDeviceMetadata] of wangwu, startTime:1580950800, endTime:1580950800
                    |		[startTime] 1580950800
                    |		[endTime] 1580950800
                    |		[ChunkGroupMetaData] of wangwu, startOffset12, endOffset253, version:0, numberOfChunks:2
                    |			[ChunkMetaData] of xinlv, startTime:1580950800, endTime:1580950800, offsetOfChunkHeader:12, dataType:INT32, statistics:[minValue:100,maxValue:100,firstValue:100,lastValue:100,sumValue:100.0]
                    |			[ChunkMetaData] of tiwen, startTime:1580950800, endTime:1580950800, offsetOfChunkHeader:121, dataType:FLOAT, statistics:[minValue:36.7,maxValue:36.7,firstValue:36.7,lastValue:36.7,sumValue:36.70000076293945]
                 446|	[TsFileMetaData]
                    |		[num of devices] 1
                    |			[TsDeviceMetadataIndex] of wangwu, startTime:1580950800, endTime:1580950800, offSet:254, len:192
                    |		[num of measurements] 2
                    |		2 key&measurementSchema
                    |		[createBy isNotNull] false
                    |		[totalChunkNum] 2
                    |		[invalidChunkNum] 0
                    //布隆过滤器
                    |		[bloom filter bit vector byte array length] 30
                    |		[bloom filter bit vector byte array] 
                    |		[bloom filter number of bits] 256
                    |		[bloom filter number of hash functions] 5
                 599|	[TsFileMetaDataSize] 153
                 603|	[magic tail] TsFile
                 609|	END of TsFile
```

当执行： `SELECT 体温 FROM 王五` 时：

1. 从 `599` 开始读，1 级索引长度为 153.
2. `599 - 153 = 446` 就是 1 级索引读开始位置，并读出 TsDeviceMetadataIndex of 王五，其中记录了，王五设备的 2 级索引的 offset 为 254.
3. 跳到 `254` 开始读 2 级索引，找到 ChunkMetaData of 体温， 其中记录了体温数据的 Chunk 的offset 为 `121`
4. 跳到 `121` ，这里进入了数据块，从 `121` 读取到 `230` ，读出的数据就全部是体温数据。

### 改进项

#### 1. 只读投影列

前面第 3 步中，读取 2 级索引时候，会将这个设备下的所有测点数据全部读出来，这依然不太符合**只读投影列**的设计，所以在新的 TsFile 中，修改了 1级索引和 2 级索引的部分结构，使得读出的数据更少，更高效。有兴趣的同学可以关注 PR： [Refactor TsFile #736](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fgithub.com%2Fapache%2Fincubator-iotdb%2Fpull%2F736)

#### 2. 文件级 Statistics

在物联网场景中经常会涉及到查询某个设备的最后状态，比如：车联网中，查询车辆的末次位置( `SELECT LAST(lat,lon) FROM VechicleID `)，或者当前的点火、熄火状态等 `SELECT LAST(accStatus) FROM VechicleID `。

或者当某些分页查询等情况时候，经常会使用到 `COUNT(*)` 等操作，这些都非常符合 Statistics 结构，这些场景涉及到的索引设计也都会体现到新的 TsFile 索引改动中。

## 六、元数据索引块

这一章主要想聊聊：

1. 原有索引中的不足
2. 新版本中索引的设计

### 原有索引中的不足

![img](E:\文档记录\img\de365a84-0130-44fc-95f5-624a45c3e574.png)

现在来张图回顾一下原有的数据存储方式，在文件尾部使用DeviceMetaDataIndexMap和MeasurementSchemaMap中记录所有设备数据偏移量、传感器的相关信息等。因为使用的是Map结构访问都是O(1)的，但是需要关注的一个问题就是它是在内存中O(1)的，在磁盘上并不能找到什么好的查询方式，唯一能做的就是全部读取出来然后放到内存中。

通常情况下这不会有什么问题，但是使用在工业场景中，**传感器+设备很有可能数以百万计**，这会引发无论你读取的是一个传感器或者是一个设备的数据，在DeviceMetaDataIndexMap这一段数据都需要完整的从磁盘上读取回来，这好吗？这不好，还拿之前的数据举例：

| 时间戳     | 人名     | 体温 | ...  | 年龄 |
| ---------- | -------- | ---- | ---- | ---- |
| 1580950800 | 张一     | 36.5 | ...  | 30   |
| 1580950800 | 张二     | 36.9 | ...  | 30   |
| 1580950800 | 张三     | 36.7 | ...  | 30   |
| 1580950800 | 张..     | 36.7 | ...  | 30   |
| 1580950800 | 张两百万 | 36.7 | ...  | 30   |

当执行一个查询`select 体温 from 张三 where time = 1580950800` 时，**张一、张二、张三...张两百万这两百万个名字**都需要从硬盘中读取并反序列化出来(通常 IoTDB 中的路径是`root.T000100010002.A_JB01.JB01.JTJBGMCA6K2052561`)，也就是1999999个都是多余的读取。

此外，在`TsDeviceMetaDataList`中，也是按照`chunkGroup`存储，意味着，如果我仅查询一列，同样会把其它的列信息读取出来。

### 新版本中索引的设计

为了尽可能贴近**只读投影列**的思想，新版本中对于TsDeviceMetaDataList部分不再按照设备级别存储，**改为按照传感器级别存储**，TsFileMetaData部分不再使用map存储所有设备，**改为使用多叉树并有序排列**。

改动1: 使用将一个传感器的所有ChunkMetaData的存储在一起，并使用TimeseriesMetadata结构进行维护（保存某个传感器的开始的offset及数据长度），如图：

![img](E:\文档记录\img\up-d7a8eb9a4f1f42fea4825e0974c6973b1b9.png)

改动2: 在完成`改动1`之后，我们得到了`TimeSeriesMetadata-年龄,TimeSeriesMetadata-体温, ..., TimeSeriesMetadata-x`，在刷盘之前将所有TimeSeriesMetadata按照传感器名字排序，那么在这里就可以进行二分查找了，如图：

![img](E:\文档记录\img\up-0a2964529fd7d41dcde0b71dbfa372d89a8.png)

改动3: 在一个复杂的设备上传感器很有可能多达数千个，例如：新能源汽车当中，每台车量可以多达4000多个信号项。假如我们需要管理10万辆车，那么依然会有10万 * 4000 = 4亿个TimeSeriesMetadata。还是太多了，假如我们进行多个传感器的值查询时，每次都进行二分，这无疑是浪费的。

所以进行一个树状的提取，假设每1024个提取一次，那么4亿个可以节省为390,625个，假如再抽取一次，那就可以减少到382个了，这样我们从硬盘上一次读取1024个，最多读取3次硬盘就可以完成查找。大意如图：

![img](E:\文档记录\img\up-a2d147afb73a328197782dc1d47e0a00b4e.png)

改动4: 除了传感器之外，还要考虑的是设备级别，因为设备也是数以万计，但同上面的逻辑是一样的。再此就不进行赘述，如图：

![img](E:\文档记录\img\up-55f17d55f107ef80eb0a55561a6b0966a9e.png)

在代码中使用`MetadataIndexNode`类进行存储图中的数据，代表了当前节点属于管理设备的节点还是管理传感器的节点，是中间节点还是叶子节点。使用`MetadataIndexEntry`用来存储具体的信息，也就是`TimeSeriesMetadata`结构在硬盘上的偏移量或者子节点`MetadataIndexNode`在硬盘上的偏移量.

附上一个文件展示：

``` log

            POSITION|	CONTENT
            -------- 	-------
                   0|	[magic head] TsFile
                   6|	[version number] 000002
|||||||||||||||||||||	[Chunk Group] of root.sg1.d1, num of Chunks:10000
                  12|	[Chunk] of s8289, numOfPoints:500, time range:[0,499], tsDataType:INT64, 
                     	startTime: 0 endTime: 499 count: 500 [minValue:1,maxValue:1,firstValue:1,lastValue:1,sumValue:500.0]
                    |		1 pages
                    		 ...
              350026|	[Chunk] of s0, numOfPoints:500, time range:[0,499], tsDataType:INT64, 
                     	startTime: 0 endTime: 499 count: 500 [minValue:1,maxValue:1,firstValue:1,lastValue:1,sumValue:500.0]
                    |		1 pages
                    		 ...
             1418902|	[Chunk Group Footer]
                    |		[marker] 0
                    |		[deviceID] root.sg1.d1
                    |		[dataSize] 1418890
                    |		[num of chunks] 10000
|||||||||||||||||||||	[Chunk Group] of root.sg1.d1 ends
             1418947|	[marker] 2
             1418948|	[ChunkMetadataList] of root.sg1.d1.s0, tsDataType:INT64
                    |		 [ChunkMetaData] of s0, offset:350026
                    		 ...
             2247838|	[TimeseriesMetadata] name:s0, offset:1418948, chunkMetaDataListDataSize:80
             3116728|	[MetadataIndexNode] nodeType:LEAF_MEASUREMENT, childSize:10
                    |		[MetadataIndexEntry] name:s0, offset:2247838, endOffset:2336808
                    |		[MetadataIndexEntry] name:s192, offset:2336808, endOffset:2425782
                    |		[MetadataIndexEntry] name:s2841, offset:2425782, endOffset:2514757
                    |		[MetadataIndexEntry] name:s3763, offset:2514757, endOffset:2603732
                    |		[MetadataIndexEntry] name:s4685, offset:2603732, endOffset:2692705
                    |		[MetadataIndexEntry] name:s5606, offset:2692705, endOffset:2781680
                    |		[MetadataIndexEntry] name:s6528, offset:2781680, endOffset:2870655
                    |		[MetadataIndexEntry] name:s745, offset:2870655, endOffset:2959629
                    |		[MetadataIndexEntry] name:s8371, offset:2959629, endOffset:3048604
                    |		[MetadataIndexEntry] name:s9293, offset:3048604, endOffset:3116728
             3116906|	[TsFileMetadata] deviceNums:1,chunkNums:10000,invalidChunkNums:0
                    |		[MetadataIndexNode] nodeType:INTERNAL_MEASUREMENT,childSize:1
                    |			[MetadataIndexEntry] name:root.sg1.d1,offset:3116728,endOffset:3116906
                    |		[bloom filter bit vector byte array length] 7794
                    |		[bloom filter bit vector byte array] 
                    |		[bloom filter number of bits] 62353
                    |		[bloom filter number of hash functions] 5
             3124764|	[TsFileMetadataSize] 7858
             3124768|	[magic tail] TsFile
             3124774|	END of TsFile

---------------------------------- TsFile Sketch End ----------------------------------
```

到此已经介绍完了文件的整体结构，以及索引的改进过程。
