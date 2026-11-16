Redis高级篇

# 分布式缓存

单机的Redis存在四大问题：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741701225-614d05d2-e8d8-4af2-b9fe-f930cf1f8b7b.png)

## Redis持久化

Redis有两种持久化方案：

- RDB持久化
- AOF持久化

### RDB持久化

RDB全称Redis Database Backup file（Redis数据备份文件），也被叫做Redis数据快照。

简单来说就是把内存中的所有数据都持久化到磁盘二进制 .rdb 文件。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据。快照文件称为RDB文件，默认是保存在当前运行目录。

**执行时机**

RDB持久化在四种情况下会执行：

- 执行save命令
- 执行bgsave命令
- Redis停机时
- 触发RDB条件时

**1）save命令**

执行下面的命令，可以立即执行一次RDB：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741701565-476f2867-bee6-4ead-8bdd-6da4f7cee587.png)

save命令会导致主进程执行RDB，这个过程中其它所有命令都会被阻塞。只有在数据迁移时可能用到。

**2）bgsave命令**

下面的命令可以异步执行RDB：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741700551-de954726-1f5f-45db-a397-77a004fd966a.png)

这个命令执行后会开启独立进程完成RDB，主进程可以持续处理用户请求，不受影响。

**3）停机时**

Redis停机时会执行一次save命令，实现RDB持久化。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741701661-03be3e0d-4a14-48d0-88b7-7dd0cc081124.png)

**4）自动触发RDB**

Redis内部有触发RDB的机制，可以在 redis.conf 文件中找到，格式如下：

```properties
# 900秒内，如果至少有1个key被修改，则执行bgsave ， 如果是save "" 则表示禁用RDB
save 900 1  
save 300 10  
save 60 10000 
```

RDB的其它配置也可以在 redis.conf 文件中设置：

```properties
# 是否压缩，建议不开启，压缩也会消耗cpu，磁盘的话不值钱
rdbcompression yes

# RDB文件名称
dbfilename dump.rdb  

# 文件保存的路径目录
dir ./ 
```

**RDB原理**

bgsave 开始时会 fork 主进程得到子进程，子进程共享主进程的内存数据。完成 fork 后读取内存数据并写入 RDB 文件。

fork采用的是 copy-on-write 技术：

- 当主进程执行读操作时，访问共享内存；
- 当主进程执行写操作时，则会拷贝一份数据，主进程在新拷贝出来的副本上做修改，对外提供最新业务数据；

- - 假如 a = 1；在 RDB 快照时，存入 a=1。此时有写操作修改为 a=2，主进程写入副本，用户来读也是 读的 a=2。

子进程依旧拿着原来未修改的旧内存页，继续写入 RDB。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741701432-ff3fadb3-3bd8-4c0b-8bfd-7703f41d1d0c.png)

**小结**

RDB方式bgsave的基本流程？

- fork主进程得到一个子进程，共享内存空间
- 子进程读取内存数据并写入新的RDB文件
- 用新RDB文件替换旧的RDB文件

RDB会在什么时候执行？save 60 1000代表什么含义？

- 默认是服务停止时
- 代表60秒内至少执行1000次修改则触发RDB

RDB的缺点？

- RDB 是定时快照，两次RDB之间写入数据有丢失的风险
- fork 子进程、压缩、写出RDB文件都比较耗时



### AOF持久化

**AOF原理**

AOF全称为Append Only File（追加文件）。Redis处理的每一个写命令都会记录在AOF文件，可以看做是命令日志文件。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741729246-1a06885f-a15e-4f08-b738-4b909ec8dad6.png)

**AOF配置**

AOF默认是关闭的，需要修改 redis.conf 配置文件来开启AOF：

```properties
# 是否开启AOF功能，默认是no
appendonly yes
# AOF文件的名称
appendfilename "appendonly.aof"
```

AOF的命令记录的频率也可以通过redis.conf文件来配：

```properties
# 表示每执行一次写命令，立即记录到AOF文件
appendfsync always 
# 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓冲区数据写到AOF文件，是默认方案
appendfsync everysec 
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendfsync no
```

三种策略对比：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741729196-d245fbc4-a21f-4d48-a1d5-8c7d156f7ab0.png)

**AOF文件重写**

因为是记录命令，AOF文件会比RDB文件大的多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义。通过执行 bgrewriteaof 命令，可以让AOF文件执行重写功能，用最少的命令达到相同效果。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741729796-e4fafbb1-5d23-48e7-a795-c85912412611.png)

如图，AOF原本有三个命令，但是set num 123 和 set num 666都是对num的操作，第二次会覆盖第一次的值，因此第一个命令记录下来没有意义。

所以重写命令后，AOF文件内容就是：mset name jack num 666

Redis也会在触发阈值时自动去重写AOF文件。阈值也可以在redis.conf中配置：

```properties
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写 
auto-aof-rewrite-min-size 64mb 
```

**RDB与AOF对比**

RDB和AOF各有自己的优缺点，如果对数据安全性要求较高，在实际开发中往往会**结合**两者来使用。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741729260-0a430fc7-9632-4876-9902-618512d1d789.png)

## Redis主从

### 搭建主从架构

单节点Redis的并发能力是有上限的，要进一步提高Redis的并发能力，就需要搭建主从集群，实现读写分离。

主从集群结构如图：

![img](https://cdn.nlark.com/yuque/0/2026/png/59590650/1785741729614-2264458c-8715-4ce5-a577-caf587c4a32e.png)

共包含三个节点，一个主节点，两个从节点。

这里我们会在同一台虚拟机中开启3个redis实例，模拟主从集群，信息如下：

| IP             | PORT | 角色   |
| -------------- | ---- | ------ |
| 192.168.88.130 | 7001 | master |
| 192.168.88.130 | 7002 | slave  |
| 192.168.88.130 | 7003 | slave  |

#### 方法一

##### 准备配置

要在同一台虚拟机开启3个实例，必须准备三份不同的配置文件和目录，配置文件所在目录也就是工作目录。

1）创建目录

我们创建三个文件夹，名字分别叫7001、7002、7003：

```bash
# 进入/tmp目录
cd /tmp
# 创建目录
mkdir 7001 7002 7003
```

如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741730618-bc111089-594e-4bb7-b97a-ab74f23a3f86.png)

2）恢复原始配置

修改redis-6.2.4/redis.conf文件，将其中的持久化模式改为默认的RDB模式，AOF保持关闭状态。

```properties
# 开启RDB
# save ""
save 3600 1
save 300 100
save 60 10000

# 关闭AOF
appendonly no
```

3）拷贝配置文件到每个实例目录

然后将redis-6.2.4/redis.conf文件拷贝到三个目录中（在/tmp目录执行下列命令）：

```bash
# 方式一：逐个拷贝
cp redis-6.2.4/redis.conf 7001
cp redis-6.2.4/redis.conf 7002
cp redis-6.2.4/redis.conf 7003

# 方式二：管道组合命令，一键拷贝
echo 7001 7002 7003 | xargs -t -n 1 cp redis-6.2.4/redis.conf
```

4）修改每个实例的端口、工作目录

修改每个文件夹内的配置文件，将端口分别修改为7001、7002、7003，将rdb文件保存位置都修改为自己所在目录（在/tmp目录执行下列命令）：

```bash
sed -i -e 's/6379/7001/g' -e 's/dir .\//dir \/tmp\/7001\//g' 7001/redis.conf
sed -i -e 's/6379/7002/g' -e 's/dir .\//dir \/tmp\/7002\//g' 7002/redis.conf
sed -i -e 's/6379/7003/g' -e 's/dir .\//dir \/tmp\/7003\//g' 7003/redis.conf
```

5）修改每个实例的声明IP

虚拟机本身有多个IP，为了避免将来混乱，我们需要在redis.conf文件中指定每一个实例的绑定ip信息，格式如下：

```bash
# redis实例的声明 IP
replica-announce-ip 192.168.150.101
```

每个目录都要改，我们一键完成修改（在/tmp目录执行下列命令）：

```bash
# 逐一执行
sed -i '1a replica-announce-ip 192.168.150.101' 7001/redis.conf
sed -i '1a replica-announce-ip 192.168.150.101' 7002/redis.conf
sed -i '1a replica-announce-ip 192.168.150.101' 7003/redis.conf

# 或者一键修改
printf '%s\n' 7001 7002 7003 | xargs -I{} -t sed -i '1a replica-announce-ip 192.168.150.101' {}/redis.conf
```

##### 启动

为了方便查看日志，我们打开3个ssh窗口，分别启动3个redis实例，启动命令：

```bash
# 第1个
redis-server 7001/redis.conf
# 第2个
redis-server 7002/redis.conf
# 第3个
redis-server 7003/redis.conf
```

启动后：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741731483-24b6f816-ed85-430c-a03b-1d7208b0f3ae.png)

如果要一键停止，可以运行下面命令：

```bash
printf '%s\n' 7001 7002 7003 | xargs -I{} -t redis-cli -p {} shutdown
```

##### 开启主从关系

现在三个实例还没有任何关系，要配置主从可以使用replicaof 或者slaveof（5.0以前）命令。

有临时和永久两种模式：

- 修改配置文件（永久生效）

- - 在redis.conf中添加一行配置：slaveof 

- 使用redis-cli客户端连接到redis服务，执行slaveof命令（重启后失效）：

```bash
slaveof <masterip> <masterport>
```

**注意**：在5.0以后新增命令replicaof，与salveof效果一致。

这里我们为了演示方便，使用方式二。

通过redis-cli命令连接7002，执行下面命令：

```bash
# 连接 7002
redis-cli -p 7002
# 执行slaveof
slaveof 192.168.150.101 7001
```

通过redis-cli命令连接7003，执行下面命令：

```bash
# 连接 7003
redis-cli -p 7003
# 执行slaveof
slaveof 192.168.150.101 7001
```

然后连接 7001节点，查看集群状态：

```bash
# 连接 7001
redis-cli -p 7001
# 查看状态
info replication
```

结果：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741731580-189b4520-75bf-4991-bc50-ff97a4dfc757.png)



#### 方法二：docker

1. 创建自定义 Docker 网络

让三个 Redis 容器在同一局域网内互相访问

```bash
docker network create redis-master-slave-net
```

1. 启动主节点 7001

```bash
docker run -d \
--name redis-7001 \
--network redis-master-slave-net \
-p 7001:6379 \
redis:7-alpine \
redis-server
```

1. 启动从节点 1 7002

```bash
docker run -d \
--name redis-7002 \
--network redis-master-slave-net \
-p 7002:6379 \
redis:7-alpine \
redis-server --slaveof redis-7001 6379
```

1. 启动从节点 2 7003

```bash
docker run -d \
--name redis-7003 \
--network redis-master-slave-net \
-p 7003:6379 \
redis:7-alpine \
redis-server --slaveof redis-7001 6379
```

1. 启动

```bash
# 进入主节点客户端
docker exec -it redis-7001 redis-cli
# 进入从节点客户端
docker exec -it redis-7002 redis-cli
docker exec -it redis-7003 redis-cli
```

#### 测试

执行下列操作以测试：

- 利用redis-cli连接7001，执行set num 123
- 利用redis-cli连接7002，执行get num，再执行set num 666
- 利用redis-cli连接7003，执行get num，再执行set num 888

可以发现，只有在7001这个master节点上可以执行写操作，7002和7003这两个slave节点只能执行读操作。

### 主从数据同步原理

#### 全量同步

主从第一次建立连接时，会执行**全量同步**，将master节点的所有数据都拷贝给slave节点，流程：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741752822-85218185-5ccd-4eb8-819e-4b42f7b4a08d.png)

这里有一个问题，master如何得知salve是第一次来连接呢？

有几个概念，可以作为判断依据：

- **Replication Id**：简称replid，是数据集的标记，id一致则说明是同一数据集。每一个master都有唯一的replid，slave则会继承master节点的replid
- **offset**：偏移量，随着记录在repl_baklog中的数据增多而逐渐增大。slave完成同步时也会记录当前同步的offset。如果slave的offset小于master的offset，说明slave数据落后于master，需要更新。

因此slave做数据同步，必须向master声明自己的replication id 和offset，master才可以判断到底需要同步哪些数据。

因为slave原本也是一个master，有自己的replid和offset，当第一次变成slave，与master建立连接时，发送的replid和offset是自己的replid和offset。

master判断发现slave发送来的replid与自己的不一致，说明这是一个全新的slave，就知道要做全量同步了。

master会将自己的replid和offset都发送给这个slave，slave保存这些信息。以后slave的replid就与master一致了。

因此，**master判断一个节点是否是第一次同步的依据，就是看replid是否一致**。

如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741752787-c0e92356-07b2-4c7f-b849-613d97e5110d.png)

完整流程描述：

1. slave节点请求增量同步
2. master节点判断replid，发现不一致，拒绝增量同步
3. Master fork 子进程，借助 COW 写时复制生成 RDB 快照，发送RDB到slave
4. slave清空本地数据，加载master的RDB
5. master将写操作的命令记录在 repl_baklog 环形缓冲区，并持续将log中的命令发送给slave

- - 无论是否正在生成 RDB，只要是写命令，都会存入缓冲区；

1. slave执行接收到的命令，保持与master之间的同步

#### 增量同步

全量同步需要先做RDB，然后将RDB文件通过网络传输个slave，成本太高了。因此除了第一次做全量同步，其它大多数时候slave与master都是做**增量同步**。

什么是增量同步？就是只更新 slave 与 master 存在差异的数据。如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741752984-2d0c1c7a-24a9-44cf-b670-9217200f0eed.png)

那么master怎么知道slave与自己的数据差异在哪里呢?

##### repl_backlog原理

master怎么知道slave与自己的数据差异在哪里呢?

这就要说到全量同步时的 repl_baklog 文件了。

这个文件是一个固定大小的数组，只不过数组是环形，也就是说**角标到达数组末尾后，会再次从0开始读写**，这样数组头部的数据就会被覆盖。

repl_baklog中会记录Redis处理过的命令日志及 offset，包括master当前的offset，和slave已经拷贝到的offset：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741752572-91a4402c-cfec-41ac-96aa-65901056c54c.png)

slave与master的offset之间的差异，就是salve需要增量拷贝的数据了。

随着不断有数据写入，master的offset逐渐变大，slave也不断的拷贝，追赶master的offset：

![img](https://cdn.nlark.com/yuque/0/2026/png/59590650/1785741752623-991e5b4e-271f-4b97-b470-b81877ffca55.png)

直到数组被填满：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741753149-0ed4a7ef-3140-48d4-8653-f91b82b3815d.png)

此时，如果有新的数据写入，就会覆盖数组中的旧数据。不过，旧的数据只要是绿色的，说明是已经被同步到slave的数据，即便被覆盖了也没什么影响。因为未同步的仅仅是红色部分。

但是，如果slave出现网络阻塞，导致master的offset远远超过了slave的offset： 

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741753514-e088e16e-322b-4ebc-858b-b615e95ab476.png)

如果master继续写入新数据，其offset就会覆盖旧的数据，直到将slave现在的offset也覆盖：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741754170-e0494ee1-19e4-4bfe-9ed3-6f8b93a9d524.png)

棕色框中的红色部分，就是尚未同步，但是却已经被覆盖的数据。此时如果slave恢复，需要同步，却发现自己的offset都没有了，无法完成增量同步了。只能做全量同步。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741754153-c16e73ea-1473-40e5-8d5d-10295c23e8ec.png)



### 主从同步优化

主从同步可以保证主从数据的一致性，非常重要。

可以从以下几个方面来优化Redis主从集群：

- 在master中配置 repl-diskless-sync yes 启用无磁盘复制，避免全量同步时的磁盘IO。
- Redis单节点上的内存占用不要太大，减少RDB导致的过多磁盘IO
- 适当提高 repl_baklog 的大小，发现slave宕机时尽快实现故障恢复，尽可能避免全量同步
- 限制一个master上的slave节点数量，如果实在是太多slave，则可以采用主-从-从链式结构，减少master压力

主从架构图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741774901-ff63abc3-54e8-40a8-a1e5-9935b38e3268.png)

**小结**

简述全量同步和增量同步区别？

- 全量同步：master将完整内存数据生成RDB，发送RDB到slave。后续命令则记录在repl_baklog，逐个发送给slave。
- 增量同步：slave提交自己的offset到master，master获取repl_baklog中从offset之后的命令给slave

什么时候执行全量同步？

- slave节点第一次连接master节点时
- slave节点断开时间太久，repl_baklog中的offset已经被覆盖时

什么时候执行增量同步？

- slave节点断开又恢复，并且在repl_baklog中能找到offset时

## Redis哨兵

### 哨兵原理

#### 集群结构和作用

哨兵的结构如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741774527-ea0059b5-6fdf-4c92-b1f4-f412a7d29dd4.png)

哨兵的作用如下：

- **监控**：Sentinel 会不断检查您的master和slave是否按预期工作
- **自动故障恢复**：如果master故障，Sentinel会将一个slave提升为master。当故障实例恢复后也以新的master为主
- **通知**：Sentinel 充当Redis客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给Redis的客户端

#### 集群监控原理

Sentinel基于心跳机制监测服务状态，每隔1秒向集群的每个实例发送ping命令：

主观下线：如果某sentinel节点发现某实例未在规定时间响应，则认为该实例**主观下线**。

客观下线：若超过指定数量（quorum）的sentinel都认为该实例主观下线，则该实例**客观下线**。quorum值最好超过Sentinel实例数量的一半。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741774543-b29bb6c0-1ac7-4fa6-ba7e-7420fd9900c9.png)

#### 集群故障恢复原理

一旦发现master故障，sentinel需要在salve中选择一个作为新的master，选择依据是这样的：

- 首先会判断slave节点与master节点断开时间长短，如果超过指定值（down-after-milliseconds * 10）则会排除该slave节点
- 然后判断slave节点的 slave-priority 值，越小优先级越高，如果是0则永不参与选举，一般都是一样的
- 如果 slave-prority 一样，则判断slave节点的offset值，越大说明数据越新，优先级越高
- offset值一样，就判断slave节点的运行 id 大小，越小优先级越高。

当选出一个新的master后，该如何实现切换呢？

流程如下：

- sentinel 给备选的 slave1 节点发送 slaveof no one 命令，让该节点成为master
- sentinel 给所有其它 slave 发送slaveof ip 端口号命令，让这些slave成为新master的从节点，开始从新的master上同步数据。
- 最后，sentinel将故障节点标记为slave，当故障节点恢复后会自动成为新的master的slave节点

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741774284-93e797cc-cc5d-4d77-9179-6926dc09c7a3.png)

**小结**

Sentinel的三个作用是什么？

- 监控
- 故障转移
- 通知

Sentinel如何判断一个redis实例是否健康？

- 每隔1秒发送一次ping命令，如果超过一定时间没有相向则认为是主观下线
- 如果大多数sentinel都认为实例主观下线，则判定服务下线

故障转移步骤有哪些？

- 首先选定一个slave作为新的master，执行slaveof no one
- 然后让所有节点都执行 slaveof 新master
- 修改故障节点配置，添加 slaveof 新master

### 搭建哨兵集群

#### 集群结构

三个sentinel实例信息如下：

| 节点 | IP              | PORT  |
| ---- | --------------- | ----- |
| s1   | 192.168.150.101 | 27001 |
| s2   | 192.168.150.101 | 27002 |
| s3   | 192.168.150.101 | 27003 |

#### 方法一

##### 准备实例和配置

要在同一台虚拟机开启3个实例，必须准备三份不同的配置文件和目录，配置文件所在目录也就是工作目录。

我们创建三个文件夹，名字分别叫s1、s2、s3：

```bash
# 进入/tmp目录
cd /tmp
# 创建目录
mkdir s1 s2 s3
```

如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741792859-4faf3afb-3566-40c9-af74-3471757c9c9c.png)

然后我们在s1目录创建一个sentinel.conf文件，添加下面的内容：

```bash
port 27001
sentinel announce-ip 192.168.150.101
sentinel monitor mymaster 192.168.150.101 7001 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
dir "/tmp/s1"
```

解读：

- port 27001：是当前sentinel实例的端口
- sentinel monitor mymaster 192.168.150.101 7001 2：指定主节点信息

- - mymaster：主节点名称，自定义任意写
  - 192.168.150.101 7001：主节点的ip和端口
  - 2：选举master时的quorum值
  - down-after-milliseconds 5000：5 秒不通判定主观下线
  - failover-timeout 60000：故障转移超时 1 分钟

然后将s1/sentinel.conf文件拷贝到s2、s3两个目录中（在/tmp目录执行下列命令）：

```bash
port 27001
sentinel announce-ip 192.168.150.101
sentinel monitor mymaster 192.168.150.101 7001 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
dir "/tmp/s1"
```

修改s2、s3两个文件夹内的配置文件，将端口分别修改为27002、27003：

```bash
sed -i -e 's/27001/27002/g' -e 's/s1/s2/g' s2/sentinel.conf
sed -i -e 's/27001/27003/g' -e 's/s1/s3/g' s3/sentinel.conf
```

##### 启动

为了方便查看日志，我们打开3个ssh窗口，分别启动3个redis实例，启动命令：

```bash
# 第1个
redis-sentinel s1/sentinel.conf
# 第2个
redis-sentinel s2/sentinel.conf
# 第3个
redis-sentinel s3/sentinel.conf
```

启动后：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741793177-fda6ac32-2b15-4b33-b99d-9d0c38990c66.png)

##### 测试

尝试让master节点7001宕机，查看sentinel日志：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741793721-30ae75a0-76e4-47e2-acf4-840d99f69c5a.png)

查看7003的日志：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741792878-89bddd50-de9f-4a82-bdd7-e6fc3d80d652.png)

查看7002的日志：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741792827-be303591-9ccd-431d-9713-6d9c230fa9dc.png)

#### 方法二：docker

1. 先确认网络

哨兵与Redis集群网络一致

```bash
# 创建网络（已创建可跳过）
docker network create redis-master-slave-net
```

1. 启动 3 个哨兵容器

- 哨兵 1：27001

```bash
docker run -d \
--name sentinel-27001 \
--network redis-master-slave-net \
-p 27001:26379 \
redis:7-alpine \
redis-sentinel /etc/sentinel.conf \
--sentinel port 26379 \
--sentinel monitor mymaster redis-7001 6379 2 \
--sentinel down-after-milliseconds mymaster 5000 \
--sentinel failover-timeout mymaster 60000
```

- 哨兵 2：27002

```bash
docker run -d \
--name sentinel-27002 \
--network redis-master-slave-net \
-p 27002:26379 \
redis:7-alpine \
redis-sentinel /etc/sentinel.conf \
--sentinel port 26379 \
--sentinel monitor mymaster redis-7001 6379 2 \
--sentinel down-after-milliseconds mymaster 5000 \
--sentinel failover-timeout mymaster 60000
```

- 哨兵 3：27003

```bash
docker run -d \
--name sentinel-27003 \
--network redis-master-slave-net \
-p 27003:26379 \
redis:7-alpine \
redis-sentinel /etc/sentinel.conf \
--sentinel port 26379 \
--sentinel monitor mymaster redis-7001 6379 2 \
--sentinel down-after-milliseconds mymaster 5000 \
--sentinel failover-timeout mymaster 60000
```

1. 验证密码

若主从 Redis 启动时加了--requirepass 123456，哨兵必须增加两条认证参数，否则无法监控：

```bash
--sentinel auth-pass mymaster 123456
--sentinel sentinel-pass 123456
```

1. 验证哨兵集群状态

- 进入任意哨兵客户端

```bash
docker exec -it sentinel-27001 redis-cli -p 26379
```

- 查看主节点监控信息

```bash
# 查询主节点状态
sentinel master mymaster
# 查询所有从节点
sentinel slaves mymaster
# 查询所有哨兵节点
sentinel sentinels mymaster
```

- 停止主节点容器模拟宕机

```bash
docker stop redis-7001
```



### RedisTemplate

在Sentinel集群监管下的Redis主从集群，其节点会因为自动故障转移而发生变化，Redis的客户端必须感知这种变化，及时更新连接信息。Spring的RedisTemplate底层利用 lettuce 实现了节点的感知和自动切换。

下面，我们通过一个测试来实现 RedisTemplate 集成哨兵机制。

**导入Demo工程**

首先，我们引入课前资料提供的Demo工程：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741821771-a7ee762b-532f-44f1-ab58-6f00bf417066.png)

**引入依赖**

在项目的pom文件中引入依赖：

```bash
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

**配置Redis地址**

然后在配置文件application.yml中指定redis的sentinel相关信息：

```bash
spring:
  redis:
    sentinel:
      master: mymaster
      nodes:
        - 192.168.88.130:27001
        - 192.168.88.130:27002
        - 192.168.88.130:27003
```

**配置读写分离**

在项目的启动类中，添加一个新的bean：

```bash
@Bean
public LettuceClientConfigurationBuilderCustomizer clientConfigurationBuilderCustomizer(){
    return clientConfigurationBuilder -> clientConfigurationBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
}
```

这个bean中配置的就是读写策略，包括四种：

- MASTER：从主节点读取
- MASTER_PREFERRED：优先从master节点读取，master不可用才读取replica
- REPLICA：从slave（replica）节点读取
- REPLICA _PREFERRED：优先从slave（replica）节点读取，所有的slave都不可用才读取master

## Redis分片集群

### 搭建分片集群

主从 和 哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决：

- 海量数据存储问题：所有数据全部存在一台Master中
- 高并发写的问题：所有写操作只能打到唯一 Master

使用分片集群可以解决上述问题，如图：

![img](https://cdn.nlark.com/yuque/0/2026/png/59590650/1785741822036-a8ffbbf4-747a-4aef-a5f8-e1fa6d36688c.png)

分片集群特征：

- 集群中有多个master，每个master保存不同数据

多台 Master 节点均分这 16384 个槽位，每个 Master 只负责一部分槽；

例：3 主架构

masterA：0 ~ 5461

masterB：5462 ~ 10922

masterC：10923 ~ 16383

写入 key 时，公式 CRC16(key) % 16384 算出 key 对应的槽，路由到对应 Master；

不同 key 落在不同 Master，数据分散在多台机器，突破单机内存上限，解决海量存储问题；

写请求分散到多台 Master，每台分担一部分写入压力，横向提升整体写并发。

- 每个master都可以有多个slave节点
- Master 之间互相 ping，检测对方健康状态

主从要哨兵，这个分片集群不需要哨兵，主机互为哨兵监控心跳。

所有 Master 节点两两之间定时发送 PING 心跳包；

若半数以上 Master 判定某节点失联，标记其客观下线；

触发故障转移：从该分片的 Slave 中选出新主，接管对应槽位；

集群自带分布式故障检测，替代独立哨兵，一套集群同时实现分片 + 高可用。

- 客户端请求可以访问集群任意节点，最终都会被转发到正确节点

客户端随便连接集群任意一台节点（主 / 从都可以），分两种情况：

key 属于当前节点负责的槽：直接本地处理读写；

key 属于其他节点槽：当前节点返回 MOVED 重定向指令，告诉客户端目标节点地址；

标准客户端（Lettuce、JedisCluster）收到重定向后，自动转发请求到正确 Master，业务层无感知。

这里我们会在同一台虚拟机中开启6个redis实例，模拟分片集群，信息如下：

| IP              | PORT | 角色   |
| --------------- | ---- | ------ |
| 192.168.150.101 | 7001 | master |
| 192.168.150.101 | 7002 | master |
| 192.168.150.101 | 7003 | master |
| 192.168.150.101 | 8001 | slave  |
| 192.168.150.101 | 8002 | slave  |
| 192.168.150.101 | 8003 | slave  |

#### 方法一

**准备实例和配置**

删除之前的7001、7002、7003这几个目录，重新创建出7001、7002、7003、8001、8002、8003目录：

```bash
# 进入/tmp目录
cd /tmp
# 删除旧的，避免配置干扰
rm -rf 7001 7002 7003
# 创建目录
mkdir 7001 7002 7003 8001 8002 8003
```

在 /tmp 下准备一个新的 redis.conf 文件，内容如下：

```bash
port 6379
# 开启集群功能
cluster-enabled yes
# 集群的配置文件名称，不需要我们创建，由redis自己维护
cluster-config-file /tmp/6379/nodes.conf
# 节点心跳失败的超时时间
cluster-node-timeout 5000
# 持久化文件存放目录
dir /tmp/6379
# 绑定地址
bind 0.0.0.0
# 让redis后台运行
daemonize yes
# 注册的实例ip
replica-announce-ip 192.168.88.130
# 保护模式
protected-mode no
# 数据库数量
databases 1
# 日志
logfile /tmp/6379/run.log
```

将这个文件拷贝到每个目录下：

```bash
# 进入/tmp目录
cd /tmp
# 执行拷贝
echo 7001 7002 7003 8001 8002 8003 | xargs -t -n 1 cp redis.conf
```

修改每个目录下的redis.conf，将其中的6379修改为与所在目录一致：

```bash
# 进入/tmp目录
cd /tmp
# 修改配置文件
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t sed -i 's/6379/{}/g' {}/redis.conf
```

**启动**

因为已经配置了后台启动模式，所以可以直接启动服务：

```bash
# 进入/tmp目录
cd /tmp
# 一键启动所有服务
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t redis-server {}/redis.conf
```

通过ps查看状态：

```bash
ps -ef | grep redis
```

发现服务都已经正常启动：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741821894-ec4a2155-6bb8-4012-a466-5ac48728a7fe.png)

如果要关闭所有进程，可以执行命令：

```bash
ps -ef | grep redis | awk '{print $2}' | xargs kill
```

或者（推荐这种方式）：

```bash
printf '%s\n' 7001 7002 7003 8001 8002 8003 | xargs -I{} -t redis-cli -p {} shutdown
```

**创建集群**

虽然服务启动了，但是目前每个服务之间都是独立的，没有任何关联。

我们需要执行命令来创建集群，在Redis5.0之前创建集群比较麻烦，5.0之后集群管理命令都集成到了redis-cli中。

1）Redis5.0之前

Redis5.0之前集群命令都是用redis安装包下的src/redis-trib.rb来实现的。因为redis-trib.rb是有ruby语言编写的所以需要安装ruby环境。

```bash
 # 安装依赖
 yum -y install zlib ruby rubygems
 gem install redis
```

然后通过命令来管理集群：

```bash
# 进入redis的src目录
cd /tmp/redis-6.2.4/src
# 创建集群
./redis-trib.rb create --replicas 1 192.168.150.101:7001 192.168.150.101:7002 192.168.150.101:7003 192.168.150.101:8001 192.168.150.101:8002 192.168.150.101:8003
```

2）Redis5.0以后

我们使用的是Redis6.2.4版本，集群管理以及集成到了redis-cli中，格式如下：

```bash
redis-cli --cluster create --cluster-replicas 1 192.168.150.101:7001 192.168.150.101:7002 192.168.150.101:7003 192.168.150.101:8001 192.168.150.101:8002 192.168.150.101:8003
```

命令说明：

- redis-cli --cluster或者./redis-trib.rb：代表集群操作命令
- create：代表是创建集群
- --replicas 1或者--cluster-replicas 1 ：指定集群中每个master的副本个数为1，即一个主机对应一个从机，此时节点总数 ÷ (replicas + 1) 得到的就是master的数量。因此节点列表中的前n个就是master，其它节点都是slave节点，随机分配到不同master

运行后的样子：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741822260-0dc84144-4d5f-4f44-9d19-d57973a2d5ab.png)

这里输入yes，则集群开始创建：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741822683-f0f5fb67-c73c-4faf-bff1-ef5affed415b.png)

通过命令可以查看集群状态：

```bash
redis-cli -p 7001 cluster nodes
```

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741823078-5d81da2d-3449-4ac6-82b4-da1359f36db1.png)

**测试**

尝试连接7001节点，存储一个数据：

```bash
# 连接
redis-cli -p 7001
# 存储数据
set num 123
# 读取数据
get num
# 再次存储
set a 1
```

结果悲剧了：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741823326-a9c333e9-7a0a-4303-b133-0f7b2221b27e.png)

集群操作时，需要给redis-cli加上-c参数才可以：

```bash
# -c 是 cluster 集群模式，不加 -c 的 redis-cli 是单机模式，不识别集群重定向指令，会直接报错；
# 加 -c 才能自动处理 MOVED/ASK 跳转。
redis-cli -c -p 7001
```

这次可以了：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741823300-cfcc17c0-540f-4d27-8b4a-77a8e15c5866.png)



#### 方法二：docker

1. 创建集群专用 Docker 网络

```bash
# 创建桥接网络，所有Redis容器加入此网络互通
docker network create redis-cluster-net
```

1. 启动 3 个 Master 主节点（7001~7003）

容器内部端口统一 6379，映射宿主机 700x；开启集群模式 --cluster-enabled yes

- Master 7001

```bash
docker run -d \
--name redis-m-7001 \
--network redis-cluster-net \
-p 7001:6379 \
redis:7-alpine \
redis-server \
--cluster-enabled yes \
--cluster-config-file nodes-7001.conf \
--cluster-node-timeout 5000
```

- Master 7002

```bash
docker run -d \
--name redis-m-7002 \
--network redis-cluster-net \
-p 7002:6379 \
redis:7-alpine \
redis-server \
--cluster-enabled yes \
--cluster-config-file nodes-7002.conf \
--cluster-node-timeout 5000
```

- Master 7003

```bash
docker run -d \
--name redis-m-7003 \
--network redis-cluster-net \
-p 7003:6379 \
redis:7-alpine \
redis-server \
--cluster-enabled yes \
--cluster-config-file nodes-7003.conf \
--cluster-node-timeout 5000
```

1. 启动 3 个 Slave 从节点（8001~8003）

- Slave 8001（依附主 redis-m-7001）

```bash
docker run -d \
--name redis-s-8001 \
--network redis-cluster-net \
-p 8001:6379 \
redis:7-alpine \
redis-server \
--cluster-enabled yes \
--cluster-config-file nodes-8001.conf \
--cluster-node-timeout 5000
```

- Slave 8002（依附主 redis-m-7002）

```bash
docker run -d \
--name redis-s-8002 \
--network redis-cluster-net \
-p 8002:6379 \
redis:7-alpine \
redis-server \
--cluster-enabled yes \
--cluster-config-file nodes-8002.conf \
--cluster-node-timeout 5000
```

- Slave 8003（依附主 redis-m-7003）

```bash
docker run -d \
--name redis-s-8003 \
--network redis-cluster-net \
-p 8003:6379 \
redis:7-alpine \
redis-server \
--cluster-enabled yes \
--cluster-config-file nodes-8003.conf \
--cluster-node-timeout 5000
```

### 散列插槽

**插槽原理**

Redis会把每一个 master 节点映射到 0~16383 共16384个插槽（hash slot）上，查看集群信息时就能看到：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741851326-33eec5af-9838-4eea-bfaa-15eb564d156d.png)

数据key不是与节点绑定，而是与插槽绑定，一个插槽可以放无数个key。redis会根据key的有效部分计算插槽值，分两种情况：

- key中包含"{}"，且“{}”中至少包含1个字符，“{}”中的部分是有效部分
- key中不包含“{}”，整个key都是有效部分

例如：key是 num，那么就根据 num 计算，如果是 user:{typeId}:num，则根据 typeId 计算。

计算方式是利用CRC16算法得到一个hash值，然后对16384取余，得到的结果就是slot值。

slot = CRC16(有效key部分) % 16384

如图，在7001这个节点执行set a 1时，对 a 做hash运算，对16384取余，得到的结果是15495，因此要存储到103节点。

到了7003后，执行get num时，对num做hash运算，对16384取余，得到的结果是2765，因此需要切换到7001节点。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741851067-64c7a1d9-7aa3-49bf-8d02-3736cdd22e39.png)

- 为什么key要与插槽绑定，而不是绑定master？

因为master可能有宕机、删除的情况，如果key绑定master，节点没了，那数据也会跟着没了。

但key绑定到插槽，只需将插槽转移到另一个master就行，数据不会丢失。

**小结**

Redis如何判断某个key应该在哪个实例？

- 将16384个插槽分配到不同的实例
- 根据key的有效部分计算哈希值，对16384取余
- 余数作为插槽，寻找插槽所在实例即可

如何将同一类数据固定的保存在同一个Redis实例？

- 这一类数据使用相同的有效部分，例如key都以 {typeId} 为前缀

### 集群伸缩

redis-cli --cluster提供了很多操作集群的命令，可以通过下面方式查看：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741851142-717561c1-1ce5-439b-9c71-71189f764197.png)

比如，添加节点的命令：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741851262-a5bd1ce5-2197-448f-8bea-63d3ad038c25.png)

需求：向集群中添加一个新的master节点，并向其中存储 num = 10

- 启动一个新的redis实例，端口为7004
- 添加7004到之前的集群，并作为一个master节点
- 给7004节点分配插槽，使得num这个key可以存储到7004实例

这里需要两个新的功能：

- 添加一个节点到集群中
- 将部分插槽分配到新插槽

#### 法一

##### 创建新的redis实例

创建一个文件夹：

```bash
mkdir 7004
```

拷贝配置文件：

```bash
cp redis.conf /7004
```

修改配置文件：

```bash
sed /s/6379/7004/g 7004/redis.conf
```

启动

```bash
redis-server 7004/redis.conf
```

##### 添加新节点到redis

添加节点的语法如下：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741851414-a4335994-cf1a-4800-8fe7-702bb5a42e4f.png)

执行命令：

```bash
redis-cli --cluster add-node  192.168.150.101:7004 192.168.150.101:7001
# 后面的节点是：集群内任意一个已有节点（用来找到整个集群，完成入网）
```

通过命令查看集群状态：

```bash
redis-cli -p 7001 cluster nodes
```

如图，7004加入了集群，并且默认是一个master节点：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741855748-cd10c7d8-991a-4b1c-b076-847c36be83a2.png)

但是，可以看到7004节点的插槽数量为0，因此没有任何数据可以存储到7004上

##### 转移插槽

我们要将num存储到7004节点，因此需要先看看num的插槽是多少：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741854881-83c08e22-6063-4010-9a7c-6f659c96bb7c.png)

如上图所示，num的插槽为2765.

我们可以将0~3000的插槽从7001转移到7004，命令格式如下：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741855134-51f7e012-6d00-4a4f-b324-b68128f14594.png)

具体命令如下：

建立连接：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741853729-747f9a9b-3951-4161-b955-a1f98e1cf8a3.png)

得到下面的反馈：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741854667-731c5828-88ea-46e9-8ddf-f505a0a9fecf.png)

询问要移动多少个插槽，我们计划是3000个：

新的问题来了：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741855079-b49dd4f0-3b2c-4632-aa8e-c724b178e4a1.png)

哪个node来接收这些插槽？？

显然是7004，那么7004节点的id是多少呢？

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741858452-48e2a773-1332-4719-b0a3-46d0dc12911d.png)

复制这个id，然后拷贝到刚才的控制台后：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741859193-1afe1ff9-64f2-4a17-8f00-343af711598f.png)

这里询问，你的插槽是从哪里移动过来的？

- all：代表全部，也就是三个节点各转移一部分
- 具体的id：目标节点的id
- done：没有了

这里我们要从7001获取，因此填写7001的id：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741857532-11270ebb-138a-4fdb-8759-7b78d57c1c79.png)

填完后，点击done，这样插槽转移就准备好了：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741857353-ceba09d6-473b-4427-81bc-dce2381b979c.png)

确认要转移吗？输入yes：

然后，通过命令查看结果：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741857216-5fb4570e-7415-4f04-8abd-2b2bba1dd9b0.png)

可以看到： 

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741860008-ee668a44-068f-44ff-88b4-cd89ae528d33.png)

目的达成。



#### 法二：docker

1. 启动新 Master 容器 7004

- 沿用集群网络redis-cluster-net，开启集群模式，和其他主节点配置保持一致

```bash
docker run -d \
--name redis-m-7004 \
--network redis-cluster-net \
-p 7004:6379 \
redis:7-alpine \
redis-server \
--cluster-enabled yes \
--cluster-config-file nodes-7004.conf \
--cluster-node-timeout 5000
```

- 验证容器正常运行

```bash
docker ps | grep redis-m-7004
```

1. 将 7004 节点加入现有集群

下面步骤与法一相同

### 故障转移

集群初识状态是这样的：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741886554-c89aea31-6b02-4a8e-9306-5d7ae7ce83ce.png)

其中7001、7002、7003都是master，我们计划让7002宕机。

#### 自动故障转移

当集群中有一个master宕机会发生什么呢？

直接停止一个redis实例，例如7002：

```bash
redis-cli -p 7002 shutdown
```

1）首先是该实例与其它实例失去连接

2）然后是疑似宕机：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741885077-a2864518-50f7-4416-8040-b7b196ec5e62.png)

3）最后是确定下线，自动提升一个slave为新的master：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741885197-a47100f1-0e6e-4a7d-9edf-6bb797a60d21.png)

4）当7002再次启动，就会变为一个slave节点了：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741887211-74cd852d-cb62-4d99-a413-f4cd7cfb3bb1.png)

#### 手动故障转移

利用cluster failover命令可以手动让集群中的某个master宕机，切换到执行cluster failover命令的这个slave节点，实现无感知的数据迁移。其流程如下：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741884251-518c1d1e-2ce1-4892-8bf7-3d04b8158f00.png)

这种failover命令可以指定三种模式：

- 缺省：默认的流程，如图1~6歩
- force：省略了对offset的一致性校验
- takeover：直接执行第5歩，忽略数据一致性、忽略master状态和其它master的意见

**案例需求**：在7002这个slave节点执行手动故障转移，重新夺回master地位

步骤如下：

1）利用redis-cli连接7002这个节点

2）执行cluster failover命令

如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741886607-8c5b9c3d-3ea3-4ec5-b86c-4d19e2775537.png)

效果：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741886707-cc86d0f3-a8d4-430e-87fd-555ee643111a.png)

### RedisTemplate访问分片集群

RedisTemplate底层同样基于 lettuce 实现了分片集群的支持，而使用的步骤与哨兵模式基本一致：

1）引入 redis 的 starter 依赖

2）配置分片集群地址

3）配置读写分离

在哨兵模式中，步骤1和3都是一样的，其中只有分片集群的配置方式略有差异，配置的不是哨兵而是各个节点。

如下：

```bash
spring:
  redis:
    cluster:
      nodes:
        - 192.168.150.101:7001
        - 192.168.150.101:7002
        - 192.168.150.101:7003
        - 192.168.150.101:8001
        - 192.168.150.101:8002
        - 192.168.150.101:8003
```



# 多级缓存

## 介绍

传统的缓存策略一般是请求到达Tomcat后，先查询Redis，如果未命中则查询数据库，如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741915257-8b5996db-d0ca-4991-b76d-fac9a09230b2.png)

存在下面的问题：

1. 所有请求都要经过Tomcat处理，Tomcat的性能成为整个系统的瓶颈。
2. Redis缓存失效时，会对数据库产生冲击。

多级缓存就是充分利用请求处理的每个环节，分别添加缓存，减轻Tomcat压力，提升服务性能：

- 浏览器访问静态资源时，优先读取浏览器本地缓存
- 访问非静态资源（ajax查询数据）时，访问服务端
- 请求到达Nginx后，优先读取Nginx本地缓存
- 如果Nginx本地缓存未命中，则去直接查询Redis（不经过Tomcat）
- 如果Redis查询未命中，则查询Tomcat
- 请求进入Tomcat后，优先查询JVM进程缓存
- 如果JVM进程缓存未命中，则查询数据库
- 查询到数据后，逆向回填各级缓存

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741915786-0a3f5794-383f-4006-a1f1-75c767c853b8.png)

在多级缓存架构中，Nginx内部需要编写本地缓存查询、Redis查询、Tomcat查询的业务逻辑，因此这样的nginx服务不再是一个**反向代理服务器**，而是一个编写**业务的Web服务器了**。

因此这样的业务Nginx服务也需要搭建集群来提高并发，再有专门的nginx服务来做反向代理，如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741915350-f8fed786-1b02-4999-acd1-e2c848357405.png)

另外，我们的Tomcat服务将来也会部署为集群模式：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741915928-77aa011d-b0d8-4e53-a2de-ee1749a11c60.png)

可见，多级缓存的关键有两个：

- 一个是在nginx中编写业务，实现nginx本地缓存、Redis、Tomcat的查询
- 另一个就是在Tomcat中实现JVM进程缓存

Nginx 通过 lua 脚本直连 Redis，原生 Nginx 不支持 Lua，必须使用 OpenResty 环境部署。

## JVM进程缓存

- 项目

其中包含两张表：

tb_item：商品表，包含商品的基本信息

tb_item_stock：商品库存表，包含商品的库存信息

之所以将库存分离出来，是因为库存是更新比较频繁的信息，写操作较多。而其他信息修改的频率非常低。

如果把库存和其他信息存在一起，那库存经常性变化，一旦数据发生修改，整条商品信息的缓存都无效了。

所以把经常修改的信息与不常修改的信息分开存储，形成不同的表，不同的缓存。

### 初识Caffeine

缓存在日常开发中起到至关重要的作用，由于是存储在内存中，数据的读取速度是非常快的，能大量减少对数据库的访问，减少数据库的压力。我们把缓存分为两类：

- 分布式缓存，例如Redis：

- - 优点：存储容量更大、可靠性更好、可以在集群间共享
  - 缺点：tomcat要访问redis，有网络开销
  - 场景：缓存数据量较大、可靠性要求较高、需要在集群间共享

- 进程本地缓存，例如HashMap、GuavaCache：

- - 优点：读取本地内存，没有网络开销，速度更快
  - 缺点：存储容量有限、tomcat宕机了数据就丢失，可靠性较低、tomcat无法共享
  - 场景：性能要求较高，缓存数据量较小

我们今天会利用Caffeine框架来实现JVM进程缓存。

**Caffeine**是一个基于Java8开发的，提供了近乎最佳命中率的高性能的本地缓存库。目前Spring内部的缓存使用的就是Caffeine。GitHub地址：https://github.com/ben-manes/caffeine

Caffeine的性能非常好，下图是官方给出的性能对比：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741915366-884fefbc-2eb8-400d-ab55-88143e68638e.png)

可以看到Caffeine的性能遥遥领先！

缓存使用的基本API：

```java
@Test
void testBasicOps() {
    // 构建cache对象
    Cache<String, String> cache = Caffeine.newBuilder().build();
    
    // 存数据
    cache.put("gf", "迪丽热巴");
    
    // 取数据
    String gf = cache.getIfPresent("gf");
    System.out.println("gf = " + gf);
    
    // 取数据，包含两个参数：
    // 参数一：缓存的key
    // 参数二：Lambda表达式，表达式参数就是缓存的key，方法体是查询数据库的逻辑
    // 优先根据key查询JVM缓存，如果未命中，则执行参数二的Lambda表达式
    String defaultGF = cache.get("defaultGF", key -> {
        // 根据key去数据库查询数据
        return "柳岩";
    });
    System.out.println("defaultGF = " + defaultGF);
}
```

Caffeine既然是缓存的一种，肯定需要有缓存的清除策略，不然的话内存总会有耗尽的时候。

Caffeine提供了三种缓存驱逐策略：

- **基于容量**：设置缓存的数量上限

```java
// 创建缓存对象
Cache<String, String> cache = Caffeine.newBuilder()
    .maximumSize(1) // 设置缓存大小上限为 1，只能存一个key
    .build();
```

- **基于时间**：设置缓存的有效时间

```java
// 创建缓存对象
Cache<String, String> cache = Caffeine.newBuilder()
    // 设置缓存有效期为 10 秒，从最后一次写入开始计时 
    .expireAfterWrite(Duration.ofSeconds(10)) 
    .build();

```

- **基于引用**：设置缓存为软引用或弱引用，利用GC来回收缓存数据。性能较差，不建议使用。

注意：在默认情况下，当一个缓存元素过期的时候，Caffeine不会自动立即将其清理和驱逐。而是在一次读或写操作后，或者在空闲时间完成对失效数据的驱逐。

### 实现JVM进程缓存

利用Caffeine实现下列需求：

- 给根据id查询商品的业务添加缓存，缓存未命中时查询数据库
- 给根据id查询商品库存的业务添加缓存，缓存未命中时查询数据库
- 缓存初始大小为100
- 缓存上限为10000

首先，我们需要定义两个Caffeine的缓存对象，分别保存商品、库存的缓存数据。

在 item-service 的com.heima.item.config包下定义CaffeineConfig类：

```java
@Configuration
public class CaffeineConfig {

    @Bean
    public Cache<Long, Item> itemCache(){ //id为key，商品为value
        return Caffeine.newBuilder()
                .initialCapacity(100) // 初始化缓存容器容量
                .maximumSize(10_000) // 缓存最大存储条目上限
                .build();
    }

    @Bean
    public Cache<Long, ItemStock> stockCache(){
        return Caffeine.newBuilder()
                .initialCapacity(100)
                .maximumSize(10_000)
                .build();
    }
}
```

然后，修改item-service中的com.heima.item.web包下的ItemController类，添加缓存逻辑：

```java
@RestController
@RequestMapping("item")
public class ItemController {

    @Autowired
    private IItemService itemService;
    @Autowired
    private IItemStockService stockService;
    // 注入cache对象
    @Autowired
    private Cache<Long, Item> itemCache;
    @Autowired
    private Cache<Long, ItemStock> stockCache;
    
    // ...其它略
    
    @GetMapping("/{id}")
    public Item findById(@PathVariable("id") Long id) {
        // 先根据key查询JVM缓存，缓存未命中，就执行参数二的Lambda表达式，查询数据库
        return itemCache.get(id, key -> itemService.query()
                .ne("status", 3).eq("id", key)
                .one()
        );
    }

    @GetMapping("/stock/{id}")
    public ItemStock findStockById(@PathVariable("id") Long id) {
        return stockCache.get(id, key -> stockService.getById(key));
    }
}
```



## Lua语法入门

Nginx编程需要用到Lua语言，因此我们必须先入门Lua的基本语法。

### 初识Lua

Lua 是一种轻量小巧的脚本语言，用标准C语言编写并以源代码形式开放， 其设计目的是为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制功能。官网：https://www.lua.org/

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741931985-915ede77-ef80-43c9-8eac-f9a932536055.png)

Lua经常嵌入到C语言开发的程序中，例如游戏开发、游戏插件等。

Nginx本身也是C语言开发，因此也允许基于Lua做拓展。

### HelloWorld

CentOS7默认已经安装了Lua语言环境，所以可以直接运行Lua代码。

1）在Linux虚拟机的任意目录下，新建一个 hello.lua 文件

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741931768-c1cad95e-e3ba-442c-9a51-a89e369c7969.png)

2）添加下面的内容

```lua
print("Hello World!") 
```

3）运行

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741931820-ba7910cd-a751-45fa-9689-1597d04eb597.png)

### 变量和循环

#### Lua的数据类型

Lua中支持的常见数据类型包括：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741931948-49bbcdc3-4c50-4e4c-84d5-cafd51455591.png)

另外，Lua提供了type()函数来判断一个变量的数据类型：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741931833-24004f72-107d-4401-abb5-8f6c08990922.png)

#### 声明变量

Lua声明变量的时候无需指定数据类型，而是用 local 来声明变量为局部变量：

```lua
-- 声明字符串，可以用单引号或双引号，
local str = 'hello'
-- 字符串拼接可以使用 ..
local str2 = 'hello' .. 'world'
-- 声明数字
local num = 21
-- 声明布尔类型
local flag = true
```

Lua中的 table 类型既可以作为数组，又可以作为Java中的map来使用。

数组就是特殊的table，key是数组索引而已：

```lua
-- 声明数组 ，key为角标的 table
local arr = {'java', 'python', 'lua'}
-- 声明table，类似java的map
local map =  {name='Jack', age=21}
```

Lua中的数组角标是从1开始，访问的时候与Java中类似：

```lua
-- 访问数组，lua数组的角标从1开始
print(arr[1])
```

Lua中的table可以用key来访问：

```lua
-- 访问table
print(map['name'])
print(map.name)
```

#### 循环

对于table，我们可以利用for循环来遍历。不过数组和普通table遍历略有差异。

遍历数组，用 **ipairs**

```lua
-- 声明数组 key为索引的 table
local arr = {'java', 'python', 'lua'}
-- 遍历数组
for index,value in ipairs(arr) do
    print(index, value) 
end
```

遍历普通table，用 **pairs**

```lua
-- 声明map，也就是table
local map = {name='Jack', age=21}
-- 遍历table
for key,value in pairs(map) do
   print(key, value) 
end
```

### 条件控制、函数

#### 函数

定义函数的语法：

```lua
function 函数名( argument1, argument2..., argument..)
    -- 函数体
    return 返回值
end
```

例如，定义一个函数，用来打印数组：

```lua
function printArr(arr)
    for index, value in ipairs(arr) do
        print(value)
    end
end
```

#### 条件控制

类似Java的条件控制，例如 if、else 语法：

```lua
if(布尔表达式)
then
   --[ 布尔表达式为 true 时执行该语句块 --]
else
   --[ 布尔表达式为 false 时执行该语句块 --]
end
```

与Java不同，布尔表达式中的逻辑运算是基于英文单词：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741932534-3b695e8a-f0b9-4580-9bde-ae764e9ef1fa.png)

需求：自定义一个函数，可以打印table，当参数为nil时，打印错误信息

```lua
function printArr(arr)
    -- nil为false，not nil就是true
    if (not arr) then
        print('数组不能为空！')
        return nil
    end
    for index, value in ipairs(arr) do
        print(value)
    end
end
```



## 实现多级缓存

### 安装OpenResty

OpenResty® 是一个基于 Nginx的高性能 Web 平台，用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。具备下列特点：

- 具备Nginx的完整功能
- 基于Lua语言进行扩展，集成了大量精良的 Lua 库、第三方模块
- 允许使用Lua**自定义业务逻辑**、**自定义库**

官方网站： https://openresty.org/cn/

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741951884-daa14d38-07f5-4f43-9278-2ce61df14d4e.png)

1. 安装开发库

首先要安装OpenResty的依赖开发库，执行命令：

```bash
yum install -y pcre-devel openssl-devel gcc --skip-broken
```

1. 安装OpenResty仓库

你可以在你的 CentOS 系统中添加 openresty 仓库，这样就可以便于未来安装或更新我们的软件包（通过 yum check-update 命令）。运行下面的命令就可以添加我们的仓库：

```bash
yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
```

如果提示说命令不存在，则运行：

```bash
yum install -y yum-utils 
```

然后再重复上面的命令

1. 安装OpenResty

然后就可以像下面这样安装软件包，比如 openresty：

```bash
yum install -y openresty
```

1. 安装opm工具

opm是OpenResty的一个管理工具，可以帮助我们安装一个第三方的Lua模块。

如果你想安装命令行工具 opm，那么可以像下面这样安装 openresty-opm 包：

```bash
yum install -y openresty-opm
```

1. 目录结构

默认情况下，OpenResty安装的目录是：/usr/local/openresty

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741953130-bb754916-29a9-499b-b5e8-551081eec8f8.png)

看到里面的 nginx 目录了吗，OpenResty就是在Nginx基础上集成了一些Lua模块。

1. 配置nginx的环境变量

打开配置文件：

```bash
vi /etc/profile
```

在最下面加入两行：

```bash
export NGINX_HOME=/usr/local/openresty/nginx
export PATH=${NGINX_HOME}/sbin:$PATH
```

NGINX_HOME：后面是OpenResty安装目录下的nginx的目录

然后让配置生效：

```bash
source /etc/profile
```

#### 启动和运行

OpenResty底层是基于Nginx的，查看OpenResty目录的nginx目录，结构与windows中安装的nginx基本一致：

![img](https://cdn.nlark.com/yuque/0/2026/png/59590650/1785741951967-b68fbbf4-96c0-44e4-8efa-eeabfe7d12c8.png)

所以运行方式与nginx基本一致：

```bash
# 启动nginx
nginx
# 重新加载配置
nginx -s reload
# 停止
nginx -s stop
```

nginx的默认配置文件注释太多，影响后续我们的编辑，这里将nginx.conf中的注释部分删除，保留有效部分。

修改/usr/local/openresty/nginx/conf/nginx.conf文件，内容如下：

```nginx
#user  nobody;
worker_processes  1;
error_log  logs/error.log;

events {
  worker_connections  1024;
}

http {
  include       mime.types;
  default_type  application/octet-stream;
  sendfile        on;
  keepalive_timeout  65;

  server {
    listen       8081;
    server_name  localhost;
    location / {
      root   html;
      index  index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
      root   html;
    }
  }
}
```

在Linux的控制台输入命令以启动nginx：

```bash
nginx
```

然后访问页面：http://192.168.88.130:8081，注意ip地址替换为你自己的虚拟机IP

#### 备注

加载OpenResty的lua模块：

```nginx
#lua 模块
lua_package_path "/usr/local/openresty/lualib/?.lua;;";
#c模块     
lua_package_cpath "/usr/local/openresty/lualib/?.so;;";  
```

common.lua

```lua
-- 封装函数，发送http请求，并解析响应
local function read_http(path, params)
  local resp = ngx.location.capture(path,{
      method = ngx.HTTP_GET,
      args = params,
    })
  if not resp then
    -- 记录错误信息，返回404
    ngx.log(ngx.ERR, "http not found, path: ", path , ", args: ", args)
    ngx.exit(404)
  end
  return resp.body
end
-- 将方法导出
local _M = {  
  read_http = read_http
}  
return _M
```

释放Redis连接API：

```lua
-- 关闭redis连接的工具方法，其实是放入连接池
local function close_redis(red)
    local pool_max_idle_time = 10000 -- 连接的空闲时间，单位是毫秒
    local pool_size = 100 --连接池大小
    local ok, err = red:set_keepalive(pool_max_idle_time, pool_size)
    if not ok then
        ngx.log(ngx.ERR, "放入redis连接池失败: ", err)
    end
end
```

读取Redis数据的API：

```lua
-- 查询redis的方法 ip和port是redis地址，key是查询的key
local function read_redis(ip, port, key)
    -- 获取一个连接
    local ok, err = red:connect(ip, port)
    if not ok then
        ngx.log(ngx.ERR, "连接redis失败 : ", err)
        return nil
    end
    -- 查询redis
    local resp, err = red:get(key)
    -- 查询失败处理
    if not resp then
        ngx.log(ngx.ERR, "查询Redis失败: ", err, ", key = " , key)
    end
    --得到的数据为空处理
    if resp == ngx.null then
        resp = nil
        ngx.log(ngx.ERR, "查询Redis数据为空, key = ", key)
    end
    close_redis(red)
    return resp
end
```

开启共享词典：

```nginx
# 共享字典，也就是本地缓存，名称叫做：item_cache，大小150m
lua_shared_dict item_cache 150m; 
```



### OpenResty快速入门

我们希望达到的多级缓存架构如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741968703-7ed265a8-6a13-42ea-b9f0-8e586e7670c6.png)

其中：

- windows上的nginx用来做反向代理服务，将前端的查询商品的ajax请求代理到OpenResty集群
- OpenResty集群用来编写多级缓存业务
- **反向代理流程**

现在，商品详情页使用的是假的商品数据。不过在浏览器中，可以看到页面有发起ajax请求查询真实商品数据。

这个请求如下：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741968664-ac93959e-64a9-4d29-b596-8bef665abf25.png)

请求地址是localhost，端口是80，就被windows上安装的Nginx服务给接收到了。然后代理给了OpenResty集群：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741968638-902f04d8-6452-478d-af05-87b1e40b336c.png)

我们需要在OpenResty中编写业务，查询商品数据并返回到浏览器。

但是这次，我们先在OpenResty接收请求，返回假的商品数据。

- **OpenResty监听请求**

OpenResty的很多功能都依赖于其目录下的Lua库，需要在nginx.conf中指定依赖库的目录，并导入依赖：

1）添加对OpenResty的Lua模块的加载

修改/usr/local/openresty/nginx/conf/nginx.conf文件，在其中的http下面，添加下面代码：

```nginx
#lua 模块
lua_package_path "/usr/local/openresty/lualib/?.lua;;";
#c模块
lua_package_cpath "/usr/local/openresty/lualib/?.so;;";
```

2）监听/api/item路径

修改/usr/local/openresty/nginx/conf/nginx.conf文件，

在 nginx.conf 的 server 下面，添加对 /api/item 这个路径的监听：

```nginx
location  /api/item {
    # 默认的响应类型
    default_type application/json;
    # 响应结果由lua/item.lua文件来决定
    content_by_lua_file lua/item.lua;
}
```

这个监听，就类似于SpringMVC中的@GetMapping("/api/item")做路径映射。

而content_by_lua_file lua/item.lua则相当于调用 item.lua 这个文件，执行其中的业务，把结果返回给用户。相当于java中调用service。

- 编写item.lua

1）在/usr/local/openresty/nginx目录创建文件夹：lua

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741968576-455bc84c-67c2-48f5-b7d8-7681bb9e070c.png)

2）在/usr/loca/openresty/nginx/lua文件夹下，新建文件：item.lua

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741968614-a3fec7f1-b7a5-46ec-bf09-a2e47d915aa7.png)

3）编写item.lua，返回假数据

item.lua中，利用ngx.say()函数返回数据到Response中

```lua
ngx.say('{"id":10001,"name":"SALSA AIR","title":"RIMOWA 21寸托运箱拉杆箱 SALSA AIR系列果绿色 820.70.36.4","price":17900,"image":"https://m.360buyimg.com/mobilecms/s720x720_jfs/t6934/364/1195375010/84676/e9f2c55f/597ece38N0ddcbc77.jpg!q70.jpg.webp","category":"拉杆箱","brand":"RIMOWA","spec":"","status":1,"createTime":"2019-04-30T16:00:00.000+00:00","updateTime":"2019-04-30T16:00:00.000+00:00","stock":2999,"sold":31290}')
```

4）重新加载配置

```bash
nginx -s reload
```

刷新商品页面：http://localhost/item.html?id=1001，即可看到效果：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741969800-7f02ed0c-1763-40aa-8c9e-0004c59628ff.png)

### 请求参数处理

我们在OpenResty接收前端请求，但是返回的是假数据。

要返回真实数据，必须根据前端传递来的商品id，查询商品信息才可以。

那么如何获取前端传递的商品参数呢？

- 获取参数的API

OpenResty中提供了一些API用来获取不同类型的前端请求参数：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741969955-585c9c22-0e29-4d1f-a7ff-62ca4198fd88.png)

- 获取参数并返回

在前端发起的ajax请求如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741969836-d3a6e219-2c9b-4106-b5c3-5e1d4a9b1dad.png)

可以看到商品id是以路径占位符方式传递的，因此可以利用正则表达式匹配的方式来获取ID

1）获取商品id

修改/usr/loca/openresty/nginx/nginx.conf文件中监听 /api/item 的代码，利用正则表达式获取ID：

注意别忘了波浪线 ~

```nginx
location ~ /api/item/(\d+) {
  # 默认的响应类型
  default_type application/json;
  # 响应结果由lua/item.lua文件来决定
  content_by_lua_file lua/item.lua;
}
```

2）拼接ID并返回

修改/usr/loca/openresty/nginx/lua/item.lua文件，获取id并拼接到结果中返回：

```lua
-- 获取商品id
local id = ngx.var[1]
-- 拼接并返回
ngx.say('{"id":' .. id .. ',"name":"SALSA AIR","title":"RIMOWA 21寸托运箱拉杆箱 SALSA AIR系列果绿色 820.70.36.4","price":17900,"image":"https://m.360buyimg.com/mobilecms/s720x720_jfs/t6934/364/1195375010/84676/e9f2c55f/597ece38N0ddcbc77.jpg!q70.jpg.webp","category":"拉杆箱","brand":"RIMOWA","spec":"","status":1,"createTime":"2019-04-30T16:00:00.000+00:00","updateTime":"2019-04-30T16:00:00.000+00:00","stock":2999,"sold":31290}')
```

3）重新加载并测试

运行命令以重新加载OpenResty配置：

```bash
nginx -s reload
```

刷新页面可以看到结果中已经带上了ID：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741969808-57544010-0069-4168-a335-d0957a0c4cb4.png)



### 查询Tomcat

拿到商品ID后，本应去缓存中查询商品信息，不过目前我们还未建立nginx、redis缓存。因此，这里我们先根据商品id去tomcat查询商品信息。我们实现如图部分：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741995137-33b01926-791d-4fa7-bacb-a70dfa7ec000.png)

需要注意的是，我们的OpenResty是在虚拟机，Tomcat是在Windows电脑上。两者IP一定不要搞错了。

两种虚拟机网卡模式区别

1. 桥接模式

虚拟机和 Windows 同网段，直接 ping Windows 局域网 IP，最简单；

1. NAT 模式

Windows 宿主机网关是虚拟机出口，虚拟机需要 ping Windows 的NAT 网卡 IP。

输入：ipconfig，往下翻找到 VMware Network Adapter VMnet8，IPv4地址 就是 Windows NAT 网卡 IP。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741995138-50ca95a5-cee4-4cf5-ab37-17dec0191ad0.png)

在虚拟机设置中看到网卡模式：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741994992-71cb55ae-e725-4cdf-8979-cc9176c4f01c.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741995124-d7b04ff5-6137-44cd-97c5-2ca25eb9dc7e.png)

#### 发送http请求的API

nginx提供了内部API用来发送 http 请求：

发起一次Nginx 内部转发请求，不是走外网 TCP，不走浏览器，完全在 Nginx 进程内部完成调度。

```lua
local resp = ngx.location.capture("/path",{
    method = ngx.HTTP_GET,   -- 请求方式
    args = {a=1,b=2},  -- get方式传参数，等价于 ?a=1&b=2
  })
```

返回的响应内容包括：

- resp.status：响应状态码
- resp.header：响应头，是一个table
- resp.body：响应体，就是响应数据

注意：这里的path是路径，并不包含IP和端口。这个请求会被nginx内部的server监听并处理。

capture 只能访问 Nginx 内部的 location，不能直接写 http://192.168.88.1:8081 目标 Tomcat，无法直接跨进程 / 跨机器访问 Java 服务，所以需要一段代理 location 做转发桥梁，反向代理转发到外部 Tomcat 服务地址 192.168.88.1:8081。

```nginx
location /path {
  # 这里是windows电脑的ip和Java服务端口，需要确保windows防火墙处于关闭状态
  proxy_pass http://192.168.88.1:8081; 
}
```

原理如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741995154-a5586493-a64f-43a8-a4c3-b4909a459fc5.png)

#### 封装http工具

这里要修改item.lua，满足下面的需求：

1. 获取请求参数中的 id
2. 根据 id 向Tomcat服务发送请求，查询商品信息
3. 根据 id 向Tomcat服务发送请求，查询库存信息
4. 组装商品信息、库存信息，序列化为JSON格式并返回

下面，我们封装一个发送 Http 请求的工具，基于 ngx.location.capture 来实现查询tomcat。

1）添加反向代理，到windows的Java服务

因为 item-service 中的接口都是 /item 开头，所以我们监听 /item 路径，代理到Windows上的 tomcat 服务。

修改 /usr/local/openresty/nginx/conf/nginx.conf文件，添加一个location：

```nginx
location /item {
    proxy_pass http://192.168.88.1:8081;
}
```

以后，只要我们调用ngx.location.capture("/item")，就一定能发送请求到Windows的tomcat服务。

2）封装工具类

之前我们说过，OpenResty启动时会加载以下两个目录中的工具文件：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741996395-afc95612-c6f4-4e14-b2c1-0aac338c773a.png)

所以，自定义的http工具也需要放到这个目录下。

在/usr/local/openresty/lualib目录下，新建一个common.lua文件：

```bash
vi /usr/local/openresty/lualib/common.lua
```

内容如下:

```lua
-- 封装函数，发送http请求，并解析响应
local function read_http(path, params)
  local resp = ngx.location.capture(path,{
      method = ngx.HTTP_GET,
      args = params,
    })
  if not resp then
    -- 记录错误信息，返回404
    ngx.log(ngx.ERR, "http请求查询失败, path: ", path , ", args: ", args)
    ngx.exit(404)
  end
  return resp.body
end
-- 将方法导出
local _M = {  
  read_http = read_http
}  
return _M
```

这个工具将 read_http 函数封装到 _M 这个table类型的变量中，并且返回，这类似于导出。

使用的时候，可以利用require('common')来导入该函数库，这里的common是函数库的文件名。

3）实现商品查询

最后，我们修改/usr/local/openresty/lua/item.lua文件，利用刚刚封装的函数库实现对tomcat的查询：

```lua
-- 引入自定义common工具模块，返回值是common中返回的 _M
local common = require("common")
-- 从 common中获取read_http这个函数
local read_http = common.read_http
-- 获取路径参数
local id = ngx.var[1]
-- 根据id查询商品
local itemJSON = read_http("/item/".. id, nil)
-- 根据id查询商品库存
local itemStockJSON = read_http("/item/stock/".. id, nil)
-- 只能返回一个信息，还没拼接
ngx.say(itemJSON)
```

这里查询到的结果是json字符串，并且包含商品、库存两个json字符串，页面最终需要的是把两个json拼接为一个json：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741996541-5f7ed28b-4627-49b1-a6ed-c95f90533dc9.png)

这就需要我们先把JSON变为 lua 的table，完成数据整合后，再转为JSON。

#### CJSON工具类

OpenResty 提供了一个 cjson 的模块用来处理JSON的序列化和反序列化。

官方地址： https://github.com/openresty/lua-cjson/

1）引入cjson模块：

```lua
local cjson = require "cjson"
```

2）序列化：

```lua
local obj = {
    name = 'jack',
    age = 21
}
-- 把 table 序列化为 json
local json = cjson.encode(obj)
```

3）反序列化：

```lua
local json = '{"name": "jack", "age": 21}'
-- 反序列化 json为 table
local obj = cjson.decode(json);
print(obj.name)
```

#### 实现Tomcat查询

下面，我们修改之前的item.lua中的业务，添加json处理功能：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
-- 导入cjson库
local cjson = require('cjson')

-- 获取路径参数
local id = ngx.var[1]
-- 根据id查询商品
local itemJSON = read_http("/item/".. id, nil)
-- 根据id查询商品库存
local itemStockJSON = read_http("/item/stock/".. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)

-- 组合数据，给商品 table新增两个字段 stock、sold，并赋值
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为json 返回结果
ngx.say(cjson.encode(item))
```

- 浏览器请求全流程

1. 浏览器发起 HTTP 请求

浏览器发送 GET 请求：http://192.168.150.101/item/1001，请求到达虚拟机上的 OpenResty。

1. Nginx 匹配 location，转发至 Lua 脚本 item.lua

```nginx
location ~ /item/(\d+) {
  # $1 捕获路径中的商品id，存入ngx.var[1]
  content_by_lua_file /usr/local/openresty/lua/item.lua;
```

请求 URI：/item/1001 匹配该正则 location：

正则捕获数字 1001，存入变量 ngx.var[1]；

执行 Lua 脚本 item.lua，整个业务逻辑在 Lua 中运行。

1. item.lua 加载公共工具模块 common.lua

```lua
local common = require("common")
```

OpenResty 去默认加载目录 /usr/local/openresty/lualib/ 寻找 common.lua；

执行 common.lua 全部代码：定义 read_http 函数、创建 _M 导出表、return _M；

require("common") 接收返回的 _M 对象，赋值给本地变量 common；

提取工具函数：local read_http = common.read_http，简化后续调用。

1. Lua 调用封装好的 read_http，发起 Nginx 内部子请求

```lua
local id = ngx.var[1]
local itemJSON = read_http("/item/".. id, nil)
```

拼接内部路径：/item/1001，无 GET 参数传入 nil；

进入 read_http 函数，执行核心 API：

```lua
local resp = ngx.location.capture("/item/1001", {method=GET, args=nil})
```

ngx.location.capture 是Nginx 内部虚拟请求，不走外网 TCP，仅在 Nginx 内部匹配 location 规则。

1. 内部请求匹配 location /item，反向代理转发到 Windows Tomcat

内部请求 URI /item/1001 匹配配置：

```nginx
location /item {
  proxy_pass http://192.168.88.1:8081;
}
```

Nginx 执行反向代理逻辑：

把内部请求封装为真实 HTTP 请求，发送给 Windows 的 Tomcat 地址 192.168.88.1:8081；

Tomcat 的 item-service 接收 /item/1001 接口，查询 MySQL 商品数据，返回 JSON 字符串。

1. 响应逐层回传给 Lua 脚本 read_http

Tomcat 返回的 JSON 响应原路交给 Nginx 代理；

Nginx 把响应封装为 resp 对象（包含 status、header、body）回传给 capture；

read_http 判断 resp 不为空，直接返回 resp.body（商品 JSON）；

itemJSON 拿到商品 JSON 数据。

1. 重复逻辑查询库存

```lua
local itemStockJSON = read_http("/item/stock/".. id, nil)
```

和上面流程完全一致：

Lua 内部请求 /item/stock/1001 → Nginx 代理到 Tomcat 库存接口 → 返回库存 JSON。

1. Lua 拼接数据，响应返回浏览器

item.lua 拿到商品、库存两段 JSON 后，组装成完整结果，通过 ngx.print() 输出给前端：

```lua
ngx.print(合并后的商品+库存json)
```

数据沿原路返回：OpenResty → 浏览器，页面展示商品信息。

#### 基于ID负载均衡

刚才的代码中，我们的tomcat是单机部署。而实际开发中，tomcat一定是集群模式：

![img](https://cdn.nlark.com/yuque/0/2026/png/59590650/1785741996747-c2c9cf6a-d48c-4018-9988-58537ed68d43.png)

因此，OpenResty需要对tomcat集群做负载均衡。

而默认的负载均衡规则是轮询模式，当我们查询/item/10001时：

- 第一次会访问8081端口的tomcat服务，在该服务内部就形成了JVM进程缓存
- 第二次会访问8082端口的tomcat服务，该服务内部没有JVM缓存（因为JVM缓存无法共享），会查询数据库
- ...

因为轮询的原因，第一次查询8081形成的JVM缓存并未生效，直到下一次再次访问到8081时才可以生效，缓存命中率太低了。

怎么办？

如果能让**同一个商品**，每次查询时都**访问同一个tomcat服务**，那么JVM缓存就一定能生效了。

也就是说，我们需要根据商品id做负载均衡，而不是轮询。

常见负载均衡算法包含：

1. 轮询（默认）
2. 加权轮询
3. IP 哈希
4. URL 哈希 / 参数哈希（你这里需要的商品 id 哈希）
5. 最小连接数
6. 一致性哈希

- 原理

nginx提供了基于请求路径做负载均衡的算法：

nginx根据请求路径做hash运算，把得到的数值对tomcat服务的数量取余，余数是几，就访问第几个服务，实现负载均衡。

例如：

- 我们的请求路径是 /item/10001
- tomcat总数为2台（8081、8082）
- 对请求路径 /item/1001 做hash运算求余的结果为1
- 则访问第一个tomcat服务，也就是8081

只要 id 不变，每次hash运算结果也不会变，那就可以保证同一个商品，一直访问同一个tomcat服务，确保JVM缓存生效。

- 实现

修改/usr/local/openresty/nginx/conf/nginx.conf文件，实现基于ID做负载均衡。

首先，定义tomcat集群，并设置基于路径做负载均衡，在http里写：

```lua
upstream tomcat-cluster {
    hash $request_uri;
    server 192.168.88.1:8081;
    server 192.168.88.1:8082;
}
```

然后，修改对tomcat服务的反向代理，目标指向tomcat集群：

```nginx
location /item {
  proxy_pass http://tomcat-cluster;
}
```

重新加载OpenResty

```bash
nginx -s reload
```

- 测试

启动两台tomcat服务：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741996433-c852a90e-1773-447e-be60-5893e4634128.png)

清空日志后，再次访问页面，可以看到不同id的商品，访问到了不同的tomcat服务：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741998925-1396ee9d-c7ec-449a-8714-a2f883d57d15.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785741999844-d3b6818c-6447-4c99-b4d1-af88673ff89d.png)



### Redis缓存预热

请求应该先查询Redis，Redis未命中再请求tomcat，所以现在来添加Redis缓存。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742025678-58cb7931-96a6-4e6c-a82d-7c9b872165e9.png)

Redis缓存会面临冷启动问题：

**冷启动**：服务刚刚启动时，Redis中并没有缓存，如果所有商品数据都在第一次查询时添加缓存，可能会给数据库带来较大压力。

**缓存预热**：在实际开发中，我们可以利用大数据统计用户访问的热点数据，在项目启动时将这些热点数据提前查询并保存到Redis中。

我们数据量较少，并且没有数据统计相关功能，目前可以在启动时将所有数据都放入缓存中。

1）利用Docker安装Redis

```bash
docker run --name redis -p 6379:6379 -d redis redis-server --appendonly yes
```

2）在item-service服务中引入Redis依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

3）配置Redis地址

```yaml
spring:
  redis:
    host: 192.168.150.101
```

4）编写初始化类

缓存预热需要在项目启动时完成，并且必须是拿到RedisTemplate之后。

这里我们利用 InitializingBean 接口来实现，因为InitializingBean可以在对象被Spring创建并且成员变量全部注入后执行。

```java
@Component
public class RedisHandler implements InitializingBean {

    @Autowired
    private StringRedisTemplate redisTemplate;
    @Autowired
    private IItemService itemService;
    @Autowired
    private IItemStockService stockService;
    // Jackson 序列化工具核心类
    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Override
    // afterPropertiesSet()方法，项目一启动就可以去做初始化
    //所有属性、依赖全部注入完成后，自动执行该方法。
    public void afterPropertiesSet() throws Exception {
        // 初始化缓存
        // 1.查询商品信息
        List<Item> itemList = itemService.list();
        // 2.放入缓存
        for (Item item : itemList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(item);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:id:" + item.getId(), json);
        }

        // 3.查询商品库存信息
        List<ItemStock> stockList = stockService.list();
        // 4.放入缓存
        for (ItemStock stock : stockList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(stock);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:stock:id:" + stock.getId(), json);
        }
    }
}
```

### 查询Redis缓存

现在，Redis缓存已经准备就绪，我们可以再OpenResty中实现查询Redis的逻辑了。如下图红框所示：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742024871-641747a5-066a-490e-94d9-4f141800e585.png)

当请求进入OpenResty之后：

- 优先查询Redis缓存
- 如果Redis缓存未命中，再查询Tomcat

#### 封装Redis工具

OpenResty提供了操作Redis的模块，我们只要引入该模块就能直接使用。但是为了方便，我们将Redis操作封装到之前的common.lua 工具库中。

修改/usr/local/openresty/lualib/common.lua文件：

1）引入Redis模块，并初始化Redis对象

```lua
-- 导入redis，加载 OpenResty 内置的 Redis 客户端库 resty-redis
local redis = require('resty.redis')
-- 创建Redis实例
local red = redis:new()
-- 设置超时时间，以下都为一秒，连接 Redis 服务、向Redis发送命令、接收Redis返回
red:set_timeouts(1000, 1000, 1000)
```

2）封装函数，用来释放Redis连接，其实是放入连接池

OpenResty 的 resty-redis 不推荐直接 red:close() 关闭 TCP 连接（频繁创建 / 销毁 TCP 会产生大量握手开销，性能极差）。

set_keepalive() 不会断开网络，而是把当前 Redis 连接归还到 Nginx 内置连接池缓存起来，后续 Lua 请求可直接复用这条 TCP 连接，减少耗时。

```lua
-- 关闭redis连接 的工具方法，其实是放回连接池
local function close_redis(red)
    -- 连接的空闲时间，单位是毫秒
    local pool_max_idle_time = 10000
    --连接池大小，最大容纳100条空闲连接
    local pool_size = 100
    -- 将连接放回连接池
    -- Lua 函数多返回值：
    -- ok：布尔值，成功返回 true，失败 nil；
    -- err：失败时携带错误描述字符串，成功为 nil。
    local ok, err = red:set_keepalive(pool_max_idle_time, pool_size)
    if not ok then
        ngx.log(ngx.ERR, "放入redis连接池失败: ", err)
    end
end
```

3）封装函数，根据key查询Redis数据

```lua
-- 查询redis的方法 ip和port是redis地址，key是查询的key
local function read_redis(ip, port, key)
    -- 获取一个连接
    local ok, err = red:connect(ip, port)
    if not ok then
        ngx.log(ngx.ERR, "连接redis失败 : ", err)
        return nil
    end
    -- 查询redis
    local resp, err = red:get(key)
    -- 查询失败处理
    if not resp then
        ngx.log(ngx.ERR, "查询Redis失败: ", err, ", key = " , key)
    end
    --得到的数据为空处理
    if resp == ngx.null then
        resp = nil
        ngx.log(ngx.ERR, "查询Redis数据为空, key = ", key)
    end
    close_redis(red)
    return resp
end
```

4）导出

```lua
-- 将方法导出
local _M = {  
    read_http = read_http,
    read_redis = read_redis
}  
return _M
```

完整的common.lua：

```lua
-- 导入redis
local redis = require('resty.redis')
-- 初始化redis
local red = redis:new()
red:set_timeouts(1000, 1000, 1000)

-- 关闭redis连接的工具方法，其实是放入连接池
local function close_redis(red)
    local pool_max_idle_time = 10000 -- 连接的空闲时间，单位是毫秒
    local pool_size = 100 --连接池大小
    local ok, err = red:set_keepalive(pool_max_idle_time, pool_size)
    if not ok then
        ngx.log(ngx.ERR, "放入redis连接池失败: ", err)
    end
end

-- 查询redis的方法 ip和port是redis地址，key是查询的key
local function read_redis(ip, port, key)
    -- 获取一个连接
    local ok, err = red:connect(ip, port)
    if not ok then
        ngx.log(ngx.ERR, "连接redis失败 : ", err)
        return nil
    end
    -- 查询redis
    local resp, err = red:get(key)
    -- 查询失败处理
    if not resp then
        ngx.log(ngx.ERR, "查询Redis失败: ", err, ", key = " , key)
    end
    --得到的数据为空处理
    if resp == ngx.null then
        resp = nil
        ngx.log(ngx.ERR, "查询Redis数据为空, key = ", key)
    end
    close_redis(red)
    return resp
end

-- 封装函数，发送http请求，并解析响应
local function read_http(path, params)
    local resp = ngx.location.capture(path,{
        method = ngx.HTTP_GET,
        args = params,
    })
    if not resp then
        -- 记录错误信息，返回404
        ngx.log(ngx.ERR, "http查询失败, path: ", path , ", args: ", args)
        ngx.exit(404)
    end
    return resp.body
end
-- 将方法导出
local _M = {  
    read_http = read_http,
    read_redis = read_redis
}  
return _M
```

#### 实现Redis查询

接下来，我们就可以去修改 item.lua 文件，实现对Redis的查询了。

查询逻辑是：

- 根据id查询Redis
- 如果查询失败则继续查询Tomcat
- 将查询结果返回

1）修改/usr/local/openresty/lua/item.lua文件，添加一个查询函数：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
local read_redis = common.read_redis
-- 封装查询函数
function read_data(key, path, params)
    -- 查询本地缓存
    local val = read_redis("127.0.0.1", 6379, key)
    -- 判断查询结果
    if not val then
        ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
        -- redis查询失败，去查询http
        val = read_http(path, params)
    end
    -- 返回数据
    return val
end
```

2）而后修改商品查询、库存查询的业务：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742024429-70cbfa9e-d569-45f9-9b82-ae79933300b2.png)

3）完整的item.lua代码：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
local read_redis = common.read_redis
-- 导入cjson库
local cjson = require('cjson')

-- 封装查询函数
function read_data(key, path, params)
    -- 查询本地缓存
    local val = read_redis("127.0.0.1", 6379, key)
    -- 判断查询结果
    if not val then
        ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
        -- redis查询失败，去查询http
        val = read_http(path, params)
    end
    -- 返回数据
    return val
end

-- 获取路径参数
local id = ngx.var[1]

-- 查询商品信息
local itemJSON = read_data("item:id:" .. id,  "/item/" .. id, nil)
-- 查询库存信息
local stockJSON = read_data("item:stock:id:" .. id, "/item/stock/" .. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)
-- 组合数据
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为json 返回结果
ngx.say(cjson.encode(item))
```



### Nginx本地缓存

现在，整个多级缓存中只差最后一环，也就是nginx的本地缓存了。如图：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742045141-0c3ea91c-d5f0-40fe-8667-31f55b1c2db2.png)

#### 本地缓存API

lua_shared_dict 是一块所有 Worker 进程共享的内存区域，所有 Nginx 工作进程读写同一份缓存数据，属于单机全局本地缓存，读写无网络、速度远超 Redis。

1）开启共享字典，在nginx.conf的 http{}块 下添加配置：

```nginx
# 共享字典，也就是本地缓存，名称：item_cache，分配共享内存150MB
lua_shared_dict item_cache 150m; 
```

2）操作共享字典：

```lua
-- 获取共享缓存对象
local item_cache = ngx.shared.item_cache
-- 写缓存, 指定key、value、过期时间，单位s，默认为0代表永不过期
item_cache:set('key', 'value', 1000)
-- 读缓存
local val = item_cache:get('key')
```

#### 实现本地缓存查询

1）修改/usr/local/openresty/lua/item.lua文件，修改read_data查询函数，添加本地缓存逻辑：

```lua
-- 导入共享词典，本地缓存
local item_cache = ngx.shared.item_cache

-- 封装查询函数
function read_data(key, expire, path, params)
    -- 查询本地缓存
    local val = item_cache:get(key)
    if not val then
        ngx.log(ngx.ERR, "本地缓存查询失败，尝试查询Redis， key: ", key)
        -- 查询redis
        val = read_redis("127.0.0.1", 6379, key)
        -- 判断查询结果
        if not val then
            ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
            -- redis查询失败，去查询http
            val = read_http(path, params)
        end
    end
    -- 查询成功，把数据写入本地缓存
    item_cache:set(key, val, expire)
    -- 返回数据
    return val
end
```

2）修改item.lua中查询商品和库存的业务，实现最新的read_data函数：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742045101-3a91baf5-2977-4afc-9991-e43268bcfce6.png)

其实就是多了缓存时间参数，过期后nginx缓存会自动删除，下次访问即可更新缓存。

这里给商品基本信息设置超时时间为30分钟，库存为1分钟。

因为库存更新频率较高，如果缓存时间过长，可能与数据库差异较大。

3）完整的item.lua文件：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
local read_redis = common.read_redis
-- 导入cjson库
local cjson = require('cjson')
-- 导入共享词典，本地缓存
local item_cache = ngx.shared.item_cache

-- 封装查询函数
function read_data(key, expire, path, params)
    -- 查询本地缓存
    local val = item_cache:get(key)
    if not val then
        ngx.log(ngx.ERR, "本地缓存查询失败，尝试查询Redis， key: ", key)
        -- 查询redis
        val = read_redis("127.0.0.1", 6379, key)
        -- 判断查询结果
        if not val then
            ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
            -- redis查询失败，去查询http
            val = read_http(path, params)
        end
    end
    -- 查询成功，把数据写入本地缓存
    item_cache:set(key, val, expire)
    -- 返回数据
    return val
end

-- 获取路径参数
local id = ngx.var[1]

-- 查询商品信息
local itemJSON = read_data("item:id:" .. id, 1800,  "/item/" .. id, nil)
-- 查询库存信息
local stockJSON = read_data("item:stock:id:" .. id, 60, "/item/stock/" .. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)
-- 组合数据
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为json 返回结果
ngx.say(cjson.encode(item))
```

## 缓存同步

大多数情况下，浏览器查询到的都是缓存数据，如果缓存数据与数据库数据存在较大差异，可能会产生比较严重的后果。

所以我们必须保证数据库数据、缓存数据的一致性，这就是缓存与数据库的同步。

### 数据同步策略

缓存数据同步的常见方式有三种：

1. **设置有效期**：给缓存设置有效期，到期后自动删除。再次查询时更新

- 优势：简单、方便
- 缺点：时效性差，缓存过期之前可能不一致
- 场景：更新频率较低，时效性要求低的业务

1. **同步双写**：在修改数据库的同时，直接修改缓存

- 优势：时效性强，缓存与数据库强一致
- 缺点：有代码侵入，耦合度高；
- 场景：对一致性、时效性要求较高的缓存数据

1. **异步通知：**修改数据库时发送事件通知，相关服务监听到通知后修改缓存数据

- 优势：低耦合，可以同时通知多个缓存服务
- 缺点：时效性一般，可能存在中间不一致状态
- 场景：时效性要求一般，有多个服务需要同步

而异步实现又可以基于MQ或者Canal来实现：

1）基于MQ的异步通知：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742045432-491be214-0e01-4ffa-ad20-ebce4f727395.png)

解读：

- 商品服务完成对数据的修改后，只需要**发送一条消息到MQ中**（代码侵入）。
- 缓存服务监听MQ消息，然后完成对缓存的更新

依然有少量的代码侵入。

2）基于Canal的通知

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742045479-ff9eaf15-d694-43fc-ae39-904cdd109125.png)

解读：

- 商品服务完成商品修改后，业务直接结束，没有任何代码侵入
- Canal监听MySQL的binlog 变化，当发现变化后，立即通知缓存服务
- 缓存服务接收到canal通知，更新缓存

代码零侵入



### 认识Canal

Canal [kə'næl]，译意为水道/管道/沟渠，canal是阿里巴巴旗下的一款开源项目，基于Java开发。基于数据库增量日志解析，提供增量数据订阅&消费。GitHub的地址：https://github.com/alibaba/canal

Canal是基于mysql的主从同步来实现的，MySQL主从同步的原理如下：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742068133-6153319b-bd2b-4765-8d08-2ff39398d2fc.png)

- MySQL master 将数据变更写入二进制日志（binary log），其中记录的数据叫做binary log events
- MySQL slave 将 master 的 binary log events拷贝到它的中继日志(relay log)
- MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据

而Canal就是把自己伪装成MySQL的一个slave节点，从而监听master的binary log变化。再把得到的变化信息通知给Canal的客户端，进而完成对其它数据库的同步。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742066573-ca220135-242a-4429-8bca-0208ffae9ab0.png)

### 安装和配置Canal

#### 配置

下面我们就开启mysql的主从同步机制，让Canal来模拟salve

- 开启MySQL主从

Canal是基于MySQL的主从同步功能，因此必须先开启MySQL的主从功能才可以。

这里以之前用Docker运行的mysql为例：

- 开启binlog

打开mysql容器挂载的日志文件，我的在/tmp/mysql/conf目录:

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742066694-faa36932-d636-477f-87a3-5ae8df4b7f0c.png)

修改文件：

```bash
vi /tmp/mysql/conf/my.cnf
```

添加内容：

```bash
log-bin=/var/lib/mysql/mysql-bin
binlog-do-db=heima
```

配置解读：

- log-bin=/var/lib/mysql/mysql-bin：设置binary log文件的存放地址和文件名，叫做mysql-bin
- binlog-do-db=heima：指定对哪个database记录binary log events，这里记录heima这个库

最终效果：

```bash
[mysqld]
skip-name-resolve
character_set_server=utf8
datadir=/var/lib/mysql
server-id=1000
log-bin=/var/lib/mysql/mysql-bin
binlog-do-db=heima
```

- 设置用户权限

接下来添加一个仅用于数据同步的账户，出于安全考虑，这里仅提供对heima这个库的操作权限。

```plsql
create user canal@'%' IDENTIFIED by 'canal';
GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT,SUPER ON *.* TO 'canal'@'%' identified by 'canal';
FLUSH PRIVILEGES;
```

重启mysql容器即可

```bash
docker restart mysql
```

测试设置是否成功：在mysql控制台，输入命令：

```plsql
show master status;
```

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742066554-eed7a7aa-d80b-45c4-a870-4ef29478ead1.png)

#### 安装Canal

- 创建网络

我们需要创建一个网络，将MySQL、Canal、MQ放到同一个Docker网络中：

```bash
docker network create heima
```

让mysql加入这个网络：

```bash
docker network connect heima mysql
```

课前资料中提供了canal的镜像压缩包，大家可以上传到虚拟机，放到 /root/ 下，然后通过命令导入：

```bash
docker load -i /root/canal.tar
# 查看镜像
docker images
```

然后运行命令创建Canal容器：

```bash
docker run -p 11111:11111 --name canal \
-e canal.destinations=heima \
-e canal.instance.master.address=mysql:3306  \
-e canal.instance.dbUsername=canal  \
-e canal.instance.dbPassword=canal  \
-e canal.instance.connectionCharset=UTF-8 \
-e canal.instance.tsdb.enable=true \
-e canal.instance.gtidon=false  \
-e canal.instance.filter.regex=heima\\..* \
--network heima \
-d canal/canal-server:v1.1.5
```

说明:

- -p 11111:11111：这是canal的默认监听端口
- -e canal.instance.master.address=mysql:3306：数据库地址和端口，如果不知道mysql容器地址，可以通过docker inspect 容器id来查看
- -e canal.instance.dbUsername=canal：数据库用户名
- -e canal.instance.dbPassword=canal ：数据库密码
- -e canal.instance.filter.regex=：要监听的表名称

表名称监听支持的语法：

```bash
mysql 数据解析关注的表，Perl正则表达式.
多个正则之间以逗号(,)分隔，转义符需要双斜杠(\\) 
常见例子：
1.  所有表：.*   or  .*\\..*
2.  canal schema下所有表： canal\\..*
3.  canal下的以canal打头的表：canal\\.canal.*
4.  canal schema下的一张表：canal.test1
5.  多个规则组合使用然后以逗号隔开：canal\\..*,mysql.test1,mysql.test2 
```

### 监听Canal

Canal提供了各种语言的客户端，当Canal监听到binlog变化时，会通知Canal的客户端。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742069154-6ad5fbdb-5aa4-4c9a-a8cc-984c6f0dec9d.png)

我们可以利用Canal提供的Java客户端，监听Canal通知消息。当收到变化的消息时，完成对缓存的更新。

不过这里我们会使用GitHub上的第三方开源的canal-starter客户端。地址：https://github.com/NormanGyllenhaal/canal-client

与SpringBoot完美整合，自动装配，比官方客户端要简单好用很多。

- 引入依赖

```xml
<dependency>
  <groupId>top.javatool</groupId>
  <artifactId>canal-spring-boot-starter</artifactId>
  <version>1.2.1-RELEASE</version>
</dependency>
```

- 编写配置

```yaml
canal:
  destination: heima # canal的集群名字，要与安装canal时设置的名称一致
  server: 192.168.88.130:11111 # canal服务地址
```

- 修改Item实体类

通过@Id、@Column、等注解完成Item与数据库表字段的映射：

```java
@Data
@TableName("tb_item")
public class Item {
    @TableId(type = IdType.AUTO)
    @Id
    private Long id;//商品id
    @Column(name = "name")//与表的属性名不一致需要加
    private String name;//商品名称
    private String title;//商品标题
    private Long price;//价格（分）
    private String image;//商品图片
    private String category;//分类名称
    private String brand;//品牌名称
    private String spec;//规格
    private Integer status;//商品状态 1-正常，2-下架
    private Date createTime;//创建时间
    private Date updateTime;//更新时间
    @TableField(exist = false)
    @Transient // 不属于表的字段
    private Integer stock;
    @TableField(exist = false)
    @Transient
    private Integer sold;
}
```

- 编写监听器

通过实现EntryHandler接口编写监听器，监听Canal消息。注意两点：

- 实现类通过@CanalTable("tb_item")指定监听的表信息
- EntryHandler的泛型是与表对应的实体类

```java
@CanalTable("tb_item")
@Component
public class ItemHandler implements EntryHandler<Item> {
    
    @Autowired
    private RedisHandler redisHandler;
    @Autowired
    private Cache<Long, Item> itemCache;
    
    @Override
    public void insert(Item item) {
        // 写数据到JVM进程缓存
        itemCache.put(item.getId(), item);
        // 写数据到redis
        redisHandler.saveItem(item);
    }
    
    @Override
    public void update(Item before, Item after) {
        // 写数据到JVM进程缓存
        itemCache.put(after.getId(), after);
        // 写数据到redis
        redisHandler.saveItem(after);
    }
    
    @Override
    public void delete(Item item) {
        // 删除数据到JVM进程缓存
        itemCache.invalidate(item.getId());
        // 删除数据到redis
        redisHandler.deleteItemById(item.getId());
    }
}
```

在这里对Redis的操作都封装到了RedisHandler这个对象中，是我们之前做缓存预热时编写的一个类，内容如下：

```java
@Component
public class RedisHandler implements InitializingBean {

    @Autowired
    private StringRedisTemplate redisTemplate;

    @Autowired
    private IItemService itemService;
    @Autowired
    private IItemStockService stockService;

    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Override
    public void afterPropertiesSet() throws Exception {
        // 初始化缓存
        // 1.查询商品信息
        List<Item> itemList = itemService.list();
        // 2.放入缓存
        for (Item item : itemList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(item);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:id:" + item.getId(), json);
        }

        // 3.查询商品库存信息
        List<ItemStock> stockList = stockService.list();
        // 4.放入缓存
        for (ItemStock stock : stockList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(stock);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:stock:id:" + stock.getId(), json);
        }
    }

    public void saveItem(Item item) {
        try {
            String json = MAPPER.writeValueAsString(item);
            redisTemplate.opsForValue().set("item:id:" + item.getId(), json);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }

    public void deleteItemById(Long id) {
        redisTemplate.delete("item:id:" + id);
    }
}
```



# 最佳实践

## Redis键值设计

### 优雅的key结构

Redis的Key虽然可以自定义，但最好遵循下面的几个最佳实践约定：

- 遵循基本格式：[业务名称]:[数据名]:[id]
- 长度不超过44字节
- 不包含特殊字符

例如：我们的登录业务，保存用户信息，其key可以设计成如下格式：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742109630-c22d9639-84f1-490d-8ef9-829ca68bbb9c.png)

这样设计的好处：

- 可读性强
- 避免key冲突
- 方便管理
- 更节省内存： key是string类型，底层编码包含int、embstr和raw三种。embstr在小于44字节使用，采用连续内存空间，内存占用更小。当字节数大于44字节时，会转为raw模式存储，在raw模式下，内存空间不是连续的，而是采用一个指针指向了另外一段内存空间，在这段空间里存储SDS内容，这样空间不连续，访问的时候性能也就会收到影响，还有可能产生内存碎片。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742109686-e3711964-33f7-433c-a688-52649994f791.png)

### 拒绝BigKey

BigKey通常以Key的大小和Key中成员的数量来综合判定，例如：

- Key本身的数据量过大：一个String类型的Key，它的值为5 MB。
- Key中的成员数过多：一个ZSET类型的Key，它的成员数量为10,000个。
- Key中成员的数据量过大：一个Hash类型的Key，它的成员数量虽然只有1,000个但这些成员的Value（值）总大小为100 MB

官方推荐阈值（生产通用标准）

- String：value < 10KB
- Hash/List/Set/ZSet：元素数量 < 1000 个

那么如何判断元素的大小呢？redis也给我们提供了命令

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742109691-59ab6d45-c14f-4dd4-b45d-803210260302.png)

#### BigKey的危害

- 网络阻塞

- - 对BigKey执行读请求时，少量的QPS就可能导致带宽使用率被占满，导致Redis实例，乃至所在物理机变慢

- 数据倾斜

- - BigKey所在的Redis实例内存使用率远超其他实例，无法使数据分片的内存资源达到均衡

- Redis阻塞

- - 对元素较多的hash、list、zset等做运算会耗时较旧，使主线程被阻塞

- CPU压力

- - 对BigKey的数据序列化和反序列化会导致CPU的使用率飙升，影响Redis实例和本机其它应用

#### 如何发现BigKey

1. redis-cli --bigkeys

利用redis-cli提供的--bigkeys参数，可以遍历分析所有key，并返回Key的整体统计信息与每个数据的**Top1的big key**(数据不完整)

命令：redis-cli -a 密码 --bigkeys

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742109829-bd6fd7e8-a1da-42bd-9fb7-86a70b4f82f0.png)

1. scan扫描

自己编程，利用scan扫描Redis中的所有key，利用strlen、hlen等命令判断key的长度（此处不建议使用MEMORY USAGE）

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742110683-f66adb85-9ee3-46a9-bda3-ffd497e2d406.png)

scan 命令调用完后每次会返回2个元素，第一个是下一次迭代的光标，第一次光标会设置为0，当最后一次scan 返回的光标等于0时，表示整个scan遍历结束了，第二个返回的是List，一个匹配的key的数组

```java
public class JedisTest {
    private Jedis jedis;

    @BeforeEach
    void setUp() {
        // 1.建立连接
        // jedis = new Jedis("192.168.150.101", 6379);
        jedis = JedisConnectionFactory.getJedis();
        // 2.设置密码
        jedis.auth("123321");
        // 3.选择库
        jedis.select(0);
    }

    final static int STR_MAX_LEN = 10 * 1024;
    final static int HASH_MAX_LEN = 500;

    @Test
    void testScan() {
        int maxLen = 0;
        long len = 0;

        String cursor = "0";
        do {
            // 扫描并获取一部分key
            ScanResult<String> result = jedis.scan(cursor);
            // 记录cursor
            cursor = result.getCursor();
            List<String> list = result.getResult();
            if (list == null || list.isEmpty()) {
                break;
            }
            // 遍历
            for (String key : list) {
                // 判断key的类型
                String type = jedis.type(key);
                switch (type) {
                    case "string":
                        len = jedis.strlen(key);
                        maxLen = STR_MAX_LEN;
                        break;
                    case "hash":
                        len = jedis.hlen(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "list":
                        len = jedis.llen(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "set":
                        len = jedis.scard(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "zset":
                        len = jedis.zcard(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    default:
                        break;
                }
                if (len >= maxLen) {
                    System.out.printf("Found big key : %s, type: %s, length or size: %d %n", key, type, len);
                }
            }
        } while (!cursor.equals("0"));
    }
    
    @AfterEach
    void tearDown() {
        if (jedis != null) {
            jedis.close();
        }
    }

}
```

1. 第三方工具

- 利用第三方工具，如 Redis-Rdb-Tools 分析RDB快照文件，全面分析内存使用情况
- https://github.com/sripathikrishnan/redis-rdb-tools

1. 网络监控

- 自定义工具，监控进出Redis的网络数据，超出预警值时主动告警
- 一般阿里云搭建的云服务器就有相关监控页面

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742111744-a72407e0-025a-44f2-bb66-5d0f6a23f007.png)

#### 如何删除BigKey

BigKey内存占用较多，即便时删除这样的key也需要耗费很长时间，导致Redis主线程阻塞，引发一系列问题。

- redis 3.0 及以下版本

- - 如果是集合类型，则遍历BigKey的元素，先逐个删除子元素，最后删除BigKey

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742111634-caf32517-58bb-4c08-ad2a-b45b190695bc.png)

- Redis 4.0以后

- - Redis在4.0后提供了异步删除的命令：unlink

### 恰当的数据类型

- 例1：比如存储一个User对象，我们有三种存储方式：

1. json字符串

| user:1 | {"name": "Jack", "age": 21} |
| ------ | --------------------------- |
|        |                             |

优点：实现简单粗暴

缺点：数据耦合，不够灵活

1. 字段打散

| user:1:name | Jack |
| ----------- | ---- |
| user:1:age  | 21   |

优点：可以灵活访问对象任意字段

缺点：占用空间大、没办法做统一控制

1. hash（推荐）

| user:1 | name | jack |
| ------ | ---- | ---- |
| age    | 21   |      |

优点：底层使用ZipList，空间占用小，可以灵活访问对象的任意字段

缺点：代码相对复杂

- 例2：假如有hash类型的key，其中有100万对field和value，field是自增id，这个key存在什么问题？如何优化？

| key       | field       | value  |
| --------- | ----------- | ------ |
| someKey   | id:0        | value0 |
| .....     | .....       |        |
| id:999999 | value999999 |        |

存在的问题：

- hash的entry数量超过500时，会使用哈希表而不是ZipList，内存占用较多
- 可以通过hash-max-ziplist-entries配置entry上限。但是如果entry过多就会导致BigKey问题
- 方案一

拆分为string类型

| key       | value       |
| --------- | ----------- |
| id:0      | value0      |
| .....     | .....       |
| id:999999 | value999999 |

存在的问题：

- string结构底层没有太多内存优化，内存占用较多

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742111944-1b68b7a6-b6e4-4c6a-ad0d-8267c442ee40.png)

- 想要批量获取这些数据比较麻烦
- 方案二

拆分为小的hash，将 id / 100 作为key， 将id % 100 作为field，这样每100个元素为一个Hash

| key      | field       | value       |
| -------- | ----------- | ----------- |
| key:0    | id:00       | value0      |
| .....    | .....       |             |
| id:99    | value99     |             |
| key:1    | id:00       | value100    |
| .....    | .....       |             |
| id:99    | value199    |             |
| ....     |             |             |
| key:9999 | id:00       | value999900 |
| .....    | .....       |             |
| id:99    | value999999 |             |

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742111951-8d662ece-75a3-434e-97eb-140ac537ce6e.png)

```java
public class JedisTest {
    private Jedis jedis;

    @BeforeEach
    void setUp() {
        // 1.建立连接
        // jedis = new Jedis("192.168.150.101", 6379);
        jedis = JedisConnectionFactory.getJedis();
        // 2.设置密码
        jedis.auth("123321");
        // 3.选择库
        jedis.select(0);
    }

    @Test
    void testSetBigKey() {
        Map<String, String> map = new HashMap<>();
        for (int i = 1; i <= 650; i++) {
            map.put("hello_" + i, "world!");
        }
        jedis.hmset("m2", map);
    }

    @Test
    void testBigHash() {
        Map<String, String> map = new HashMap<>();
        for (int i = 1; i <= 100000; i++) {
            map.put("key_" + i, "value_" + i);
        }
        jedis.hmset("test:big:hash", map);
    }

    @Test
    void testBigString() {
        for (int i = 1; i <= 100000; i++) {
            jedis.set("test:str:key_" + i, "value_" + i);
        }
    }

    @Test
    void testSmallHash() {
        int hashSize = 100;
        Map<String, String> map = new HashMap<>(hashSize);
        for (int i = 1; i <= 100000; i++) {
            int k = (i - 1) / hashSize;
            int v = i % hashSize;
            map.put("key_" + v, "value_" + v);
            if (v == 0) {
                jedis.hmset("test:small:hash_" + k, map);
            }
        }
    }

    @AfterEach
    void tearDown() {
        if (jedis != null) {
            jedis.close();
        }
    }
}
```

总结：

- Key的最佳实践

- - 固定格式：[业务名]:[数据名]:[id]
  - 足够简短：不超过44字节
  - 不包含特殊字符

- Value的最佳实践：

- - 合理的拆分数据，拒绝BigKey
  - 选择合适数据结构
  - Hash结构的entry数量不要超过1000
  - 设置合理的超时时间



## 批处理优化

### Pipeline

我们的客户端与redis服务器是这样交互的：

- 单个命令的执行流程

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742131306-3c9a62c2-b825-4e71-8ccf-4dc64bcef0fc.png)

- N条命令的执行流程

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742131522-f3bf8daa-4fa4-45c1-99be-5a77fc162645.png)

redis处理指令是很快的，主要花费的时候在于网络传输。

所以要将多条指令批量的传输给redis。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742131544-6f270ba4-e0aa-4316-9953-85cf088c3349.png)

#### MSet

Redis提供了很多Mxxx这样的命令，可以实现批量插入数据，例如：

- mset
- hmset

```bash
MSET k1 v1 k2 v2 k3 v3
```

等价连续执行多条 SET k1 v1; SET k2 v2; SET k3 v3，但只一次网络 IO。

仅支持单一数据结构：MSET 只能操作 String；

不支持差异化过期时间，只能统一处理或写完再额外执行 EXPIRE，增加开销。

批量数据量过大有命令缓冲区风险：一次性传入十万条会造成单条命令数据包巨大，产生 BigKey、阻塞主线程。

利用mset批量插入10万条数据

```java
@Test
void testMxx() {
    String[] arr = new String[2000];
    int j;
    long b = System.currentTimeMillis();
    for (int i = 1; i <= 100000; i++) {
        j = (i % 1000) << 1;
        arr[j] = "test:key_" + i;
        arr[j + 1] = "value_" + i;
        if (j == 0) {
            jedis.mset(arr);
        }
    }
    long e = System.currentTimeMillis();
    System.out.println("time: " + (e - b));
}
```

#### Pipeline

MSET虽然可以批处理，但是却只能操作部分数据类型，因此如果有对复杂数据类型的批处理需要，建议使用Pipeline。

管道内可以混合 SET / HSET / ZADD / LPUSH / DEL / EXPIRE 各种不同结构命令。

```java
@Test
void testPipeline() {
    // 创建管道
    Pipeline pipeline = jedis.pipelined();
    long b = System.currentTimeMillis();
    for (int i = 1; i <= 100000; i++) {
        // 放入命令到管道
        pipeline.set("test:key_" + i, "value_" + i);
        if (i % 1000 == 0) {
            // 每放入1000条命令，批量执行
            pipeline.sync();
        }
    }
    long e = System.currentTimeMillis();
    System.out.println("time: " + (e - b));
}
```

| 特性         | MSET                        | Pipeline                       |
| ------------ | --------------------------- | ------------------------------ |
| 支持数据类型 | 仅 String                   | 全部 Redis 命令、混合多种结构  |
| 命令自由度   | 只能批量 set                | 任意读写、删除、过期、聚合命令 |
| 过期时间     | 所有 key 统一，无法单独设置 | 每条 key 独立设置过期          |
| 底层原理     | Redis 内置单条复合命令      | 客户端缓存多条命令批量发送     |
| 适用场景     | 大批量同类型 String 写入    | 混合多种数据结构、复杂批量操作 |

### 集群下的批处理

执行 MSET、Pipeline 批量命令时，一批命令里的所有 key 必须落在同一个 slot，否则 Redis 直接报错 CROSSSLOT Keys in request don't hash to the same slot。

但在批处理时，可能一次要插入很多条数据，而此时如果Redis是一个集群，这些数据很有可能不会都落在相同的插槽上，这就会导致报错了。

这个时候，我们可以找到4种解决方案：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742132037-11e27f60-8caf-457c-aece-e546153e7b16.png)

第一种方案：串行执行，放弃批量，循环每条数据单独执行SET，不使用任何批处理。

第二种方案：串行slot，简单来说，就是执行前，客户端先计算一下对应的key的slot，一样slot的key就放到一个组里边，不同的，就放到不同的组里边，然后对每个组执行pipeline的批处理，他就能串行执行各个组的命令，这种做法比第一种方法耗时要少，但是缺点呢，相对来说复杂一点，所以这种方案还需要优化一下

第三种方案：并行slot，相较于第二种方案，在分组完成后串行执行，第三种方案，就变成了并行，开多个线程执行各个命令，所以它的耗时就非常短，但是实现也更加复杂。**（推荐）**

第四种：hash_tag，redis计算key的slot的时候，其实是根据key的有效部分来计算的，通过这种方式就能一次处理所有的key，这种方式耗时最短，实现也简单，但是如果通过操作key的有效部分，那么就会导致所有的key都落在一个节点上，产生数据倾斜的问题，所以我们推荐使用第三种方式。

- 串行化执行代码实践

```java
public class JedisClusterTest {

    private JedisCluster jedisCluster;

    @BeforeEach
    void setUp() {
        // 配置连接池
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        poolConfig.setMaxTotal(8);
        poolConfig.setMaxIdle(8);
        poolConfig.setMinIdle(0);
        poolConfig.setMaxWaitMillis(1000);
        HashSet<HostAndPort> nodes = new HashSet<>();
        nodes.add(new HostAndPort("192.168.150.101", 7001));
        nodes.add(new HostAndPort("192.168.150.101", 7002));
        nodes.add(new HostAndPort("192.168.150.101", 7003));
        nodes.add(new HostAndPort("192.168.150.101", 8001));
        nodes.add(new HostAndPort("192.168.150.101", 8002));
        nodes.add(new HostAndPort("192.168.150.101", 8003));
        jedisCluster = new JedisCluster(nodes, poolConfig);
    }

    @Test
    void testMSet() {
        jedisCluster.mset("name", "Jack", "age", "21", "sex", "male");

    }

    @Test
    void testMSet2() {
        Map<String, String> map = new HashMap<>(3);
        map.put("name", "Jack");
        map.put("age", "21");
        map.put("sex", "Male");
        //对Map数据进行分组。根据相同的slot放在一个分组
        //key就是slot，value就是一个组
        Map<Integer, List<Map.Entry<String, String>>> result = map.entrySet()
                .stream()
                .collect(Collectors.groupingBy(
                        entry -> ClusterSlotHashUtil.calculateSlot(entry.getKey()))
                );
        //串行的去执行mset的逻辑
        for (List<Map.Entry<String, String>> list : result.values()) {
            String[] arr = new String[list.size() * 2];
            int j = 0;
            for (int i = 0; i < list.size(); i++) {
                j = i<<2;
                Map.Entry<String, String> e = list.get(0);
                arr[j] = e.getKey();
                arr[j + 1] = e.getValue();
            }
            jedisCluster.mset(arr);
        }
    }

    @AfterEach
    void tearDown() {
        if (jedisCluster != null) {
            jedisCluster.close();
        }
    }
}
```

- Spring集群环境下批处理代码

```java
@Test
void testMSetInCluster() {
    Map<String, String> map = new HashMap<>(3);
    map.put("name", "Rose");
    map.put("age", "21");
    map.put("sex", "Female");
    stringRedisTemplate.opsForValue().multiSet(map);
    
    List<String> strings = stringRedisTemplate.opsForValue().multiGet(Arrays.asList("name", "age", "sex"));
    strings.forEach(System.out::println);
}
```

- 原理分析

在RedisAdvancedClusterAsyncCommandsImpl 类中

首先根据slotHash算出来一个partitioned的map，map中的key就是slot，而他的value就是对应的对应相同slot的key对应的数据

通过 RedisFuture mset = super.mset(op);进行异步的消息发送

```java
@Override
public RedisFuture<String> mset(Map<K, V> map) {

    Map<Integer, List<K>> partitioned = SlotHash.partition(codec, map.keySet());

    if (partitioned.size() < 2) {
        return super.mset(map);
    }

    Map<Integer, RedisFuture<String>> executions = new HashMap<>();

    for (Map.Entry<Integer, List<K>> entry : partitioned.entrySet()) {

        Map<K, V> op = new HashMap<>();
        entry.getValue().forEach(k -> op.put(k, map.get(k)));

        RedisFuture<String> mset = super.mset(op);
        executions.put(entry.getKey(), mset);
    }

    return MultiNodeExecution.firstOfAsync(executions);
}
```



## 服务器端优化

### 持久化配置

Redis的持久化虽然可以保证数据安全，但也会带来很多额外的开销，因此持久化请遵循下列建议：

- 用来做缓存的Redis实例尽量不要开启持久化功能
- 建议关闭RDB持久化功能，使用AOF持久化
- 利用脚本定期手动在slave节点做RDB，实现数据备份
- 设置合理的rewrite阈值，避免频繁的bgrewrite
- 配置no-appendfsync-on-rewrite = yes，禁止在rewrite期间做aof，避免因AOF引起的阻塞
- 部署有关建议：

- - Redis实例的物理机要预留足够内存，应对fork和rewrite
  - 单个Redis实例内存上限不要太大，例如4G或8G。可以加快fork的速度、减少主从同步、数据迁移压力
  - 不要与CPU密集型应用部署在一起
  - 不要与高硬盘负载应用一起部署。例如：数据库、消息队列

### 慢查询优化

#### 什么是慢查询

并不是很慢的查询才是慢查询，而是：在Redis执行时耗时超过某个阈值的命令，称为慢查询。

慢查询的危害：由于Redis是单线程的，所以当客户端发出指令后，他们都会进入到redis底层的queue来执行，如果此时有一些慢查询的数据，就会导致大量请求阻塞，从而引起报错，所以我们需要解决慢查询问题。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742150475-f8140ac3-98c9-42cf-9a58-55bd040701b9.png)

慢查询的阈值可以通过配置指定：

slowlog-log-slower-than：慢查询阈值，单位是微秒。默认是10000，建议1000

慢查询会被放入慢查询日志中，日志的长度有上限，可以通过配置指定：

slowlog-max-len：慢查询日志（本质是一个队列）的长度。默认是128，建议1000

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742150346-7be224ef-630e-448a-b4c8-fb3b33733e84.png)

修改这两个配置可以使用：config set命令：

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742150326-0bef78ff-0540-4711-851f-eb00c2dcd5da.png)

#### 如何查看慢查询

知道了以上内容之后，那么咱们如何去查看慢查询日志列表呢：

- slowlog len：查询慢查询日志长度
- slowlog get [n]：读取n条慢查询日志
- slowlog reset：清空慢查询列表

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742150372-7cbc5a6d-2b3b-4331-ab87-a4c048111588.png)

### 命令及安全配置

安全可以说是服务器端一个非常重要的话题，如果安全出现了问题，那么一旦这个漏洞被一些坏人知道了之后，并且进行攻击，那么这就会给咱们的系统带来很多的损失，所以我们这节课就来解决这个问题。

Redis绑定在0.0.0.0:6379(监听服务器所有网卡地址)，全世界任何一台电脑都能通过公网直接连接你的 Redis 6379 端口，而Redis如果没有做身份认证，会出现严重的安全漏洞，漏洞重现方式：https://cloud.tencent.com/developer/article/1039000

你的云服务器公网 IP 是 120.xx.xx.xx，黑客在自己电脑直接执行：

```bash
redis-cli -h 120.xx.xx.xx -p 6379
就能连上你的 Redis 服务。
```

- 免密登录原理（验证过程）

1. 你本地发起连接：ssh root@服务器IP
2. 服务器随机生成一段随机字符串；
3. 服务器用你的公钥加密这段字符串，发给你的电脑；
4. 你本地用私钥解密字符串，回传给服务器；
5. 服务器对比解密后的内容一致，证明你是合法用户，不用输服务器密码，直接登录。

为什么会出现不需要密码也能够登录呢，主要是Redis考虑到每次登录都比较麻烦，所以Redis就有一种ssh免秘钥登录的方式（如上），生成一对公钥和私钥，私钥放在本地，公钥放在服务器，当我们登录时服务器，在登录时候，会去解析公钥和私钥，如果没有问题，则不需要利用redis的登录也能访问。这种做法本身也很常见，但是这里有一个前提，前提就是公钥必须保存在服务器上才行。但是Redis的漏洞在于：在不登录的情况下，在Linux服务器添加公钥，从而产生漏洞。

漏洞出现的核心的原因有以下几点：

- Redis未设置密码
- 利用了Redis的config set命令动态修改Redis配置
- 使用了Root账号权限启动Redis

所以：如何解决呢？我们可以采用如下几种方案

为了避免这样的漏洞，这里给出一些建议：

- Redis一定要设置密码
- 禁止线上使用下面命令：keys、flushall、flushdb、config set等命令。可以利用rename-command禁用。
- bind：限制网卡，修改成只允许本机或内网网段，禁止外网网卡访问
- 开启防火墙，只放通内网业务服务器网段访问 6379，拒绝所有公网陌生 IP 访问 6379。
- 不要使用Root账户启动Redis
- 尽量不是有默认的端口

### Redis内存划分和内存配置

当Redis内存不足时，无法写入新的key，就会把旧Key删除、响应时间变长(淘汰大量的key会阻塞主线程)、QPS不稳定等问题。当内存使用率达到90%以上时就需要我们警惕，并快速定位到内存占用的原因。

**有关碎片问题分析**

Redis底层分配并不是这个key有多大，它就会分配多大，而是有它自己的分配策略，比如8,16,20等等，假定当前key只需要10个字节，此时分配8肯定不够，那么他就会分配16个字节，多出来的6个字节就不能被使用，这就是我们常说的 **碎片问题**。

碎片问题可以通过重启Redis服务解决。

**进程内存问题分析**

这片内存，通常我们都可以忽略不计。

**缓冲区内存问题分析**

一般包括客户端缓冲区、AOF缓冲区、复制缓冲区等。

客户端缓冲区又包括输入缓冲区和输出缓冲区两种，这部分内存占用波动较大，所以这片内存也是我们需要重点分析的内存问题。

| **内存占用** | **说明**                                                     |
| ------------ | ------------------------------------------------------------ |
| 数据内存     | 是Redis最主要的部分，存储Redis的键值信息。主要问题是BigKey问题、内存碎片问题 |
| 进程内存     | Redis主进程本身运行肯定需要占用内存，如代码、常量池等等；这部分内存大约几兆，在大多数生产环境中与Redis数据占用的内存相比可以忽略。 |
| 缓冲区内存   | 一般包括客户端缓冲区、AOF缓冲区、复制缓冲区等。客户端缓冲区又包括输入缓冲区和输出缓冲区两种。这部分内存占用波动较大，不当使用BigKey，可能导致内存溢出。 |

于是我们就需要通过一些命令，可以查看到Redis目前的内存分配状态：

- info memory：查看内存分配的情况

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742150472-0c0e4079-98e6-4faa-91c2-a890fc433114.png)

- memory xxx：查看key的主要占用情况

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742151457-097e0703-423b-417e-b287-640d85671805.png)

接下来我们看到了这些配置，最关键的缓存区内存如何定位和解决呢？

内存缓冲区常见的有三种：

- 复制缓冲区：主从复制的repl_backlog_buf，如果太小可能导致频繁的全量复制，影响性能。通过replbacklog-size来设置，默认1mb
- AOF缓冲区：AOF刷盘之前的缓存区域，AOF执行rewrite的缓冲区。无法设置容量上限
- 客户端缓冲区：分为输入缓冲区和输出缓冲区，输入缓冲区最大1G且不能设置。输出缓冲区可以设置

以上复制缓冲区和AOF缓冲区 不会有问题，最关键就是客户端缓冲区的问题

客户端缓冲区：缓冲区是服务端为抹平「客户端发送速度」和「Redis 处理速度」的速度差设计的临时内存。

输入缓冲区：暂存客户端发来的所有命令，主线程空闲后读取执行，输入缓冲区最大1G且不能设置，所以这一块我们根本不用担心，如果超过了这个空间，redis会直接断开，因为本来此时此刻就代表着redis处理不过来了，我们需要担心的就是**输出端缓冲区**

输出缓冲区：如果结果数据很大，或者客户端网络慢、读取慢、一次性返回超大批量数据，数据会持续堆积在服务端缓冲区。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742152136-d838fdc9-e718-4943-8068-953ba6953646.png)

我们在使用redis过程中，处理大量的big value，那么会导致我们的输出结果过多，如果输出缓存区过大，会导致redis直接断开，而默认配置的情况下， 其实它是没有大小的，内存可能一下子被占满，会直接导致咱们的redis断开，所以解决方案有两个：

1、设置一个大小

2、增加我们带宽的大小，避免我们出现大量数据从而直接超过了redis的承受能力

### 集群还是主从

集群虽然具备高可用特性，能实现自动故障恢复，但是如果使用不当，也会存在一些问题：

- 集群完整性问题
- 集群带宽问题
- 数据倾斜问题
- 客户端性能问题
- 命令的集群兼容性问题
- lua和事务问题

**问题1：在Redis的默认配置中，如果发现任意一个插槽不可用，则整个集群都会停止对外服务：**

大家可以设想一下，如果有几个slot不能使用，那么此时整个集群都不能用了，我们在开发中，其实最重要的是可用性，所以需要把如下配置修改成no，即有slot不能使用时，我们的redis集群还是可以对外提供服务。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785742152284-443d2a8d-6c47-4c1c-8154-a5dd15ce3110.png)

**问题2：集群带宽问题**

集群节点之间会不断的互相Ping来确定集群中其它节点的状态。每次Ping携带的信息至少包括：

- 插槽信息
- 集群状态信息

集群中节点越多，集群状态信息数据量也越大，10个节点的相关信息可能达到1kb，此时每次集群互通需要的带宽会非常高，这样会导致集群中大量的带宽都会被ping信息所占用，这是一个非常可怕的问题，所以我们需要去解决这样的问题。

**解决途径：**

- 避免大集群，集群节点数不要太多，最好少于1000，如果业务庞大，则建立多个集群。
- 避免在单个物理机中运行太多Redis实例
- 配置合适的cluster-node-timeout值

**问题3、命令的集群兼容性问题**

有关这个问题咱们已经探讨过了，当我们使用批处理的命令时，redis要求我们的key必须落在相同的slot上，然后大量的key同时操作时，是无法完成的，所以客户端必须要对这样的数据进行处理，这些方案我们之前已经探讨过了，所以不再这个地方赘述了。

**问题4、lua和事务的问题**

lua和事务都是要保证原子性问题，如果你的key不在一个节点，那么是无法保证lua的执行和事务的特性的，所以在集群模式是没有办法执行lua和事务的。

**那我们到底是集群还是主从**

单体Redis（主从Redis）已经能达到万级别的QPS，并且也具备很强的高可用特性。如果主从能满足业务需求的情况下，所以如果不是在万不得已的情况下，尽量不搭建Redis集群。