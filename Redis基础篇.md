# **入门**
## **初始Redis**
### **认识NoSQL**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据结构</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785230978612-bd62860d-1a42-4c95-a260-05d57ce37094.png" width="735" title="" crop="0,0,1,1" id="u07aed508" class="ne-image" style="font-size: 14px">

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">维度</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SQL 数据库</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">NoSQL 数据库</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据类型</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">仅支持</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">结构化数据。</font>**<br/><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">有固定格式、固定字段、固定类型的数据</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">支持</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">非结构化 / 半结构化 / 结构化</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">全类型。</font><br/><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">无固定格式、无固定字段</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据模型</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">固定表结构（行 + 列）</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">灵活模型（文档、键值、列族、图）</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">结构要求</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">必须先定义表结构，不可随意修改。</font><br/><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">要加字段必须修改表结构，麻烦。</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不用提前定义结构，直接存数据。</font><br/><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">无固定结构，</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每条数据字段可不一样</font>** |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据关联</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">强关联（JOIN 多表联查）</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">弱关联 / 无关联，不推荐 JOIN</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">事务</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">强事务（ACID）</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">弱事务 / 最终一致性（BASE）</font> |
| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">典型场景</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">银行、订单、财务、ERP</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">社交、日志、大数据、内容平台</font> |


+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据关联</font>

SQL 关系型数据库：表与表之间用主键、外键物理绑定关联关系，数据库底层强制约束完整性。

例：用户表 (user)、订单表 (order)，订单表存 user_id 作为外键，指向用户表主键 id，删除用户时可级联删除订单，保证关联数据一致性。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785230978824-ce454bd2-17ce-4e7e-b458-2d3b052517e6.png" width="357" title="" crop="0,0,1,1" id="u87bfb988" class="ne-image" style="font-size: 14px">

NoSQL：原生弱支持，无外键，仅手动 ID 引用，数据尽量内嵌聚合存储，减少跨文档、跨集合关联查询，弱化表间绑定。

示例：一条用户文档直接内嵌该用户所有订单，读取一次文档就能拿到全部关联信息，无 JOIN 开销。

```json
{
  "_id": 1,
  "username": "张三",
  "orders": [
    {"orderId":101,"price":99},
    {"orderId":102,"price":199}
  ]
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SQL查询语言</font>

SQL：统一标准 SQL 语言。结构化查询语言，是关系型数据库专属标准查询语言，有统一国际标准语法（ANSI SQL），所有关系库（MySQL、Oracle、SQL Server、PostgreSQL）基础语法高度兼容。

NoSQL：无统一语言，各库语法不同，不同类型 NoSQL 数据库拥有独立查询语法。

SQL 用统一标准化语言解决复杂数据查询与关联统计；NoSQL 放弃统一 SQL 标准，用轻量化专属语法换取高并发读写性能。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">事务</font>

SQL：原生强事务，ACID 四大特性

NoSQL：整体偏向 BASE，放弃实时强一致，换取**高并发**、高可用、水平扩容能力。

BASE：Basically Available（基本可用）、Soft State（柔性状态）、Eventually Consistent（最终一致性）

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">基本可用 Basically Available</font>

系统哪怕一部分节点故障、流量暴打，不会直接整体崩溃宕机，依然能对外提供核心服务，允许非核心功能降级。

正面例子：电商大促，某台服务器崩了，集群其他节点顶上；首页商品正常浏览，只是暂时关闭评论、推荐等次要功能，下单支付核心流程保留。

和 ACID 区别：关系型数据库为了强一致，节点故障可能直接锁住、拒绝访问；NoSQL 优先保 “能访问”，牺牲小部分完整功能。

核心：可用性优先级 > 完美完整性。

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">柔性状态 Soft State（软状态）</font>

数据允许中间不一致临时状态，不需要任意时刻所有节点数据完全同步一致。

关系型 SQL（硬状态）：只要事务没提交，别人读不到未提交数据；提交瞬间全库统一，不存在 “一半更新一半没更新”。

NoSQL 软状态举例：你在分布式集群修改用户昵称，A 节点已经更新，B、C 节点还没同步，短时间内不同服务器查到名字不一样，这个短暂不同步就是柔性状态。

核心：允许数据存在短暂不一致中间态，不用强制瞬时全局统一。

3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">最终一致性 Eventually Consistent</font>

柔性状态只是临时的，经过一小段同步延迟（毫秒 / 秒级），所有节点数据会自动同步，一段时间后全网数据完全一致，只是不保证修改后立刻一致。

举例：

朋友圈点赞，你刚点完自己页面立刻显示点赞数 + 1；

好友刷新页面，几秒后才看到你的点赞；

几秒后所有人查看，点赞数字完全统一。

中间几秒不同步完全可接受，不需要点赞一瞬间全球所有服务器同步完成。

核心：不追求实时一致，只保证一定时间后收敛一致。

### **介绍**
Redis 全称是 Remote Dictionary Server（远程字典服务器），你可以把它理解成一个 “内存数据库” —— 所有**数据都存在内存**里，读写速度飞快（比传统的 MySQL 快几十到上百倍），同时它也支持把数据持久化到硬盘，不用担心重启后数据丢失。

Redis 是一款把数据存在**内存中**的**键值对**数据库；MySQL是一款把数据**存在磁盘**中的**二维表**数据库

打个比方：

传统数据库（比如 MySQL）像家里的衣柜，找衣服需要打开柜门、翻抽屉，**速度慢但能存很多东西**；

Redis 像你口袋里的钱包，钱（数据）就在手边，拿取瞬间完成，**虽然容量小，但速度极快**。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis 的核心特点：</font>

速度快：基于内存操作，IO 多路复用，每秒能处理几十万次读写请求；

数据类型丰富： 键值型，value 支持不同数据结构，还能存 字符串、列表、哈希、集合等，满足多种场景；

数据持久化：内存数据可以存到硬盘，重启后能恢复；

支持分布式：可以多台服务器一起用，应对高并发；

实际应用场景：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缓存（最核心用途）</font>

比如你打开某购物 App，首页的商品列表、你的个人信息，这些数据如果每次都从 MySQL 查，速度慢还耗服务器资源。

Redis 会先把这些 “常用数据” 存到内存里，用户再访问时直接从 Redis 拿，瞬间加载，App 就不会卡顿。

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">计数器 / 排行榜</font>

比如视频的播放量、文章的点赞数、电商的销量排行榜，Redis 能快速实现 “实时计数” 和 “排序”，比如每点击一次播放，Redis 里的数字就 + 1，毫秒级完成。

3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">秒杀 / 限流</font>

比如电商的 “618 秒杀”，瞬间有几十万人抢商品，Redis 能精准控制并发请求，防止服务器被冲垮，还能保证 “一人一单” 不超卖。

4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">消息队列</font>

比如 App 的推送通知、订单支付后的短信提醒，Redis 可以临时存这些 “待处理的消息”，确保不丢失、按顺序处理。

总结：即在某个特定时间点（一般较短），有大量用户去访问数据，访问量大

### **Redis安装**
大多数企业都是基于Linux服务器来部署项目，而且Redis官方也**没有提供Windows**版本的安装包，Windows版redis是微软搞出来的。

因此课程中我们会基于Linux系统来安装Redis，此处选择的Linux版本为CentOS 7.

Redis的官方网站地址：[<font style="color:rgb(0, 56, 132);">https://redis.io/</font>](https://redis.io/)

yum EPEL 源 CentOS7 安装Redis最高就到 3.2.12 老旧版本，**建议源码安装**。

Redis是基于C语言编写的，因此首先需要安装Redis所需要的gcc依赖

```bash
yum install -y gcc tcl
```

放到了/usr/local/src 目录

```bash
cd /usr/local/src
```

解压

```bash
tar -xzf redis-6.2.6.tar.gz
```

进入redis目录

```bash
cd redis-6.2.6
```

运行编译命令

```bash
make && make install
```

如果没有出错，应该就安装成功了。

默认的安装路径是在 /usr/local/bin 目录下

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231060255-e3708817-e08f-4219-86e1-bf32cdafd75f.png" width="358" title="" crop="0,0,1,1" id="u737024d3" class="ne-image" style="font-size: 14px">

该目录以及默认配置到环境变量，因此可以在任意目录下运行这些命令。其中：

redis-cli：是redis提供的命令行客户端

redis-server：是redis的服务端启动脚本

redis-sentinel：是redis的哨兵启动脚本

### **服务启动**
在当前文件夹cmd中输入指令

服务端启动命令：redis-server.exe redis.windows.conf

Redis服务默认端口号为6379，通过快捷键Ctrl+C 即可停止服务

启动客户端：点击redis-cli.exe，输入auth root@123456

退出客户端：exit

连接其他地方的redis：redis-cli.exe -h localhost(换) -p 6379(换) -a root@123456(密码)

输入：ping，回复：pong，即连接成功

启动图形化界面前要先启动服务端

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">默认启动</font>

这种启动属于前台启动，会阻塞整个会话窗口，窗口关闭或者按下 CTRL + C 则Redis停止，不推荐使用。

```bash
redis-server
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">指定配置启动</font>

如果要让Redis以**后台**方式启动，则必须修改Redis配置文件。

就在我们之前解压的redis安装包下（/usr/local/src/redis-6.2.6`），名字叫redis.conf：

我们先将这个配置文件备份一份

```bash
cp redis.conf redis.conf.bck
```

然后修改redis.conf文件中的一些配置

```bash
vim redis.conf
#建议在命令模式按 /，进入搜索模式，比如 /daemonize，再回车，会好找一点，n下一个，N上一个
# 监听的地址，默认是127.0.0.1，会导致只能在本地访问。修改为0.0.0.0则可以在任意IP访问，生产环境不要设置为0.0.0.0
bind 0.0.0.0
# 守护进程，修改为yes后即可后台运行
daemonize yes 
# 密码，设置后访问Redis必须输入密码
requirepass 1234
```

Redis的其它常见配置

```bash
# 监听的端口
port 6379
# 工作目录，默认是当前目录，也就是运行redis-server时的命令，日志、持久化等文件会保存在这个目录
dir .
# 数据库数量，设置为1，代表只使用1个库，默认有16个库，编号0~15
databases 1
# 设置redis能够使用的最大内存
maxmemory 512mb
# 日志文件，默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```

启动Redis

```bash
# 进入redis安装目录 
cd /usr/local/src/redis-6.2.6
# 启动
redis-server redis.conf
```

停止服务

```bash
# 利用redis-cli来执行 shutdown 命令，即可停止 Redis 服务，
# 因为之前配置了密码，因此需要通过 -u 来指定密码
redis-cli -u 1234 shutdown
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">开机自启</font>

首先，新建一个系统服务文件

```bash
vim /etc/systemd/system/redis.service
```

内容如下：

```bash
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /usr/local/src/redis-6.2.6/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

然后重载系统服务

```bash
systemctl daemon-reload
```

现在，我们可以用下面这组命令来操作redis了

```bash
# 启动
systemctl start redis
# 停止
systemctl stop redis
# 重启
systemctl restart redis
# 查看状态
systemctl status redis
```

执行下面的命令，可以让redis开机自启

```bash
systemctl enable redis
```

登录客户端命令

```bash
redis-cli
```

### **Redis客户端**
安装完成Redis，我们就可以操作Redis，实现数据的CRUD了，这需要用到Redis客户端。

Redis安装完成后就自带了命令行客户端：redis-cli，使用方式如下：

```bash
redis-cli [options] [commonds]
```

其中常见的 options 有：

-h 127.0.0.1：指定要连接的redis节点的IP地址，默认是127.0.0.1

-p 6379：指定要连接的redis节点的端口，默认是6379

-a 123321：指定 redis 的访问密码

其中的 commonds 就是Redis的操作命令，例如：

ping：与redis服务端做心跳测试，服务端正常会返回 pong

#### **图形化桌面客户端**
如果连接不上，就关闭防火墙，或者开放6379端口

```bash
systemctl stop firewalld
systemctl disable firewalld
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231060506-b06e2442-2c59-4871-adc0-091852ec78b4.png" width="572" title="" crop="0,0,1,1" id="ub8924e34" class="ne-image" style="font-size: 14px">

Redis默认有16个仓库，编号从0至15。通过配置文件可以设置仓库数量，但是不超过16，并且不能自定义仓库名称。

如果是基于redis-cli连接Redis服务，可以通过select命令来选择数据库：

```bash
# 选择 0号库
select 0
```

## **数据类型**
Redis 存储的是 key-value 结构的数据，其中 key 一般是字符串类型，value 有5种常用的数据类型

字符串(string)：普通字符串，Redis中最简单的数据类型

哈希(hash)：也叫散列，类似于Java中的 HashMap 结构

列表(Iist)：按照插入顺序排序，可以有重复元素，类似于Java中的LinkedList

集合(set)：无序集合，没有重复元素，类似于Java中的HashSet

有序集合(sorted set/zset)：集合中每个元素关联一个分数(score)，根据分数升序排序，没有重复元素

## **常用命令**
### **key的层级格式**
Redis没有类似MySQL中的Table的概念，我们该如何区分不同类型的 key 呢？

例如，需要存储用户、商品信息到redis，有一个用户 id 是1，有一个商品 id 恰好也是1

Redis 的 key 允许有多个单词形成层级结构，多个单词之间用冒号 **: **隔开，格式如下：

项目名 : 业务名 : 类型 : id

这个格式并非固定，也可以根据自己的需求来删除或添加词条。

例如我们的项目名称叫 shop，有 user 和 product 两种不同类型的数据，我们可以这样定义key：

user相关的key：shop:user:1

product相关的key：shop:product:1

如果Value是一个Java对象，例如一个User对象，则可以将对象序列化为SON字符串后存储

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">KEY</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">VALUE</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">shop:user:1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">{"id":1,"name":"Jack","age":21}</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">shop:product:1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">{"id":1,"name":"小米 11","price":4999}</font> |


例如：set shop:user:1 '{"id":1,"name":"Jack","age":21}'

json格式、单词带空格、含特殊符号 要记得加单引号

### **字符串操作命令**
其value是字符串，不过根据字符串的格式不同，又可以分为 3 类：

string：普通字符串

int：整数类型，可以做自增、自减操作

float：浮点类型，可以做自增、自减操作

不管是哪种格式，底层都是字节数组形式存储，只不过是编码方式不同。字符串类型的最大空间不能超过512M。

常用命令：

set key value：设置指定key的值

get key：获取指定key的值

mset k1 v1 k2 v2 ...：批量添加多个String类型的键值对

mget k1 k2 ...：根据多个key获取多个String类型的value

setex key seconds value：设置指定key的值，并将key的过期时间设为seconds秒

setnx key value：只有在key不存在时设置key的值

incr key：将key中存储的整型数字值加一

incrby key increment：将key所储存的整型值加上给定的增量值(increment)

incrbyfloat key increment：让一个浮点类型的数字自增并指定步长

decr key：将key中储存的数字值减一

decrby key decrement：key所储存的值减去给定的减量值(decrement)

### **哈希操作命令**
hash是一个string类型的 field 和 value 的映射表，hash特别适合**用于存储对象**。

String 结构是将对象序列化为 JSON 字符串后存储，当需要修改对象单个字段时很不方便。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231083874-13ff810a-9643-40b9-86e5-0ebd13f20cbb.png" width="520" title="" crop="0,0,1,1" id="u073da879" class="ne-image" style="font-size: 14px">

Hash结构可以将对象中的**每个字段独立存储**，可以针对单个字段做CRUD。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231084128-7d0f29da-07e6-458a-9c79-89af6ea4a839.png" width="585" title="" crop="0,0,1,1" id="u9ff6e527" class="ne-image" style="font-size: 14px">

常用命令：

hset key field value：将哈希表key中的字段field的值设为value

hget key field：获取存储在哈希表中指定字段的值

hmset key field1 valuel field2 value2：同时将多个field-value(域-值)对设置到哈希表key中

hmget key field1 field2：获取所有给定字段的值

hdel key field：删除存储在哈希表中的指定字段

hkeys key：获取哈希表中所有字段

hvals key：获取哈希表中所有值

hgetall key：获取一个key中所有的域值

hincrby key field increment：让一个hash类型key的字段值自增并指定步长

hsetnx key field：添加一个hash类型的key的field值，前提是这个field不存在，否则不执行

### **列表操作命令**
Redis中的 List 类型与Java中的 LinkedList 类似，可以看做是一个双向链表结构，既可以支持正向检索和也可以支持反向检索。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">特征也与 LinkedList 类似：</font>

有序、元素可以重复、插入和删除快、查询速度一般

Redis列表是简单的字符串列表，按照插入顺序排序，常用命令：

lpush key value1 [value2]：将一个或多个值插入到列表头部

rpush key value1 [value2]：将一个或多个值插入到列表尾部

lrange key start stop：获取列表指定范围内的元素；start为起始索引，一般从0开始，想拿到所有元素，stop就写-1

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231083977-891a5bd3-488b-4190-8636-0bf64a2e8dec.png" width="418" title="" crop="0,0,1,1" id="u207beb9a" class="ne-image" style="font-size: 14px">

lpop key：移出并获取列表的第一个元素

rpop key：移除并获取列表最后一个元素

llen key：获取列表长度

rpoplpush source dest：移除列表的最后一个元素，并将该元素添加到另一个列表并返回

brpoplpush source dest timeout：从列表中弹出一个值，将弹出的元素插入到目标列表中并返回它；

如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。

blpop key1 [key2 ...] timeout：从队列头部取出元素，元素取出后直接从原队列删除

brpop key1 [key2 ...] timeout：从队列尾部取出元素，元素取出后直接从原队列删除

### **集合操作命令**
Redis的 Set 结构与Java中的 HashSet 类似，可以看做是一个 value 为 null 的 HashMap。因为也是一个hash表，因此具备

与HashSet类似的特征：无序、元素不可重复、查找快、支持交集、并集、差集等功能

常用命令：

sadd key member1 [member2]：向集合添加一个或多个成员

smembers key：返回集合中的所有成员

sismember key member：判断一个元素是否存在于set中

scard key：获取集合的成员数

sinter key1 [key2]：返回给定所有集合的交集

sunion key1 [key2]：返回所有给定集合的并集

sdiff key1 [key2]：返回所有给定集合的差集

srem key member1 [member2]：删除集合中一个或多个成员

### **有序集合操作命令**
Redis的 SortedSet 是一个可排序的 set 集合，与Java中的 TreeSet 有些类似，但底层数据结构却差别很大。

SortedSet中的每一个元素都带有一个score属性，可以基于 score 属性对元素排序，底层的实现是一个跳表（SkipList）+ hash表。

SortedSet 具备下列特性：可排序、元素不重复、查询快

因为SortedSet的可排序特性，经常被用来实现排行榜这样的功能。

常用命令：

zadd key score1 member1 [score2 member2]：向有序集合添加一个或多个成员，如果已存在则更新其 score 值

zrange key start stop [withscores]：按照score排序后，返回有序集合中指定 排名范围内的成员（分数从小到大）

zrangebyscore key min max：按照score排序后，获取指定 score 范围内的元素

注意：所有的排名默认都是升序，如果要降序则在命令的 z 后面添加 rev 即可

zincrby key increment member：有序集合中对指定成员的分数加上增量 increment

zrem key member [member ...]：移除有序集合中的一个或多个成员

zcard key：计算集合中元素的数量

zscore key member：获取sorted set中的指定元素的score值

zrank key member：获取sorted set中的指定元素的排名

zcount key min max：统计score值在给定范围内的所有元素的数量

zdiff、zinter、zunion：求差集、交集、并集

### **通用命令**
#### **客户端**
Redis的通用客户端命令是不分数据类型的，都可以使用的命令：

keys *：获取全部的key

keys pattern：查找所有符合给定模式(pattern)的key，例如 *name：匹配 lastname 等

在生产环境中不建议用这个命令，数据量达到一定规模后，搜索会变慢，又因为redis是单线程，会阻塞redis服务

exists key：检查给定key是否存在

type key：返回key所储存的值的类型

del key：该命令用于在key存在是删除key

expire key timeout：设置指定key的超时时间

ttl key：查看key的剩余生存时间(time to live)。正值为过期时间、-1为永久不过期、-2为不存在或已过期

persist key：移除key的生存时间，转换成一个持久的key

#### **服务端**
Redis的通用服务端命令是用来查看redis服务器相关信息的：

dbsize：获取当前数据库中key的数量

select db number：切换到指定的数据库，数据库索引号index用数字值指定，以0作为起始索引值

flushdb：清空当前数据库的所有的key

flushall：清空所有数据库的所有的key

info：获取redis，服务器的各种信息及统计数据

monitor：实时打印出redis服务器接收到的命令（调试用）

## **在Java中操作Redis**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231160993-e629cb14-5bcb-42c1-a799-ccc80638fd38.png" width="653" title="" crop="0,0,1,1" id="u55ce380f" class="ne-image" style="font-size: 14px">

### **Jedis**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231161106-0abc3bf2-5981-4825-8831-4acb0bb3505f.png" width="570" title="" crop="0,0,1,1" id="udc84627b" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231160984-d719a19b-b5dd-4693-99c3-05e257f313d6.png" width="574" title="" crop="0,0,1,1" id="u9c09f4f0" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Jedis 连接池</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231197998-8e52fff7-da26-419c-bbf2-e9cd8df31375.png" width="682" title="" crop="0,0,1,1" id="ud493cb64" class="ne-image" style="font-size: 14px">

### **Spring Data Redis**
#### **介绍**
提供了对不同Redis客户端的整合（Lettuce 和 Jedis）

提供了RedisTemplate统一API来操作Redis

支持Redis的发布订阅模型

支持Redis哨兵和Redis集群

支持基于Lettuce的响应式编程

支持基于JDK、JSON、字符串、Spring对象的数据序列化及反序列化

支持基于Redis的DKCollection实现

#### **使用方式**
操作步骤：

创建SpringBoot工程，勾选起步依赖

配置Redis的连接信息

在单元测试中，直接注入RedisTemplate对象

通过RedisTemplate对象操作Redis

springboot版本别用这么高的版本

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231233338-02bd5b89-6085-4741-94e2-986cc8687beb.png" width="488" title="" crop="0,0,1,1" id="uebad7bf6" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">引入依赖</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231234149-dbd6ff72-7630-4d15-af3e-bfbae55b7da1.png" width="500" title="" crop="0,0,1,1" id="u5f9a2d2f" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">配置文件</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231233806-824bd4d7-bbbd-4332-8c76-0af848879dad.png" width="385" title="" crop="0,0,1,1" id="ud5792261" class="ne-image" style="font-size: 14px">

```yaml
//在application.yml文件
spring:
  data:
    redis:
      host: 192.168.130.88
      port: 6379
      database: 0
      password: 1234
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231234252-3ade3775-b3f8-41f2-a4d0-679ae832c296.png" width="811" title="" crop="0,0,1,1" id="u1bc003cc" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231233415-a0dc6ef6-707d-4188-91dc-889a743c85a2.png" width="552" title="" crop="0,0,1,1" id="uba339126" class="ne-image" style="font-size: 14px">



#### **序列化**
如果你用 Java 程序往 Redis 里存数据（比如用 RedisTemplate），但没指定序列化方式，Java 会默认用 JdkSerializationRedisSerializer，把数据序列化成二进制格式。

Redis 里实际存的不是明文 "name"，而是上面这串序列化后的字节；

用 Redis 客户端（redis-cli）查看时，就会显示 \xac\xed\x00\x05t\x00\x04name 这种转义字符，可读性差，内存占用大。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改序列化方式：自定义配置类</font>

##### **自定义RedisTemplate**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231278796-3d28addb-6680-4268-94a6-f1097d8b75e3.png" width="1944" title="" crop="0,0,1,1" id="u982af667" class="ne-image" style="font-size: 14px">

value 用 JSON（实际并不用），因为value有可能存入Java对象；

取的时候，也可以反序列化成Java对象（经过强转），因为存储了对象类的字节码

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231278323-4e7c38d4-3959-4658-8049-fbf7d13d0e0b.png" width="622" title="" crop="0,0,1,1" id="ufbfb8477" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231277873-8aaac9f6-16c8-4bac-bbe4-ea192a509922.png" width="431" title="" crop="0,0,1,1" id="u5d0360b5" class="ne-image" style="font-size: 14px">

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //创建Template
        RedisTemplate<Object, Object> template = new RedisTemplate();
        //设置连接工厂
        template.setConnectionFactory(redisConnectionFactory);
        //指定序列化方式
        template.setKeySerializer(new StringRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        return template;
    }
}
```

为了在反序列化时知道对象的类型，JSON序列化器会将类的class类型写入json结果中，存入Redis，会带来**额外的内存开销**。

为了节省内存空间，我们并不会使用JSON序列化器来处理value，而是统一使用String序列化器，要求只能存储String类型的key和value。当需要存储Java对象时，**手动完成**对象的序列化和反序列化。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231278645-a13ddc37-065e-431b-867f-085fe5b12f83.png" width="2005" title="" crop="0,0,1,1" id="u5462cb9b" class="ne-image" style="font-size: 14px">

##### **StringRedisTemplate(推荐)**
Spring默认提供了一个StringRedisTemplate类，它的key和value的序列化方式默认就是String方式。省去了我们自定义RedisTemplate的过程。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231278952-58212594-7f70-4e4a-add7-0d23f7b676aa.png" width="599" title="" crop="0,0,1,1" id="u8cf27f09" class="ne-image" style="font-size: 14px">



### **String类型操作**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231162444-1916fccb-e8c9-4046-9cb9-ef273d9fa257.png" width="772" title="" crop="0,0,1,1" id="u9b1ba5fa" class="ne-image" style="font-size: 14px">

### **List类型操作**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231162463-19cea540-a09d-4ca6-bfdd-6f1f3d3978f3.png" width="701" title="" crop="0,0,1,1" id="uaeb88705" class="ne-image" style="font-size: 14px">



### **Set类型操作**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231303838-af85b9da-4a99-46c7-92bb-c6be3e61cec6.png" width="678" title="" crop="0,0,1,1" id="ub3de61cf" class="ne-image" style="font-size: 14px">

没有展示全部的命令

### **Hash类型操作**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231303934-58b96470-db35-449e-a477-aaef1dd94cc5.png" width="713" title="" crop="0,0,1,1" id="uf799a90a" class="ne-image" style="font-size: 14px">

### **ZSet类型操作**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231303997-b524d64c-769a-4559-bd10-5d55fc293805.png" width="776" title="" crop="0,0,1,1" id="ud387a325" class="ne-image" style="font-size: 14px">

### **通用操作**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785231304054-bc097556-dc15-4f77-b787-e68ef943348c.png" width="764" title="" crop="0,0,1,1" id="u72cf393c" class="ne-image" style="font-size: 14px">



## **持久化机制**
### **RDB**
RDB 就是 Redis 把当前内存中的所有数据，以 “快照” 的形式（相当于给内存数据拍一张 “照片”），一次性保存到硬盘上的二进制文件（默认文件名是 dump.rdb）。

save：由Redis主进程来执行RDB，会阻塞所有命令，会暂停所有客户端请求

bgsave：开启子进程执行RDB，避免主进程受到影响

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">优点</font>

1. 文件体积小：二进制格式，比下文的 AOF 文件小很多；

2. 恢复速度快：重启 Redis 时加载 RDB 比加载 AOF 快；

3. 对性能影响小：bgsave 用子进程生成文件，主进程不阻塞；

4. 适合备份：可以定时生成 RDB 文件，方便归档备份。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缺点</font>

1. 数据丢失风险：如果 Redis 崩溃，最后一次快照后的所有修改都会丢失（比如按默认配置，最多丢 15 分钟数据）；

2. 生成快照成本高：如果数据量很大（比如几十 G），bgsave 创建子进程会消耗大量内存，短暂影响性能；

3. 不适合实时持久化：无法做到 “每修改一条数据就保存”，实时性差。

### **AOF**
它通过记录写命令实现持久化，能大幅降低数据丢失风险。

Redis 会把所有执行过的写命令（比如 set、hset、lpush 等，读命令 get、hget 不记录），以纯文本的格式一行行追加到 AOF 文件中；当 Redis 重启时，会重新执行 AOF 文件中的所有写命令，把数据恢复到内存中。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">对比</font>

RDB 是给 Redis 内存数据拍一张照片，保存的是「数据结果」；

AOF 是给 Redis 的写操作记流水账，保存的是「数据产生的过程」。

#### **工作原理**
阶段 1：命令追加（Append）

Redis 执行任意写命令后，会先把命令按照「Redis 通信协议」格式拼接成字符串，然后追加到内存中的 AOF 缓冲区（不是直接写硬盘，避免频繁 IO 操作拖慢性能）。

阶段 2：文件写入（Write）

Redis 会定期把AOF 缓冲区中的命令写入到硬盘的 AOF 文件中（注意：写入到操作系统的「硬盘缓存」，还没真正落盘）。

阶段 3：文件同步（Sync）

通过配置规则，让操作系统把「硬盘缓存」中的数据真正刷写到物理硬盘（这一步是保证数据不丢的关键，刷写到硬盘后，即使服务器断电，数据也不会丢失）。

#### **刷盘策略**
always：每执行一条写命令，就立即把缓冲区数据刷写到物理硬盘；安全最高（几乎不丢数据）	

性能差（频繁刷盘，IO 密集）；核心金融 / 支付业务（零数据丢失要求）

everysec：每1 秒自动刷盘一次，把缓冲区累计的命令刷写到物理硬盘；安全较高（最多丢 1 秒数据）

性能适中（平衡安全和性能）；绝大多数业务（电商、社交、通用系统）

no：把刷盘操作交给操作系统，由系统决定何时刷盘（通常 30 秒左右）；安全最低（可能丢几秒到几十秒数据）

性能好（Redis 不做刷盘操作，性能最优）；非核心缓存数据（丢数据无影响）

#### **文件膨胀 & 重写**
AOF 是追加式文件，所有写命令都会往末尾加，时间久了会出现一个严重问题：AOF 文件越来越大（比如反复执行 incr num，AOF 会记录每一次 incr 命令，而不是最终的 num 值）。

比如你执行了 1000 次 incr num 让 num=1000，AOF 会记录 1000 条 incr num 命令，文件会存 1000 行；但实际上恢复数据只需要 1 条 set num 1000 就够了，这就造成了**文件膨胀**。

AOF 重写就是 Redis 会重新生成一个新的 AOF 文件，新文件中只保留「恢复当前数据所需的最少命令」，替换掉原来臃肿的旧 AOF 文件，从而实现**文件瘦身**。

手动重写：

在 redis-cli 中执行命令 bgrewriteaof，Redis 会创建子进程执行重写

自动触发（配置文件规则）

在 Redis 配置文件中通过两个参数配合，满足条件后自动执行 bgrewriteaof：

#自动重写的最小AOF文件大小（文件小于这个值，即使满足百分比也不重写）

auto-aof-rewrite-min-size 64mb

#自动重写的百分比：当前AOF文件大小 / 最后一次重写后的AOF文件大小 ≥ 该值，触发重写

auto-aof-rewrite-percentage 100