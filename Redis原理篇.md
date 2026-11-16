Redis原理篇

# **Redis数据结构**
## **动态字符串**
<font style="color:#333333;">我们都知道Redis中保存的Key是字符串，value往往是字符串或者字符串的集合。可见字符串是Redis中最常用的一种数据结构。</font>

<font style="color:#333333;">不过Redis没有直接使用C语言中的字符串，因为C语言字符串存在很多问题：</font>

1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">获取字符串长度要遍历计算，C 字符串以 \0 作为结束标记，想要知道长度必须循环遍历直到遇见\0，时间复杂度 O (n)，效率极低</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不是二进制安全，无法存储图片、序列化二进制数据，C 字符串遇到 \0 就判定字符串结束，如果你的数据中间包含\0，后面内容直接丢失。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不支持动态扩容，C 字符串拼接前必须手动计算长度、手动申请内存；频繁追加数据会反复malloc/free，产生大量内存碎片、损耗 CPU。</font>

<font style="color:#333333;">Redis构建了一种新的字符串结构，称为简单动态字符串（Simple Dynamic String），简称SDS。例如，我们执行命令：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912268319-118a9d75-18e2-46ab-a5a3-63776027e3d3.png" width="585" title="" crop="0,0,1,1" id="u3bcb4e98" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">那么Redis将在底层创建两个SDS，其中一个是包含 “name” 的SDS，另一个是包含“虎哥”的SDS。</font>

<font style="color:#333333;">Redis是C语言实现的，其中SDS是一个结构体，源码如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912268390-6e1c1538-3482-4bc9-8276-677fb53fa44c.png" width="678" title="" crop="0,0,1,1" id="ud4b661a3" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">5 种 SDS 头部分类</font>

字符串长度 256 → uint8_t 存不了，直接升级 sdshdr16

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SDS_TYPE_5（sdshdr5）</font>

极短字符串，只存 flags，无单独 len/alloc，最大长度 31；Redis 内部很少对外使用。

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SDS_TYPE_8（sdshdr8，图中结构）</font>

len/alloc 都是 uint8_t（1 字节），最大支持 255 字节字符串；

业务中绝大多数短 key、短 value 都会用这个结构，占用最小。

3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SDS_TYPE_16（sdshdr16）</font>

len/alloc 是 uint16_t（2 字节），最大 65535 字节。

4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SDS_TYPE_32（sdshdr32）</font>

len/alloc uint32_t（4 字节），最大 4GB。

5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SDS_TYPE_64（sdshdr64）</font>

len/alloc uint64_t（8 字节），超大字符串专用。

<font style="color:#333333;">例如，一个包含字符串 “name” 的sds结构如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912268428-18fced69-ab18-433f-870a-308618e22ac6.png" width="555" title="" crop="0,0,1,1" id="u30588108" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">SDS之所以叫做动态字符串，是因为它具备动态扩容的能力，例如一个内容为“hi”的SDS：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912268390-33931a97-386c-49de-b790-8e20e95825d7.png" width="572" title="" crop="0,0,1,1" id="u726536cf" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">假如我们要给SDS追加一段字符串“,Amy”，这里首先会申请新内存空间：</font>

<font style="color:#333333;">如果新字符串小于1M，则新空间为扩展后字符串长度的两倍+1；</font>

<font style="color:#333333;">如果新字符串大于1M，则新空间为扩展后字符串长度+1M+1。称为内存预分配。</font>

<font style="color:#333333;">申请内存的操作很消耗资源，所以要多申请内存空间，以减少内存分配次数。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912268562-338c5823-a532-48f1-ad48-f48fef75de70.png" width="699" title="" crop="0,0,1,1" id="u6b16bd10" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912269041-6e618e57-426b-4c96-85d0-18a42b9d9b48.png" width="470" title="" crop="0,0,1,1" id="u52ea9f2a" class="ne-image" style="font-size: 14px">

## **intset**
Redis 的 Set 有两种底层实现：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Dict（哈希表）：元素是字符串、小数、大数混合，通用实现；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IntSet：只有当集合全部都是整数、且数值很小时，Redis 自动用这个压缩数组存储，内存占用远小于哈希表，专门优化纯数字集合。</font>

<font style="color:#333333;">IntSet 基于整数数组来实现，并且具备长度可变、有序等特征。结构如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912269104-214dbc36-ff88-4c38-a5fd-5ee76def31ad.png" width="641" title="" crop="0,0,1,1" id="u8d4da1e3" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">其中的encoding包含三种模式，表示存储的整数大小不同：</font>

INTSET_ENC_INT16 每个数字占 2字节 范围：-32768 ~ 32767

INTSET_ENC_INT32 每个数字占 4字节 范围：-2147483648 ~ 2147483647

INTSET_ENC_INT64 每个数字占 8字节 超大整数

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912269199-54b51985-2872-475d-a947-d3a5460ac167.png" width="600" title="" crop="0,0,1,1" id="u9d07bb7a" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912269398-af58369c-f38e-4261-b3cf-5ac0e4628ac2.png" width="686" title="" crop="0,0,1,1" id="u9bea617a" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">现在，数组中每个数字：{5，10，20} 都在 int16_t 的范围内，因此采用的编码方式是INTSET_ENC_INT16，每部分占用的字节大小为：</font>

<font style="color:#333333;">encoding：4字节</font>

<font style="color:#333333;">length：4字节</font>

<font style="color:#333333;">contents：2字节 * 3 = 6字节</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912269478-c810b05f-12a5-47cf-9a69-66c26eadd471.png" width="1934" title="" crop="0,0,1,1" id="ubb427100" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">我们向该其中添加一个数字：50000，集合变为：{5，10，20，50000}，50000超出了 int16_t 的范围，intset会自动升级编码方式到合适的大小。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912269953-05a8d377-826f-4c48-91b5-38eafa9858b9.png" width="725" title="" crop="0,0,1,1" id="uf41b3271" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">以当前案例来说流程如下：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">升级编码为INTSET_ENC_INT32，每个整数占4字节，并按照新的编码方式及元素个数扩容数组</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">倒序依次将数组中的元素拷贝到扩容后的正确位置</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis 为了节省内存，不会新开一块独立内存，直接在原内存空间原地扩容，旧数据和新大空间重叠在同一块内存。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">正序拷贝：把 0~1 的 num1 复制到 0~3，但原来 2~3 字节存的 num2，被新 num1 的后半段覆盖冲掉了；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果加入新数字需要扩容，那新数字如果是负数就在第一位，正数就在最后一位，不需要二分查找。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果不需要扩容，IntSet 要求数组永远有序</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">判断如果比最大值大，就插入队尾；如果比最小值小，插入队首。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果都不是，通过二分查找找到插入下标，把新数字插入正确位置</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">最后，将inset的encoding属性改为INTSET_ENC_INT32，将length属性改为4</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912270094-40759727-678f-451a-adde-5525d7b2b36d.png" width="1926" title="" crop="0,0,1,1" id="ude7a4246" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912270344-ea950c45-cbf9-4d38-aad1-ac6356f3b71a.png" width="687" title="" crop="0,0,1,1" id="u95300354" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">源码如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912270463-40374263-abb5-4a4d-a3b4-2542d2ff5f3e.png" width="619" title="" crop="0,0,1,1" id="ubb6d6126" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912271444-4d841c2a-a085-4827-9a69-acc09f711a38.png" width="601" title="" crop="0,0,1,1" id="uc5729aaf" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">小总结：</font>

<font style="color:#333333;">Intset可以看做是特殊的整数数组，具备一些特点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis会确保Intset中的元素唯一、有序</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">具备类型自动升级机制，可以节省内存空间</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">底层采用二分查找方式来查询</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

## **Dict**
Dict 是 Redis 最核心底层结构：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">所有 Redis 的 KV 主数据库、Hash 数据类型、Set（非 intset 时）、ZSet 底层都靠它实现；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">作用：而键与值的映射关系正是通过Dict来实现的，通过 key 快速映射 value，实现 O (1) 增删改查。</font>

<font style="color:#333333;">Dict由三部分组成，分别是：哈希表（DictHashTable）、哈希节点（DictEntry）、字典（Dict）</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">DictHashTable 哈希表</font>

作用：存储哈希节点，通过哈希值计算下标，实现 O (1) 定位节点。

哈希表的核心成员：

entry[]：数组，数组每一格存放 DictEntry* 指针；

size：数组长度，一定是 2 的幂（2、4、8、16、32...）；

sizemask：掩码 = size - 1，用来计算数组下标；

used：当前实际存储的键值对总数量。

Dict 里会存两张哈希表 ht [0]、ht [1]，平时只用 ht [0]；扩容缩容时渐进式迁移到 ht [1]（rehash）。

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">DictEntry 哈希节点（最小单元）</font>

每一个键值对就是一个节点，next是链表指针，当两个 key 算出同一个数组下标，就用next把节点挂在链表尾部。

3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Dict 字典（顶层结构体）</font>

管理两张哈希表、rehash 状态、哈希函数等，对外提供增删改查接口。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912288434-12bc6b77-1a78-47c6-8650-cb296357e10e.png" width="788.6666870117188" title="" crop="0,0,1,1" id="u11357d72" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">当我们向 Dict 添加键值对时，Redis首先根据key计算出hash值（h），然后利用 h & sizemask 与运算来计算元素的数组索引。</font>

位与 & 规则：对应二进制位同时为 1，结果才是 1，否则为 0。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">为什么不用 h % size，非要用 h & sizemask？</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">仅当 size 是 2 的幂时，h & (size-1) 等价于 h % size。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">运算速度更快：CPU 二进制位运算 & 效率远高于取模 %</font>

<font style="color:#333333;">假如我们存储k1=v1，假设k1的哈希值h =1，则1&3 =1，因此k1=v1要存储到数组角标1位置。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912288535-bd5fb11f-e16d-4787-a1a8-5850e2946e7b.png" width="775" title="" crop="0,0,1,1" id="u0a2c330b" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">加入又来一个 k2，k2计算出的数组角标也是1，把新来的元素加入到链表的队首。如果加到队尾，因为是单向链表就要遍历到最后一个元素才能添加新元素。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912288434-8cd64372-bbed-44dd-b972-48b6ffca9c70.png" width="749" title="" crop="0,0,1,1" id="u12a1360a" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912288774-8803276e-5258-4e99-9d6f-6d3f357fc6c6.png" width="799" title="" crop="0,0,1,1" id="ue31366a8" class="ne-image" style="font-size: 14px">

### **<font style="color:#333333;">Dict的扩容</font>**
<font style="color:#333333;">Dict中的HashTable就是数组结合单向链表的实现，当集合中元素较多时，必然导致哈希冲突增多，链表过长，则查询效率会大大降低。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">负载因子：LoadFactor = used/size</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">used：哈希表里实际存放的键值对总数</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">size：哈希表数组长度（一定是 2 的幂：4、8、16、32...）</font>

负载因子含义：数组平均每个下标链表上挂多少个节点。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">为什么要控制负载因子？</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">哈希表靠数组下标快速定位，冲突元素靠链表遍历查找。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">负载因子越大，链表越长，查询时遍历链表次数变多，O (1) 退化成 O (n)，性能暴跌。所以 Redis 会自动扩容，增大数组size，分散节点、缩短链表。</font>

<font style="color:#333333;">Dict在每次新增 键值对 时都会检查负载因子 ，满足以下两种情况时会触发哈希表扩容：</font>

1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">哈希表的 LoadFactor >= 1，并且服务器没有执行 BGSAVE 或者 BGREWRITEAOF 等后台进程；</font>
2. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">哈希表的 LoadFactor > 5 ，强制扩容，无视后台持久化任务。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912288427-17c682a0-1085-4830-b7dd-8ef9758f0f1d.png" width="730" title="" crop="0,0,1,1" id="udb78667c" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缩容 rehash：只有删除数据后，负载因子 LoadFactor < 0.1 才会触发。</font>

新容量 = 大于等于当前 ht[0].used 的最小 2 的整数次幂

硬性下限：新容量不能小于 4，防止数组太小频繁扩容缩容震荡。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912289442-82893898-61fe-44a0-9d35-74cdb507d4b8.png" width="776" title="" crop="0,0,1,1" id="u7f810b44" class="ne-image" style="font-size: 14px">

### **<font style="color:#333333;">Dict的rehash</font>**
<font style="color:#333333;">哈希表扩容 / 缩容后，数组长度 size 变了，对应的 sizemask = size-1 也会变。同一个 key，新旧 sizemask 不同，算出的数组下标完全不一样。所以所有旧表 ht [0] 里的键值对，必须重新算下标，搬到新表 ht [1]，这个整体迁移流程就叫 rehash。</font>

<font style="color:#333333;">过程是这样的：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">计算新hash表的realSize，值取决于当前要做的是扩容还是收缩</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果是扩容，则新size为第一个 大于等于 dict.ht[0].used + 1 的 2^n</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">旧表 used=5，used+1=6，大于等于 6 最小的 2 次幂是 8，新 size=8。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果是收缩，则新size为第一个 大于等于 dict.ht[0].used 的2^n （不得小于4）</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">限制最小 4 是为了避免数组过小，频繁反复缩容扩容。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">按照新的realSize申请内存空间，创建dictht，并赋值给 dict.ht[1]</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">正常业务下 ht [1] 是空的，只有 rehash 时才启用。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">设置dict.rehashidx = 0，标示开始 rehash</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">rehashidx 记录当前迁移到 ht [0] 的第几个数组下标。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">等于 0 代表：从下标 0 开始，逐步迁移；等于 - 1 代表没有在做 rehash。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Dict采用渐进式rehash，每次对Dict进行 增删改查 操作时，都会检查 rehashidx 是否大于 -1，如果是，则执行rehash，迁移 ht [0] 当前 rehashidx 下标整条链表所有节点</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每个 entry 重新计算哈希下标，存入 ht [1]</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">清空 ht [0][rehashidx]，rehashidx += 1，下一次操作迁移下一个下标。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">当rehashidx走完ht[0].size全部下标，说明 ht [0] 所有数据迁移完毕；释放旧ht[0]数组的内存；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">把ht[1]整体赋值给ht[0]；重置ht[1]为空哈希表，留作下一次 rehash 备用；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">最后将 rehashidx 赋值为 -1，代表rehash结束</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">rehash 期间读写规则（保证 ht [0] 只减不增）</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">新增 key：直接插入 ht [1]，绝不写入 ht [0]</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">效果：ht [0] 的数据只会被迁走、不会新增，最终一定会清空。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查询 / 修改 / 删除 key：先查 ht [0]，找不到再查 ht [1]，两边都操作。</font>

<font style="color:#333333;">整个过程可以描述成：</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">新增第五个元素，size 扩容</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912290508-6fd7b10e-51e5-4e62-ac91-670f3101405e.png" width="716" title="" crop="0,0,1,1" id="ub030d476" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">size 从4扩容到8</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912290456-c9c56daa-cd6b-4511-b5e8-40eddc333068.png" width="755" title="" crop="0,0,1,1" id="u491d8804" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">ht [0] 所有数据迁移到 ht[1]</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912290985-cff5ddc0-93f9-4b62-b126-538996169f2a.png" width="742" title="" crop="0,0,1,1" id="u1d74de68" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">把ht[1]整体赋值给ht[0]</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912291671-545ee5ea-96a6-4064-9907-eb53119e8e5d.png" width="756" title="" crop="0,0,1,1" id="u33f7f396" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">重置ht[1]为空哈希表</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912291520-5bc800f4-33de-4218-b599-e0753a7cfd25.png" width="751" title="" crop="0,0,1,1" id="u96fdbb9b" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">小总结：</font>

<font style="color:#333333;">Dict的结构：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">类似java的HashTable，底层是数组加链表来解决哈希冲突</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Dict包含两个哈希表，ht[0]平常用，ht[1]用来rehash</font>

<font style="color:#333333;">Dict的伸缩：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">当LoadFactor大于5或者LoadFactor大于1并且没有子进程任务时，Dict扩容</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">当LoadFactor小于0.1时，Dict收缩</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">扩容大小为第一个大于等于used + 1的2^n</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">收缩大小为第一个大于等于used 的2^n</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Dict采用渐进式rehash，每次访问Dict时执行一次rehash</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">rehash时ht[0]只减不增，新增操作只在ht[1]执行，其它操作在两个哈希表</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

## **ZipList**
ZipList 是一块连续内存实现的紧凑型双端链表，专为节省内存设计，没有普通链表的指针开销；

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内存全部连续，CPU 缓存友好、无内存碎片；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">支持头部、尾部快速压入 / 弹出，理论时间复杂度 O (1)；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">适用场景：Hash、List、ZSet 元素数量少、数据很短时，Redis 自动用它存储。</font>

整体内存固定格式（从头到尾）：

zlbytes(4Byte) + zltail(4B) + zllen(2B) + 若干变长entry节点 + zlend(1B)

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912307434-44e4c741-a01e-4304-aeb0-88817b255502.png" width="754" title="" crop="0,0,1,1" id="u122403d6" class="ne-image" style="font-size: 14px">

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">属性</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">类型</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">长度</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用途</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">zlbytes</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">uint32_t</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">4 字节</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">记录整个压缩列表占用的内存字节数</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">zltail</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">uint32_t</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">4 字节</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">记录最后一个 entry 距离列表起始地址的字节偏移量。</font><br/><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">作用：不用遍历全部节点，直接定位尾部元素，保证尾部 pop/push O (1)，实现双端操作。</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">zllen</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">uint16_t</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">2 字节</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">记录了压缩列表包含的节点数量。 最大值为UINT16_MAX （65534），如果超过这个值，此处会写死为65535，此时该字段失效，必须从头到尾完整遍历所有 entry 才能算出真实数量。</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">entry</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">列表节点</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不定</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">节点的字节数不固定，由节点保存的内容决定</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">zlend</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">uint8_t</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1 字节</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">固定值 0xFF（255），专门标记压缩列表内存结束。</font> |


> <font style="color:rgb(0, 0, 0);">补充：理论 O (1) 只是定位节点的复杂度；</font>
>
> <font style="color:rgb(0, 0, 0);">插入 / 删除会触发整块内存 realloc 搬迁所有元素，实际时间复杂度 O (n)，元素多性能暴跌，因此 ZipList 只适合少量数据。</font>
>

### **<font style="color:#333333;">ZipListEntry</font>**
<font style="color:#333333;">普通双向链表节点需要存 prev、next 两个 8 字节指针，合计 16 字节内存开销；</font>

ZipList 为了压缩内存，不存指针，改用 前一个节点长度(字节数) 反向定位上一个节点，只记录数字长度，内存开销远小于指针。

例如：已知当前节点地址 A，previous_entry_length=300（5 字节存储）

上一个节点地址 = A - 300，无需遍历整条列表，实现反向遍历。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912307486-d4525ea0-274f-4de5-b4a0-d0fa919cc179.png" width="619" title="" crop="0,0,1,1" id="u5bad4138" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">previous_entry_length：前一个节点的长度，占1个或5个字节。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果前一节点的长度小于254字节，则采用1个字节来保存这个长度值</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果前一节点的长度大于254字节，则采用5个字节来保存这个长度值，第一个字节为标识头</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">0xfe</font><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">，后四个字节才是存真实长度数据</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">encoding：编码属性，记录content的数据类型（字符串还是整数）以及长度，占用1个、2个或5个字节</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">字符串编码（最高两位不是 11）：记录字符串字节长度。1、2、5 字节三种规格，适配短 / 中 / 长字符串；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">整数编码（最高两位固定 11）：记录整数字节长度。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">contents：负责保存节点的数据，可以是字符串或整数</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果 encoding 标记为字符串：这里存完整字符数组；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果 encoding 标记为</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">压缩整数</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">：此字段为空，数字直接存在 encoding 内部。</font>

<font style="color:#333333;">ZipList中所有存储长度的数值均采用</font>**<font style="color:#333333;">小端字节序</font>**<font style="color:#333333;">：低位字节存在低内存地址(靠前)，高位字节存在高内存地址(靠后)。</font>

<font style="color:#333333;">例如：数值0x1234，高位0x12(一个字节)，低位0x34(一个字节)，采用小端字节序后实际存储值为：0x3412</font>

适用范围：previous_entry_length 的 4 字节长长度、encoding 里多字节长度、zlbytes/zltail/zllen 全部使用小端序存储，是 Redis ZipList 统一解析标准。

### **<font style="color:#333333;">Encoding编码</font>**
<font style="color:#333333;">ZipListEntry中的encoding编码分为字符串和整数两种：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">字符串：如果encoding是以“00”、“01”或者“10”开头，则证明content是字符串</font>

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码长度</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">字符串大小</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> | 00pppppp                                                     | </font>                                                      |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> | 01pppppp                                                     | qqqqqqqq                                                     |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> | 10000000                                                     | qqqqqqqq                                                     |


<font style="color:#333333;">pppppp 存储占用字节数</font>

<font style="color:#333333;">例如，我们要保存字符串：“ab”和 “bc”</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">"ab"</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912307511-256b449f-8994-45d0-8935-4d25d8cffa12.png" width="571" title="" crop="0,0,1,1" id="u7c0910c6" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">存储"ab"和"bc"的整个ZipList 表示，均用16 进制表示，且采用小端字节序</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912307342-1975dd8f-ea44-45c0-bb62-00b96a70cc28.png" width="787.6666870117188" title="" crop="0,0,1,1" id="ud3280f5a" class="ne-image" style="font-size: 14px">

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">整数：如果encoding是以“11”开始，则证明content是整数，且encoding固定只占用1个字节</font>

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码长度</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">整数类型(contents 占用字节)</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">11000000</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">int16_t 短整数（2 bytes）</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">11010000</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">int32_t 整数（4 bytes）</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">11100000</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">int64_t 长整数（8 bytes）</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">11110000</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">24位有符整数(3 bytes)</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">11111110</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">8位有符整数(1 bytes)</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1111xxxx</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">0字节，无 contents</font> |


1111xxxx 为压缩小整数：直接在xxxx位置保存数值，范围从0001~1101(1~13)，减1后结果为实际值(0~12)

例如，一个ZipList中包含两个整数值：“2” 和 “5”

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">表示 “2”</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912307258-3b85c45c-a366-46ed-aa61-720ba4c446c1.png" width="262" title="" crop="0,0,1,1" id="u7c5c06a8" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">表示 “2” 和 “5”</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912308050-af796965-8448-474a-b3d1-c44924a8d8bb.png" width="591" title="" crop="0,0,1,1" id="ud6084540" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">“2” 和 “5” 整个ZipList，无 contents</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912308215-674d0066-d52e-4d62-99cc-68f8f8b99fb2.png" width="733" title="" crop="0,0,1,1" id="u9c11d536" class="ne-image" style="font-size: 14px">

## **ZipList的连锁更新问题**
<font style="color:#333333;">ZipList的每个Entry都包含previous_entry_length来记录上一个节点的大小，长度是1个或5个字节：</font>

<font style="color:#333333;">如果前一节点的长度小于254字节，则采用1个字节来保存这个长度值。</font>

<font style="color:#333333;">如果前一节点的长度大于等于254字节，则采用5个字节来保存这个长度值，第一个字节为0xfe，后四个字节才是真实长度数据。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">举例</font>

节点 A (252B) → 节点 B (251B) → 节点 C (253B) → 节点 D (250B)

B 的previous_entry_length=252（1 字节）

C 的previous_entry_length=251（1 字节）

D 的previous_entry_length=253（1 字节）

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">在最前面插入一个新节点 X，X 的长度是 300B（≥254）。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">原来的第一个节点 A，它的previous_entry_length原本存 252（1 字节），现在前节点变成 X、长度 300≥254：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">A 的 previous_entry_length 必须从 1 字节扩容成 5 字节；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">A 整个节点整体变长了 5 - 1 = 4 字节。</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">A 变长 4 字节 → 它后面的节点 B 的前节点（A）总长度变大，超过 254：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">B 的 previous_entry_length 也要从 1 字节扩为 5 字节，B 整体又增加 4 字节；</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">B 变长 4 字节 → 下一个节点 C 的前节点长度超标，同样扩容 4 字节；</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">以此类推，后面所有 N 个节点全部要依次扩容、重写内存。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">反向场景</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">一串节点，前面某个大节点（长度 300B，5 字节 prevlen）被删除，它后面第一个节点的前节点长度变回 < 254：</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">该节点previous_entry_length从 5 字节缩为 1 字节，整体缩短 4 字节；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">后序每一个节点的前节点长度同步变小，全部需要修改自身 prevlen，连续更新。</font>

这种一次插入 / 删除，引发后续一串节点全部修改自身previous_entry_length、连续空间重分配的现象，就是连锁更新（Cascade Update）。

<font style="color:#333333;">ZipList这种特殊情况下产生的连续多次空间扩展操作称之为连锁更新（Cascade Update）。新增、删除都可能导致连锁更新的发生。</font>

**<font style="color:#333333;">小总结</font>**

**<font style="color:#333333;">ZipList特性：</font>**

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">压缩列表的可以看做一种连续内存空间的"双向链表"</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">列表的节点之间不是通过指针连接，而是记录上一节点和本节点长度来寻址，内存占用较低</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果列表数据过多，导致链表过长，可能影响查询性能</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">增或删较大数据时有可能发生连续更新问题</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

## **QuickList**
<font style="color:#333333;">问题1：ZipList虽然节省内存，但申请内存必须是连续空间，如果内存占用较多，申请内存效率很低。怎么办？</font>

<font style="color:#333333;">答：找到一大块连续内存很难，为了缓解这个问题，我们必须限制ZipList的长度和entry大小。</font>

<font style="color:#333333;">问题2：但是我们要存储大量数据，超出了ZipList最佳的上限该怎么办？</font>

<font style="color:#333333;">答：我们可以创建多个ZipList来分片存储数据。</font>

<font style="color:#333333;">问题3：一堆分散的 ZipList，怎么串联、统一管理、支持头尾快速读写？</font>

<font style="color:#333333;">答：Redis在3.2版本引入了新的数据结构QuickList，它是一个双端链表，只不过链表中的每个节点都是一个ZipList。</font>

<font style="color:#333333;">问题4：ZipList 为了压缩内存才不存指针，为什么还要加上 QuickList，QuickList 存双向指针了啊，岂不是背道而驰？</font>

<font style="color:#333333;">答：外层双向链表 只给每一块 ZipList配一对 prev/next 指针，不是给每一条数据配指针</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912337957-597efe88-1023-42d5-9d01-3b64ebc9ec15.png" width="833" title="" crop="0,0,1,1" id="ueb47d554" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">为了避免QuickList中的每个ZipList中entry过多，Redis提供了一个配置项：list-max-ziplist-size来限制。</font>

<font style="color:#333333;">如果值为正，则代表ZipList的允许的entry个数的最大值。</font>

<font style="color:#333333;">如果值为负，则代表ZipList的最大内存大小，分5种情况：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-1：每个ZipList的内存占用不能超过4kb</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-2：每个ZipList的内存占用不能超过8kb</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-3：每个ZipList的内存占用不能超过16kb</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-4：每个ZipList的内存占用不能超过32kb</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">-5：每个ZipList的内存占用不能超过64kb</font>

<font style="color:#333333;">其默认值为 -2：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912337820-9d4c81f6-1cfa-4501-8efc-04d41c4a94b9.png" width="595" title="" crop="0,0,1,1" id="ua97bf1b6" class="ne-image" style="font-size: 14px">

除了控制ZipList的大小，QuickList 还可以对节点的ZipList做压缩。通过配置项 list-compress-depth 来控制。

因为链表绝大多数操作**只操作头部、尾部**（LPUSH、RPUSH、LPOP、RPOP），中间的节点很少读写。所以首尾是不压缩的。这个参数是控制首尾不压缩的节点个数：

0：特殊值，代表不压缩

1：链表头部 1 个节点、尾部 1 个节点保持原始不压缩，中间节点压缩

2：链表头部 2 个、尾部 2 个不压缩；其余中间节点压缩。

以此类推

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">举例</font>

链表节点顺序：N1 N2 N3 N4 N5 N6 N7 N8

首尾各 2 个免压缩

不压缩：N1、N2、N7、N8

压缩：N3、N4、N5、N6

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">默认值</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912338143-a3401fd1-2fd0-43e0-8e48-a0ecac57cd12.png" width="575" title="" crop="0,0,1,1" id="u2d290e3d" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">以下是QuickList的和QuickListNode的结构源码：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912337989-6c7586f8-b000-46f5-aee7-b3c0f3c8664e.png" width="864" title="" crop="0,0,1,1" id="uf346ef8d" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">描述当前的这个结构：</font>

<font style="color:#333333;">中间两个节点为压缩后的形式</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912338091-f28c50b7-522a-4d81-b129-2ebdc7645247.png" width="763" title="" crop="0,0,1,1" id="u186bb75b" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">总结：</font>

<font style="color:#333333;">QuickList的特点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">是一个节点为ZipList的双端链表</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">节点采用ZipList，解决了传统链表的内存占用问题</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">控制了ZipList大小，解决连续内存空间申请效率问题</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">中间节点可以压缩，进一步节省了内存</font>

## **SkipList**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">普通有序单向链表：1 → 3 → 5 → 7 → 9</font>

只有一层，查找元素必须从头逐个遍历，查询复杂度 O (n)。

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">SkipList（跳表）首先是链表，但与传统链表相比有几点差异：</font>
1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">元素按照升序排列。支持范围查询（ZRANGE/ZREVRANGE），Redis ZSet 依靠它实现区间遍历。</font>
2. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">每个节点携带多层向前指针，每层指针跨度不一样</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">QuickList 适合做首尾查询，SkipList 适合做中间查询</font>

查找元素全程不用遍历全部节点，平均复杂度 O (log n)。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912338574-647206d7-82d8-49e9-be80-e933735ea083.png" width="870.6666870117188" title="" crop="0,0,1,1" id="ub8b51da0" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912339965-74e5404d-2a30-47b8-9ba2-8140ae0679a2.png" width="890.6666870117188" title="" crop="0,0,1,1" id="uf9b5f7c9" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">结构表示为</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912339549-a8ffa9f4-dc11-4a43-bb1c-284cb4aefc43.png" width="854" title="" crop="0,0,1,1" id="u29f6a3ef" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">小总结：</font>

<font style="color:#333333;">SkipList的特点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">跳跃表是一个双向链表，每个节点都包含score和ele值</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">节点按照score值排序，score值一样则按照ele字典排序</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每个节点都可以包含多层指针，层数是1到32之间的随机数</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不同层指针到下一个节点的跨度不同，层级越高，跨度越大</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">增删改查效率与红黑树基本一致，实现却更简单</font>

## **RedisObject**
<font style="color:#333333;">Redis中的任意数据类型的键和值都会被封装为一个RedisObject，也叫做Redis对象，源码如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912339700-83010872-fcc8-4b6c-8c78-9bc0026c5e8d.png" width="889" title="" crop="0,0,1,1" id="u90c3d23e" class="ne-image" style="font-size: 14px">

redis 对象头要占去 16 个字节，存10个字符串就要占160个字节；

但如果把这10个字符串用集合去存储，对象头只占16个字节。

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">什么是redisObject</font>

<font style="color:#333333;">从Redis的使用者的角度来看，⼀个Redis节点包含多个database（非cluster模式下默认是16个，cluster模式下只能是1个），而一个database维护了从key space到object space的映射关系。这个映射关系的key是string类型，而value可以是多种数据类型，比如：string, list, hash、set、sorted set等。</font>

<font style="color:#333333;">而从Redis内部实现的角度来看，database内的这个映射关系是用⼀个dict来维护的。dict的key固定用⼀种数据结构来表达就够了，这就是动态字符串sds。而value则比较复杂，为了在同⼀个dict内能够存储不同类型的value，这就需要⼀个通用的数据结构，这个通用的数据结构就是 robj，全名是 redisObject。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">robj 的核心作用</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">统一封装：Dict 只存 robj*，不用区分底层是 ZipList / Dict / SkipList / IntSet 等；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">记录类型 type：标记对外数据类型（String/List/Hash/Set/ZSet），对应 TYPE key 命令；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">记录编码 encoding：标记底层真实存储结构（raw/embstr/ziplist/quicklist/intset/skiplist…），对应 OBJECT ENCODING key；</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">引用计数 refcount：实现对象共享、内存自动回收，0 引用时释放底层结构；</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">lru 访问时间：用于内存淘汰策略（LRU/LFU）。</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">Redis的编码方式</font>

<font style="color:#333333;">Redis中会根据存储的数据类型不同，选择不同的编码方式，共包含11种不同类型：</font>

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编号</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码方式</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">说明</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">0</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_RAW</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">raw编码动态字符串</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">1</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_INT</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">long类型的整数的字符串</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">2</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_HT</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">hash表（字典dict）</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">3</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_ZIPMAP</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">已废弃</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">4</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_LINKEDLIST</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">双端链表</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">5</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_ZIPLIST</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">压缩列表</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">6</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_INTSET</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">整数集合</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">7</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_SKIPLIST</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">跳表</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">8</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_EMBSTR</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">embstr的动态字符串</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">9</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_QUICKLIST</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">快速列表</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">10</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_STREAM</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Stream流</font> |


<font style="color:#333333;">Redis中会根据存储的数据类型不同，选择不同的编码方式。每种数据类型的使用的编码方式如下：</font>

| **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据类型</font>** | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码方式</font>** |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_STRING</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">int、embstr、raw</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_LIST</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LinkedList和ZipList(3.2以前)、QuickList（3.2以后）</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_SET</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">intset、HT</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ZSET</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ZipList、HT、SkipList</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_HASH</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ZipList、HT</font> |


示例 1：String 类型

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">type = OBJ_STRING</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码可选：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_INT：数字字符串，直接存 long 整数，省内存；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_EMBSTR：短字符串，robj 和 SDS 连续内存；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_RAW：长字符串，标准 SDS。</font>

示例 2：List 类型

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">type = OBJ_LIST</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码只有：OBJ_ENCODING_QUICKLIST（3.2 后废弃 linkedlist、ziplist 单独使用）</font>

示例 3：ZSet 有序集合

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">type = OBJ_ZSET</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码：OBJ_ENCODING_ZIPLIST（少量数据） / OBJ_ENCODING_SKIPLIST（大数据）</font>

## **<font style="color:#333333;">五种数据结构</font>**
### **String**
<font style="color:#333333;">String是Redis中最常见的数据存储类型：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_RAW</font>
    - **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">触发条件</font>**

满足任意一条：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">字符串长度 > 44 字节；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">EMBSTR 字符串执行修改操作（append、setbit 等）；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">INT 编码执行字符串操作，转成字符串后。</font>
    - **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">特点</font>**
        * <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">基于简单动态字符串（SDS）实现，SDS 支持动态扩容，支持任意字符串修改。</font>
        * <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">存储上限为512mb。</font>
        * <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">robj 结构体、SDS 字符串是两块分开的内存，两次 malloc。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912339440-ade42eee-bab6-4bd8-877d-53baf70a6959.png" width="816" title="" crop="0,0,1,1" id="ub3189c92" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_EMBSTR</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">如果存储的SDS长度小于44字节。</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">此时object head与SDS是一段连续空间。申请内存时只需要调用一次malloc 内存分配，效率更高。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">EMBSTR 是只读编码，不支持修改操作：一旦执行 APPEND 追加、修改字符串，Redis 会重新分配两块独立内存，自动转成 RAW 编码。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912340021-8c0c0733-59b7-44f1-b2fa-59835e3c17c0.png" width="864" title="" crop="0,0,1,1" id="ufd613683" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_INT</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数值在 LONG_MIN ~ LONG_MAX 范围</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">把数字值直接存在 ptr 字段（刚好8字节），不再需要SDS了。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">INCR / DECR / INCRBY / DECRBY 自增自减：直接操作 ptr 里的 long 数字，无需字符串转换，运算速度极快。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">但如果执行 APPEND / SETBIT / GETRANGE / STRLEN 等字符串操作：不能直接操作 long 二进制，必须先把数字转成 SDS 字符串，编码会自动转为 RAW/EMBSTR。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912340853-1544d16b-5201-43ee-82c6-b87e07352378.png" width="605" title="" crop="0,0,1,1" id="u054c8432" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">底层实现方式：动态字符串sds 或者 longString的内部存储结构⼀般是sds（Simple Dynamic String，可以动态扩展内存），但是如果⼀个String类型的value的值是数字，那么Redis内部会把它转成long类型来存储，从而减少内存的使用。</font>

<font style="color:#333333;">sds 源码：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912341536-ba0e2d92-4bdc-4d1e-a293-d5ea2fc7b73e.png" width="721" title="" crop="0,0,1,1" id="u14918351" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912341415-b30e4667-1acd-42b4-ac9b-a7255536d36e.png" width="723" title="" crop="0,0,1,1" id="ud8804c15" class="ne-image" style="font-size: 14px">

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">底层结构</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">触发条件</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内存特点</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">支持修改？</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">INT</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">long 数字，无 SDS</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">字符串是合法 64 位整数</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">开销最小，无堆字符串</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">仅数字运算；字符串操作会转码</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">EMBSTR</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">robj+SDS 连续内存</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">字符串≤44 字节、非纯数字</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">一次 malloc，效率高</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">只读，修改自动转 RAW</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">RAW</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">robj、SDS 两块独立内存</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">长度 > 44 / EMBSTR 修改 / INT 转字符串</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">两次内存分配，支持动态扩容</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">完全支持增删改</font> |


<font style="color:#333333;">确切地说，String在Redis中是用⼀个robj来表示的。</font>

<font style="color:#333333;">用来表示String的robj可能编码成3种内部表示：OBJ_ENCODING_RAW，OBJ_ENCODING_EMBSTR，OBJ_ENCODING_INT。</font>

<font style="color:#333333;">其中前两种编码使用的是sds来存储，最后⼀种OBJ_ENCODING_INT编码直接把string存成了long型。在对string进行incr, decr等操作的时候，如果它内部是OBJ_ENCODING_INT编码，那么可以直接行加减操作；</font>

<font style="color:#333333;">如果它内部是OBJ_ENCODING_RAW或OBJ_ENCODING_EMBSTR编码，那么Redis会先试图把sds存储的字符串转成long型，如果能转成功，再进行加减操作。对⼀个内部表示成long型的string执行 append, setbit, getrange 这些命令，针对的仍然是string的值（即十进制表示的字符串），而不是针对内部表示的long型进行操作。</font>

<font style="color:#333333;">比如字符串”32”，如果按照字符数组来解释，它包含两个字符，它们的ASCII码分别是0x33和0x32。当我们执行命令 setbit key 7 0 的时候，相当于把字符0x33变成了0x32，这样字符串的值就变成了”22”。而如果将字符串”32”按照内部的64位long型来解释，那么它是0x0000000000000020，在这个基础上执行setbit位操作，结果就完全不对了。因此，在这些命令的实现中，会把long型先转成字符串再进行相应的操作。</font>

<font style="color:#333333;"></font>

### **List**
<font style="color:#333333;">Redis的List类型可以从首、尾操作列表中的元素：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912361144-f6b67651-7deb-4462-baa8-6e4ea4e4d50a.png" width="506" title="" crop="0,0,1,1" id="u27a74556" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">哪一个数据结构能满足上述特征？</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LinkedList ：普通链表，可以从双端访问，内存占用较高，内存碎片较多</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ZipList ：压缩列表，可以从双端访问，内存占用低，存储上限低</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">QuickList：LinkedList + ZipList，可以从双端访问，内存占用较低，包含多个ZipList，存储上限高</font>

<font style="color:#333333;">Redis的List结构类似一个双端链表，可以从首、尾操作列表中的元素：</font>

<font style="color:#333333;">在3.2版本之前，Redis采用ZipList和LinkedList来实现List，当元素数量小于512 并且 元素大小小于64字节时采用ZipList编码，有一个条件不满足则采用LinkedList编码。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">旧方案痛点</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">一旦数据量稍大就全部变成纯双向链表，指针内存开销爆炸；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">纯 ZipList 大数据量连锁更新性能极差，两者只能二选一，没有折中方案。</font>

<font style="color:#333333;">在3.2版本之后，Redis统一采用 QuickList 来实现List：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912361171-d1551db6-0175-4be5-9677-5fd26d06a61b.png" width="830.6666870117188" title="" crop="0,0,1,1" id="uecf6f0e5" class="ne-image" style="font-size: 14px">

### **Set结构**
<font style="color:#333333;">Set是Redis中的单列集合，满足下列特点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不保证有序性</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">保证元素唯一</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">求交集、并集、差集</font>

查询元素的频率太频繁了，所以对查询元素的效率要求很高。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912361258-89d455b4-ffd5-4492-b283-91a3d7aa9e61.png" width="573" title="" crop="0,0,1,1" id="u490bc90e" class="ne-image" style="font-size: 14px">

能满足**唯一 + 快速查找**的天然结构就是**哈希表 Dict**。

但当存储的所有数据都是整数，并且元素数量不超过set-max-intset-entries时，Set会采用IntSet编码，以节省内存，因此 Set 存在两种编码自动切换：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_INTSET 整数集合（小量纯整数）</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_HT 哈希字典（海量数据 / 含非整数）</font>

Dict 本是键值对结构，Set 只需要存单个唯一元素，因此复用 Dict 做特殊改造：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">集合元素作为 Dict 的 key；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">所有 value 统一存 NULL（占位，无实际数据）。</font>

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">存储结构</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">适用场景</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查询复杂度</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内存开销</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IntSet</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">连续有序整数数组</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">全整数、数量≤512</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O (logn) 二分查找</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">极低</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">HT(Dict)</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">哈希字典，key 存元素，value=null</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">含字符串 / 数据量大</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">平均 O (1)</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">较高（哈希表指针、冲突链表）</font> |


<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912361211-e078bfea-fac4-4908-b4c3-1849542a0080.png" width="858.6666870117188" title="" crop="0,0,1,1" id="u3ebf4d31" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">结构如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912361248-69f83ccd-4c64-4fad-a34b-9da768b2831d.png" width="701" title="" crop="0,0,1,1" id="ue9b02282" class="ne-image" style="font-size: 14px">

### **ZSET**
<font style="color:#333333;">ZSet也就是SortedSet，其中每一个元素都需要指定一个score值和member值：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可以根据score值排序后</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">member必须唯一</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可以根据member查询分数</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912362228-cc6b91b2-4d50-4ce3-8fe3-7c1a63f7b3a4.png" width="582" title="" crop="0,0,1,1" id="u10dbc260" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">因此，zset底层数据结构必须满足：键值存储、键必须唯一、可排序 这几个需求。</font>

因此 ZSet 设计两套底层编码，自动切换：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_ZIPLIST 压缩列表：少量短元素，节约内存；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">OBJ_ENCODING_SKIPLIST 跳表 + 字典双结构：大数据量，兼顾排序与快速查分。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SkipList：可以</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">排序</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">，并且可以同时存储score和ele值（member）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">HT（Dict）：可以</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">键值存储</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">，并且可以根据key找value，且保证</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">键唯一</font>**

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912362352-4acd017d-6133-4576-9f7b-919b45dc05ff.png" width="747" title="" crop="0,0,1,1" id="ua8686a40" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912362759-8f69af97-4bdc-4707-b727-f282f6c89a36.png" width="640" title="" crop="0,0,1,1" id="u3d1a746b" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">当元素数量不多时，HT和SkipList的优势不明显，而且更耗内存。因此zset还会</font>**<font style="color:#333333;">采用ZipList结构</font>**<font style="color:#333333;">来节省内存，不过需要同时满足两个条件：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">元素数量小于zset_max_ziplist_entries，默认值128</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每个元素都小于zset_max_ziplist_value字节，默认值64</font>

<font style="color:#333333;">ziplist本身没有排序功能，而且没有键值对的概念，因此需要通过编码实现：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">成对存储：连续两个 entry 为一组，先存 member，紧跟存对应 score；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">一组结构：[member][score] [member][score] ...</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">整体按 score 升序排布：score 越小，越靠近 ZipList 头部；score 越大越靠尾部；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查询 member 的逻辑：只能从头到尾顺序遍历 O (n)，找到匹配 member 后取下一个 entry 作为 score。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912362627-813fde58-9622-4b74-9c24-d1e1835375f0.png" width="818" title="" crop="0,0,1,1" id="u2dbdc863" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912362957-5968b01e-f363-43f3-832b-02fdb3079057.png" width="955" title="" crop="0,0,1,1" id="uf0741f67" class="ne-image" style="font-size: 14px">

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">编码</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">底层结构</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查询 member 效率</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">范围排序</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内存占用</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">适用场景</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ZipList</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">连续内存成对存储 member+score</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O (n) 顺序遍历</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O (n) 遍历</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">极低</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">元素少、每个 member 很短</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SkipList+Dict</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">跳表有序存储 + Dict 哈希映射</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O (1) 哈希直查</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O(logn)</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">较高（双结构冗余）</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">元素多、长字符串、频繁区间查询</font> |


### **Hash**
<font style="color:#333333;">Hash结构与Redis中的Zset非常类似：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">都是键值存储</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">都需求根据键获取值</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">键必须唯一</font>

<font style="color:#333333;">区别如下：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">zset的键是member，值是score；hash的键和值都是任意值</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">zset要根据score排序；hash则无需排序</font>

<font style="color:#333333;">底层实现方式：压缩列表ziplist 或者 字典dict</font>

<font style="color:#333333;">当Hash中数据项比较少的情况下，Hash底层才用压缩列表ziplist进行存储数据，随着数据的增加，底层的ziplist就可能会转成dict，具体配置如下：</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">hash-max-ziplist-entries 512</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">hash-max-ziplist-value 64</font>

<font style="color:#333333;">当满足上面两个条件其中之⼀的时候，Redis就使用dict字典来实现hash。</font>

<font style="color:#333333;">Redis的hash之所以这样设计，是因为当ziplist变得很大的时候，它有如下几个缺点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每次插入或修改引发的realloc操作会有更大的概率造成内存拷贝，从而降低性能。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">⼀旦发生内存拷贝，内存拷贝的成本也相应增加，因为要拷贝更大的⼀块数据。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">当ziplist数据项过多的时候，在它上面查找指定的数据项就会性能变得很低，因为ziplist上的查找需要进行遍历。</font>

<font style="color:#333333;">总之，ziplist本来就设计为各个数据项挨在⼀起组成连续的内存空间，这种结构并不擅长做修改操作。⼀旦数据发生改动，就会引发内存realloc，可能导致内存拷贝。</font>

<font style="color:#333333;">hash结构如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912363223-73050b6b-ae53-4477-8826-37befb64f029.png" width="544" title="" crop="0,0,1,1" id="ud9708a0e" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">zset集合如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912363553-af05ece2-9994-4314-b68d-6ec129a902ab.png" width="544" title="" crop="0,0,1,1" id="u8f0812f2" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">因此，Hash底层采用的编码与Zset也基本一致，只需要把排序有关的SkipList去掉即可：</font>

<font style="color:#333333;">Hash结构默认采用ZipList编码，用以节省内存。 ZipList中相邻的两个entry 分别保存field和value</font>

<font style="color:#333333;">当数据量较大时，Hash结构会转为HT编码，也就是Dict。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912364063-6d7c1f93-8451-4d6b-a6ac-53936102b176.png" width="897" title="" crop="0,0,1,1" id="u3adeafc1" class="ne-image" style="font-size: 14px">



# **Redis网络模型**
## **用户空间和内核态空间**
<font style="color:#333333;">服务器大多都采用Linux系统，这里我们以Linux为例来讲解:</font>

<font style="color:#333333;">ubuntu和Centos 都是Linux的发行版，发行版可以看成对linux包了一层壳，任何Linux发行版，其系统内核都是Linux。我们的应用都需要通过Linux内核与硬件交互。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912386133-cc43c412-98e8-4032-9c5a-3885aabb0890.png" width="548" title="" crop="0,0,1,1" id="u9af856d0" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">用户的应用，比如redis，mysql等其实是没有办法去访问我们操作系统的硬件的，所以我们可以通过发行版的这个壳子去访问内核，再通过内核去访问计算机硬件。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912386218-af79ce9e-944a-4dea-a9c3-d8fd90afb02e.png" width="421" title="" crop="0,0,1,1" id="uda8a636b" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">计算机硬件包括，如cpu，内存，网卡等等，内核（通过寻址空间）可以操作硬件的，但是内核需要不同设备的驱动，有了这些驱动之后，内核就可以去对计算机硬件去进行 内存管理，文件系统的管理，进程的管理等等。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912386035-9baac21f-2898-42b9-acf5-736db0de7446.png" width="653" title="" crop="0,0,1,1" id="ue3799903" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">应用会消耗资源，如果不加任何限制，用户去操作随意的去操作我们的资源，就有可能导致一些冲突，甚至有可能导致我们的系统出现无法运行的问题，因此Linux 做了两层隔离手段：内存隔离、权限隔离。</font>

1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">内存隔离</font>

<font style="color:#333333;">进程的寻址空间划分成两部分：</font>**<font style="color:#333333;">内核空间、用户空间</font>**

<font style="color:#333333;">什么是寻址空间呢？</font>

程序不能直接访问物理内存，操作系统给每个进程分配独立虚拟地址空间，通过页表映射到真实物理内存。

虚拟地址是一串无符号整数，程序访问虚拟内存只需虚拟地址，由 MMU 硬件自动翻译为物理地址。

比如一个32位的操作系统，它的带宽就是32，它的虚拟地址就是2的32次方，也就是说它寻址范围就是 0~2^32，这就是寻址空间。2^32 个字节 = 4GB，这个4GB，会有3个GB分给用户空间，会有1GB给内核系统。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户空间（0 ~ 0xBFFFFFFF，共 3GB）：每个进程私有，存放应用代码、堆、栈、动态库；进程之间隔离，互不干扰。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核空间（0xC0000000 ~ 0xFFFFFFFF，共 1GB）：所有进程共享，存放内核代码、驱动、硬件寄存器、内核缓冲区；只有内核能读写。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912385630-35130248-44fb-4700-9583-956027f68862.png" width="350" title="" crop="0,0,1,1" id="ub1f3152b" class="ne-image" style="font-size: 14px">

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">权限隔离</font>

<font style="color:#333333;">在linux中，它们权限分成两个等级，0和3。</font>

<font style="color:#333333;">用户空间只能执行受限的命令（Ring3），而且不能直接调用系统资源，必须通过内核提供的接口来访问。Redis、Java、MySQL 等业务程序全部运行在 Ring3。</font>

<font style="color:#333333;">内核空间可以执行特权命令（Ring0），调用一切系统资源。</font>

<font style="color:#333333;">所以一般情况下，用户的操作是运行在用户空间，而内核运行的数据是在内核空间的。而有的情况下，一个应用程序需要去调用一些特权资源，去调用一些内核空间的操作，所以此时CPU需要在用户态和内核态之间进行切换。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">隔离之后，程序要读写磁盘 / 网络怎么办？</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户程序需要操作硬件（读磁盘、发网络请求）；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主动调用 Linux 提供的系统调用 API接口；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">CPU 从 Ring3 切换到 Ring0，内核接管操作；</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核凭借最高权限操作硬件，拿到结果；</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">切回用户态，把结果还给应用。</font>

<font style="color:#333333;">比如：</font>

<font style="color:#333333;">Linux 为平衡磁盘低速与 CPU 高速，提高IO效率，在内核、用户层各设缓冲区：</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">写数据进磁盘时，用户写进用户缓冲区，内核才能把数据写进磁盘，那内核缓冲区想拿到数据，就要把用户缓冲区的数据拷贝到内核缓冲区，然后写入设备。</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">从磁盘或网卡读数据时，要从设备读取数据到内核缓冲区，然后拷贝到用户缓冲区。</font>

<font style="color:#333333;">针对这个操作：我们的用户在读数据时，会去向内核态申请，想要读取内核的数据，而内核数据要去等待驱动程序从硬件上读取数据，当从磁盘上加载到数据之后，内核会将数据写入到内核的缓冲区中，然后再将数据拷贝到用户态的buffer中，然后再返回给应用程序。</font>

<font style="color:#333333;">整体而言，速度慢的原因是 wait for data（等磁盘或网卡给数据） 和数据拷贝。</font>

<font style="color:#333333;">后面学的五种IO模型就是针对这两个点做优化的。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912385948-805307bf-c759-42ec-9ac3-0fd7f3b016f1.png" width="683" title="" crop="0,0,1,1" id="u5eeb9140" class="ne-image" style="font-size: 14px">

## **五种IO模型**
### **阻塞IO**
<font style="color:#333333;">在《UNIX网络编程》一书中，总结归纳了5种IO模型：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阻塞IO（Blocking IO）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">非阻塞IO（Nonblocking IO）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO多路复用（IO Multiplexing）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">信号驱动IO（Signal Driven IO）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">异步IO（Asynchronous IO）</font>

<font style="color:#333333;">应用程序想要去读取数据，它是无法直接去读取磁盘数据的，需要先到内核里边去等待内核操作硬件拿到数据，这个过程就是1，是需要等待的。等到内核从磁盘上把数据加载出来之后，再把这个数据写给用户的缓存区，这个过程是2。</font>

<font style="color:#333333;">如果是阻塞IO，那么整个过程中，用户从发起读请求开始，一直到读取到数据，都是一个阻塞状态。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912386934-7738b911-200a-4dd5-8cf2-beb568f09a43.png" width="525" title="" crop="0,0,1,1" id="u3f25b34b" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">具体流程如下图：</font>

<font style="color:#333333;">用户去读取数据时，会去先发起 recvform 一个命令，去尝试从内核上加载数据，如果内核没有数据，那么用户就会等待，此时内核会去从硬件上读取数据，内核读取数据之后，会把数据拷贝到用户态，并且返回ok，整个过程，都是阻塞等待的，这就是阻塞IO。</font>

<font style="color:#333333;">总结如下：</font>

<font style="color:#333333;">顾名思义，阻塞IO就是两个阶段都必须阻塞等待：</font>

<font style="color:#333333;">阶段一：等硬件数据</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程尝试读取数据（比如网卡数据）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">此时数据尚未到达，内核需要等待数据</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">此时用户进程也处于阻塞状态</font>

<font style="color:#333333;">阶段二：等内存拷贝</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据到达并拷贝到内核缓冲区，代表已就绪</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">将内核数据拷贝到用户缓冲区</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝过程中，用户进程依然阻塞等待</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝完成，用户进程解除阻塞，处理数据</font>

<font style="color:#333333;">可以看到，阻塞IO模型中，用户进程在两个阶段都是阻塞状态。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912387301-b0b8f5b0-6d93-40a4-94ac-b4a4a4452e50.png" width="657" title="" crop="0,0,1,1" id="u548ecf52" class="ne-image" style="font-size: 14px">

### **非阻塞IO**
<font style="color:#333333;">顾名思义，非阻塞IO的 recvfrom 操作会立即返回结果而不是阻塞用户进程。</font>

<font style="color:#333333;">阶段一：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程尝试读取数据（比如网卡数据）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">此时数据尚未到达，内核需要等待数据</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">返回异常给用户进程</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程拿到error后，再次尝试读取</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">循环往复，不断重复调用recvfrom轮询查询，直到内核缓冲区出现数据。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">环不停查询，CPU 一直满负荷跑空逻辑，耗费体力，这就是CPU 空转</font>

<font style="color:#333333;">阶段二：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">将内核数据拷贝到用户缓冲区</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝过程中，用户进程依然阻塞等待</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝完成，用户进程解除阻塞，处理数据</font>

可以看到，非阻塞IO模型中，用户进程在第一个阶段是非阻塞，第二个阶段是阻塞状态。虽然是非阻塞，但性能并没有得到提高。而且忙等机制会导致CPU空转，CPU使用率暴增。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912387065-ab56df40-8169-4d3b-858e-a88511770e0e.png" width="643" title="" crop="0,0,1,1" id="u5c8256dc" class="ne-image" style="font-size: 14px">

### **IO多路复用**
<font style="color:#333333;">无论是阻塞IO还是非阻塞IO，用户应用在一阶段都需要调用recvfrom来获取数据，差别在于无数据时的处理方案：</font>

<font style="color:#333333;">如果调用recvfrom时，恰好没有数据，阻塞IO会使CPU阻塞，非阻塞IO使CPU空转，都不能充分发挥CPU的作用。如果调用recvfrom时，恰好有数据，则用户进程可以直接进入第二阶段，读取并处理数据。</font>

<font style="color:#333333;">而在单线程情况下，只能依次处理IO事件，如果正在处理的IO事件恰好未就绪（数据不可读或不可写），线程就会被阻塞，所有IO事件都必须等待，性能自然会很差。</font>

<font style="color:#333333;">就比如服务员给顾客点餐，</font>**<font style="color:#333333;">分两步</font>**<font style="color:#333333;">：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">顾客思考要吃什么（等待数据就绪）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">顾客想好了，开始点餐（读取数据）</font>

<font style="color:#333333;">要提高效率有几种办法？</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">增加更多服务员（多线程）</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不排队，谁想好了吃什么（数据就绪了），服务员就给谁点餐（用户应用就去读取数据）</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">用户进程如何知道内核中数据是否就绪呢？</font>

<font style="color:#333333;">这个问题的解决依赖于提出的</font>

<font style="color:#333333;">文件描述符（File Descriptor）：简称FD，是一个从0 开始的无符号整数，用来关联Linux中的一个文件。在Linux中，一切皆文件，例如常规文件、视频、硬件设备、网络套接字（Socket）都对应一个数字FD。</font>

<font style="color:#333333;">进程把一堆需要监听的 Socket FD 打包传给内核，内核在内核空间持续监控所有 FD 的读写状态，并在某个FD可读、可写时通知用户进程，从而避免无效的等待，充分利用CPU资源。</font>

<font style="color:#333333;">阶段一：（阻塞）</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程创建 FD 集合，放入所有要监听的 Socket</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程调用 select(FD集合)，指定要监听的FD集合</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">监听FD对应的多个 socket</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">任意一个或多个 socket 数据就绪则返回 readable</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">此过程中用户进程阻塞</font>

<font style="color:#333333;">阶段二：（阻塞）</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程找到就绪的socket</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">依次调用 recvfrom 读取数据</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核将数据拷贝到用户空间</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程处理数据</font>

<font style="color:#333333;">当用户去读取数据的时候，不再去直接调用recvfrom了，而是调用select的函数，select函数会将需要监听的数据交给内核，由内核去检查这些数据是否就绪了。如果说这个数据就绪了，就会通知应用程序数据就绪，然后来读取数据，再从内核中把数据拷贝给用户态，完成数据处理，如果N多个FD一个都没处理完，此时就进行等待。</font>

<font style="color:#333333;">用IO复用模式，可以确保去读数据的时候，数据是一定存在的，它的效率比原来的阻塞IO和非阻塞IO性能都要高</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912387104-5b570cbc-621e-4296-8f5b-ae9d2b4b1f19.png" width="738" title="" crop="0,0,1,1" id="ua329a1c5" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">IO多路复用是利用单个线程来同时监听多个FD，并在某个FD可读、可写时得到通知，从而避免无效的等待，充分利用CPU资源。不过监听FD的方式、通知的方式又有多种实现，常见的有：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">select</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">poll</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">epoll</font>

<font style="color:#333333;">其中select 和 epool 相当于是当被监听的数据准备好之后，它会把你监听的FD整个数据都发给你，你需要到整个FD中去找，哪些是处理好了的，需要通过遍历的方式，所以性能也并不是那么好。</font>

<font style="color:#333333;">而epoll，则相当于内核准备好了之后，会把准备好的数据，直接发给你，就省去了遍历的动作。</font>

<font style="color:#333333;"></font>

#### **select方式**
<font style="color:#333333;">select是Linux最早的I/O多路复用方案：</font>

<font style="color:#333333;">简单说，就是我们把需要处理的数据封装成FD，然后在用户态时创建一个fd的集合（这个集合的大小是要监听的那个FD的最大值+1），这个集合的长度大小是有限制的（默认1024），同时在这个集合中，标明出来我们要控制哪些数据。</font>

<font style="color:#333333;">比如要监听的数据，是1,2,5 三个数据，此时会执行select函数，然后将整个FD拷贝到内核态，内核态会去遍历用户态传递过来的数据，如果发现数据都没有就绪 就休眠，直到有数据准备好时，就会被唤醒。唤醒之后，再次遍历一遍，看谁准备好了，然后再处理掉没有准备好的数据，最后再将这个FD集合写回到用户态中去，此时用户态就知道有人准备好了，但是对于用户态而言，并不知道谁处理好了，所以用户态也需要去进行遍历，然后找到对应准备好数据的节点，再去发起读请求。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">fd_set（fd集合） 本质是一张位图（bit 数组），每一个 bit 对应一个文件描述符 fd</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">bit = 1：代表这个 fd 是你要监听的；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">bit = 0：代表不监听。</font>

举例：监听 fd=1、2、5，最大 fd=5，集合大小 = 6

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">程序每次 select 前都要把 1、2、5 塞进集合；</font>
    1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">下标	0 1 2 3 4 5</font>
    2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">bit	0 1 1 0 0 1</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">传给内核，内核循环检查 1、2、3、4、5；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">假设只有 fd=5 有数据，内核把 1、2 的标记清除，只保留 5；</font>
    1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">下标	0 1 2 3 4 5</font>
    2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">bit	0 0 0 0 0 1</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">整张 6 位的集合拷贝回用户；</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">程序循环判断 1、2、3、4、5，才发现 fd=5 就绪；</font>
6. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">下一轮循环，原来要监听的 1、2 标记被清成 0 了，所以要把 1、2、5 重新加入集合，重复上面全部流程。</font>

<font style="color:#333333;">我们会发现，这种模式下虽然比阻塞IO和非阻塞IO好，但是依然有些麻烦的事情， 比如说频繁的传递fd集合，频繁的去遍历FD等问题。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912411052-62d5d63b-8b03-48de-82b2-68f354174fd9.png" width="835.6666870117188" title="" crop="0,0,1,1" id="ua6b6635a" class="ne-image" style="font-size: 14px">

#### **poll模式**
<font style="color:#333333;">poll 模式对 select 模式做了简单改进，但性能提升不明显，部分关键代码如下：</font>

<font style="color:#333333;">IO流程：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">创建 pollfd 数组，向其中添加关注的fd信息，数组大小自定义</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">调用 poll 函数，将 pollfd 数组拷贝到内核空间，</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">转链表存储，无上限</font>**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核遍历fd，判断是否就绪</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据就绪或超时后，拷贝 pollfd 数组到用户空间，返回就绪fd数量 n，但不会给出具体是哪几个</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程判断n是否大于0，大于0则遍历pollfd数组，找到就绪的fd</font>

<font style="color:#333333;">与select对比：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">select模式中的fd_set大小固定为1024，而pollfd在内核中采用链表，理论上无上限</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不用每次调用前重置监听集合，events（用户监听事件）和revents（内核就绪标记）完全分离，内核只会修改 revents，不会改动 events</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">监听FD越多，每次遍历消耗时间也越久，性能反而会下降</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912410581-f6395fbf-2186-4714-9970-9d7efc0f767d.png" width="460" title="" crop="0,0,1,1" id="u26434608" class="ne-image" style="font-size: 14px">

#### **epoll函数**
<font style="color:#333333;">epoll 模式是对select和poll的改进，它提供了三个函数：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912411493-e63e9228-a849-44d7-94c5-e5f4d532917b.png" width="877.6666870117188" title="" crop="0,0,1,1" id="u4f08f1f9" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">epoll_create</font>

调用此函数创建eventpoll（poll实例），内核生成一个独立的 epoll 对象，内部维护两个核心容器：

1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">红黑树 -> 记录的事要监听的FD</font>
2. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">一个是链表 -> 一个链表，记录的是就绪的FD</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">epoll_ctl</font>

<font style="color:#333333;">将要监听的数据添加到红黑树上去，并且给每个fd设置一个监听函数 ep_poll_callback，这个函数会在fd数据就绪时触发，就是准备好了，现在就把fd把数据添加到 list_head链表中去。</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">epoll_wait</font>
1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">用户态提前创建空的 events 数组，用来接收就绪事件。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">调用 epoll_wait 进入内核，内核先检查就绪链表 rdllist：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">链表非空：直接把链表里所有就绪 fd 事件拷贝到用户 events 数组，返回就绪数量 n；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">链表为空：进程阻塞休眠，让出 CPU，等待硬件中断唤醒；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户拿到 events 数组，数组里只有就绪 fd，只需要遍历少量活跃连接，不用遍历全部监听 fd；</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">逐个调用 recvfrom 读取数据，完成业务处理。</font>

<font style="color:#333333;">小总结：</font>

<font style="color:#333333;">select模式存在的三个问题：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">能监听的FD最大不超过1024</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每次select都需要把所有要监听的FD都拷贝到内核空间</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每次都要遍历所有FD来判断就绪状态</font>

<font style="color:#333333;">poll模式的问题：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">poll利用链表解决了select中监听FD上限的问题，但依然要遍历所有FD，如果监听较多，性能会下降</font>

<font style="color:#333333;">epoll模式中如何解决这些问题的？</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">基于epoll实例中的红黑树保存要监听的FD，理论上无上限，而且红黑树增删改查效率都非常高</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每个FD只需要执行一次 epoll_ctl 添加到红黑树，以后每次epol_wait无需传递任何参数，无需重复拷贝FD到内核空间</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">利用ep_poll_callback 机制来监听FD状态，无需遍历所有FD，因此性能不会随监听的FD数量增多而下降</font>

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">问题点</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">select</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">poll</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">epoll</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">FD 数量上限</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">固定 1024</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">无硬限</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">无硬限</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">fd 拷贝方式</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每次全量双向拷贝</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每次全量双向拷贝</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">仅 epoll_ctl 拷贝一次</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">就绪检测方式</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核遍历全部 fd</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核遍历全部 fd</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">回调自动存入就绪链表</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户遍历范围</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">所有监听 fd</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">所有监听 fd</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">仅少量就绪 fd</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">复杂度</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O(n)</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O(n)</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">O (活跃连接数)</font> |


#### **epoll中的ET和LT**
<font style="color:#333333;">当FD有数据可读时，我们调用epoll_wait（或者select、poll）可以得到通知。</font>

<font style="color:#333333;">但是事件通知的模式有两种：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LevelTriggered：简称LT，也叫做水平触发（默认）</font>

只要内核缓冲区还有未读完的数据，每次调用 epoll_wait 都会持续通知你这个 fd 可读。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">EdgeTriggered：简称ET，也叫做边沿触发</font>

只有发生 新数据抵达 这一状态跳变，才通知一次。缓冲区剩有旧数据也不会通知。

<font style="color:#333333;">举个栗子：客户端发 2kb 数据，服务端一次只 read 1kb</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">假设一个客户端socket对应的FD已经注册到了epoll实例中</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">客户端socket发送了2kb的数据</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">服务端调用 epoll_wait，得到通知说FD就绪</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">服务端从FD读取了1kb数据</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">再次调用epoll_wait，形成循环</font>

<font style="color:#333333;">如果我们采用LT模式，因为FD中仍有1kb数据，则第5步依然会返回结果，并且得到通知</font>

<font style="color:#333333;">如果我们采用ET模式，因为第4步已经消费了FD可读事件，第5步FD状态没有变化，因此epoll_wait不会返回，数据无法读取，客户端响应超时。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LT（默认）</font>

优点：编程简单，不用一次性读完缓冲区；哪怕一次只读少量数据，下一轮 epoll_wait 还会提醒；

缺点：若代码处理缓慢、缓冲区一直有数据，会频繁唤醒 epoll_wait，产生大量重复事件（消耗性能）。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ET</font>

优点：仅数据新来时通知一次，事件触发次数更少，减少用户态 / 内核态切换开销；高并发海量连接场景性能更好（Nginx 默认使用 ET）。

缺点：编码要求严格，必须循环读完缓冲区全部数据，否则数据滞留；逻辑出错极易造成数据丢失、连接卡死。

结论：

ET模式避免了LT模式可能出现的惊群现象.

ET模式最好结合非阻塞IO读取FD数据，相比LT会复杂一些。

#### **基于epoll的服务器端流程**
<font style="color:#333333;">服务器启动以后，服务端会去调用 epoll_create，创建一个epoll实例，epoll实例中包含两个数据：</font>

<font style="color:#333333;">1、红黑树（为空）：rb_root 用来去记录需要被监听的FD</font>

<font style="color:#333333;">2、链表（为空）：list_head，用来存放已经就绪的FD</font>

<font style="color:#333333;">创建好了之后，会去调用 epoll_ctl 函数，此函数会将需要监听的数据添加到 rb_root 红黑树中，后续每一个新客户端连接 fd，都要再次调用 epoll_ctl 添加到红黑树。并且对红黑树的节点设置回调函数，当这些被监听的数据一旦准备完成，就会被调用，而调用的结果就是将红黑树的fd添加到list_head 链表中去。</font>

<font style="color:#333333;">调用epoll_wait函数，这个函数会去校验是否有数据准备完毕（因为数据一旦准备就绪，就会被回调函数添加到list_head中），在等待了一段时间后(可以进行配置)，如果等够了超时时间，则返回没有数据。如果有，则进一步判断当前是什么事件，如果是建立连接时间，则调用 accept() 接受客户端socket，拿到建立连接的socket，然后建立起来连接，如果是其他事件，则把数据进行写出。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912410385-428033f3-1db0-4347-81ea-1ecb8dee344e.png" width="860" title="" crop="0,0,1,1" id="u8ab8cf10" class="ne-image" style="font-size: 14px">

### **信号驱动IO**
<font style="color:#333333;">信号驱动IO是与内核建立SIGIO的信号关联并设置回调，当内核有FD就绪时，会发出SIGIO信号通知用户，期间用户应用可以执行其它业务，无需阻塞等待。</font>

<font style="color:#333333;">阶段一：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程调用 sigaction 注册 SIGIO 信号的回调处理函数；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核返回成功，开始监听FD</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程不阻塞等待，可以执行其它业务</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">当内核数据就绪后，回调用户进程的SIGIO处理函数</font>

<font style="color:#333333;">阶段二：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">收到SIGIO回调信号</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">调用recvfrom，读取</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核将数据拷贝到用户空间</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户进程处理数据</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912410221-7722d714-8acc-46da-b098-605bd5c8d832.png" width="801" title="" crop="0,0,1,1" id="u9d03427e" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缺点</font>

<font style="color:#333333;">如果瞬间成千上百个 socket 同时就绪，内核会批量产生大量 SIGIO 信号，SIGIO处理函数不能及时处理，可能导致信号队列溢出，而且内核空间与用户空间的频繁信号交互性能也较低。</font>

信号触发会发生用户态 / 内核态切换、保存上下文、打断当前运行代码：

海量并发场景下，信号频繁触发，大量上下文切换严重消耗 CPU，整体性能不如 epoll。

SIGIO 信号只通知 “有 fd 就绪”，不告诉你是哪一个 fd；

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">适用：连接数量少、IO 事件稀疏的简单程序；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不适用：高并发服务（Nginx、Redis、后端网关全部不用）；</font>

主流高并发服务统一选用 epoll 多路复用，解决了信号丢失、频繁切换、无法区分就绪 fd 等全部问题。

### **异步IO**
<font style="color:#333333;">这种方式，不仅仅是用户态在试图读取数据后 不阻塞，而且当内核的数据准备完成后 也不会阻塞。</font>

<font style="color:#333333;">它会由内核将所有数据处理完成后，由内核将数据写入到用户态中，然后才算完成，所以性能极高，不会有任何阻塞，全部都由内核完成，可以看到，异步IO模型中，用户进程在两个阶段都是非阻塞状态。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912411529-25c37e8f-fe10-4e9e-86f6-9db2863ceeb2.png" width="719" title="" crop="0,0,1,1" id="u5b942e9c" class="ne-image" style="font-size: 14px">

Linux 原生 AIO 主要针对磁盘文件 IO，对 socket 网络支持不完善；



### **IO模型对比**
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO 模型</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阶段 1：等待硬件数据</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阶段 2：内核→用户缓冲区拷贝</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">线程是否阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">核心缺点</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">适用场景</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阻塞 IO</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阻塞，线程休眠</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阻塞等待拷贝</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">两阶段全阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">单线程只能处理 1 个连接，高并发需要大量线程，内存、上下文切换开销大</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">简单单机小程序、低并发工具</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">非阻塞 IO</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不阻塞，无数据立刻返回，循环轮询</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝阶段阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">阶段 1 忙等 CPU 100%，阶段 2 阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">循环轮询 CPU 空转，资源浪费</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">几乎不单独使用，仅配合多路复用</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO 多路复用 (epoll)</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">epoll_wait 阻塞等待事件</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">recvfrom 拷贝阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">等待事件时阻塞，读数据拷贝阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拷贝阶段仍会短暂阻塞；select/poll 有遍历缺陷，epoll 已优化</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">网络高并发：Nginx/Redis/Java NIO 主流方案</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">信号驱动 IO</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">完全不阻塞，线程执行业务</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">收到信号后调用 recvfrom 拷贝阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">仅拷贝阶段阻塞</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">大量事件会信号队列溢出丢失事件，无法区分就绪 fd，频繁信号切换损耗性能</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">低并发、IO 事件稀疏小程序，生产环境极少使用</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">异步 AIO</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">完全不阻塞，提交请求直接返回</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">内核后台自动完成拷贝，无需用户线程参与</font> | **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">两阶段全程无阻塞</font>** | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Linux 对网络 socket 支持差，仅磁盘文件完善；API 复杂</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据库、大文件磁盘读写，网络服务不用</font> |


<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912448124-2d797bac-b7fd-4036-929a-c2f660458892.png" width="584" title="" crop="0,0,1,1" id="u64c8853b" class="ne-image" style="font-size: 14px">

## **Redis单线程**
## **<font style="color:#333333;">Redis到底是单线程还是多线程？</font>**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果只看命令执行（核心业务逻辑），答案是单线程</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">所有GET/SET/HGET等读写内存的命令，全部由唯一主线程串行执行，不存在并发执行，天然无线程安全问题。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果看整个 Redis 进程，那么答案就是多线程</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">包含主线程、BIO 后台异步线程、Redis6.0 新增 IO 多线程三类线程，分工完全隔离，互不干涉核心命令执行。</font>

<font style="color:#333333;">在Redis版本迭代过程中，在两个重要的时间节点上引入了多线程的支持：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis v4.0：引入多线程异步处理一些耗时较旧的任务，例如异步删除命令unlink</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis v6.0：在核心网络模型中引入 多线程，进一步提高对于多核CPU的利用率</font>

<font style="color:#333333;">因此，对于Redis的核心网络模型，在Redis 6.0之前确实都是单线程。是利用epoll（Linux系统）这样的IO多路复用技术在事件循环中不断处理客户端情况。</font>

**<font style="color:#333333;">为什么Redis要选择单线程？</font>**

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">抛开持久化不谈，Redis 是纯内存操作，执行速度非常快，它的性能瓶颈是网络延迟而不是执行速度，因此多线程并不会带来巨大的性能提升。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">多线程会导致过多的上下文切换，带来不必要的开销</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">引入多线程会面临线程安全问题，必然要引入线程锁这样的安全手段，实现复杂度增高，而且性能也会大打折扣</font>

Redis 通过IO多路复用来提高网络性能，并且支持各种不同的多路复用实现，并且将这些实现进行封装，提供了统一的高性能事件库API库AE：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912528024-1ad408f4-334a-466e-a8f8-6de1beeac9f6.png" width="529" title="" crop="0,0,1,1" id="u87103c7e" class="ne-image" style="font-size: 14px">

### **单线程和多线程网络模型变更**
<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912528323-7978065e-be28-463c-a6d0-03665cac75e0.png" width="917" title="" crop="0,0,1,1" id="ua8087633" class="ne-image" style="font-size: 14px">

Redis 主线程基于 epoll IO 多路复用，把网络事件拆分成 4 类事件处理器：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">连接应答处理器（acceptHandler）</font>

监听 listenfd，专门处理客户端 TCP 握手、新建连接；

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">客户端请求读处理器（readQueryFromClient）</font>

连接就绪可读时，读取客户端 socket 二进制数据流；

3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令执行处理器</font>

解析缓冲区字节 → 转成 Redis 命令对象 → 单线程操作内存数据结构；

4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令回复写处理器（sendReplyToClient）</font>

命令执行完成，把返回数据写入 socket 缓冲区，回复客户端。

当我们的客户端想要去连接我们服务器，会去先到IO多路复用模型去进行排队，会有一个连接应答处理器，它会去接受读请求，然后又把读请求注册到具体模型中。此时这些建立起来的连接，如果是客户端请求处理器去进行执行命令时，它会去把数据读取出来，然后把数据放入到client中， client 去解析当前的命令转化为redis认识的命令，接下来就开始处理这些命令，从redis中的command中找到这些命令，然后就真正的去操作对应的数据了，当数据操作完成后，会去找到命令回复处理器，再由它将数据写出。

Redis6.0 之前：纯单线程网络模型

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">客户端发起 TCP 连接，内核完成三次握手，listenfd 产生可读事件，epoll_wait 唤醒主线程；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">连接应答处理器执行 accept，生成新客户端 connfd，注册读事件到 epoll 红黑树；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">客户端发送指令（set/get），connfd 可读触发 epoll 事件；</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">读请求处理器：主线程调用 read，把 socket 数据读到客户端缓冲区 client；</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">client 缓冲区解析字节流，转化为 Redis 可识别命令对象；</font>
6. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程串行执行命令，操作 SDS/Hash/ZSet 等内存数据；</font>
7. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">生成响应数据，注册写事件；</font>
8. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令回复处理器：主线程调用 write，把结果写回 socket 发给客户端；</font>
9. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">一轮处理完毕，主线程再次阻塞 epoll_wait，等待下一批网络事件。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">单线程模型特点</font>

所有操作：连接建立、读网络、解析命令、执行内存操作、写回响应

全部由一条主线程串行执行，无其他线程参与网络读写。

瓶颈：高并发大量客户端收发大包数据时，read/write 系统调用占用主线程 CPU，拖慢命令执行。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912528546-d659de91-789a-4016-99db-c9b4795780d8.png" width="866" title="" crop="0,0,1,1" id="u6903b895" class="ne-image" style="font-size: 14px">

Redis6.0+：IO 多线程网络模型

1. 读流程（多线程并行读网络）

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">epoll_wait 主线程捕获一批可读 connfd；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程将就绪 socket 分发到多个 IO 工作线程；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO 多线程并行执行读请求处理器，批量读取 socket 数据存入 client 缓冲区；</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程阻塞等待所有 IO 线程读取完成，回收所有客户端缓冲区数据；</font>
5. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程单线程统一解析、串行执行所有 Redis 内存命令。</font>

2. 写流程（多线程并行回包）

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程执行完所有命令，生成每条客户端的响应数据；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">将待写 socket 分发至 IO 线程池；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO 多线程并行执行命令回复处理器，批量 write 把数据发回客户端；</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程等待全部写操作完成，继续下一轮 epoll 事件循环。</font>

3. 不变的核心逻辑

连接应答处理器（accept 新建连接）依旧主线程单线程执行；

命令解析、内存数据操作、过期键、持久化相关逻辑，全程主线程串行，不存在多线程并发修改数据。

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">环节</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis6 前 纯单线程</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis6+ IO 多线程</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">建立 TCP 连接</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程 accept</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程 accept</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">socket 读客户端数据</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程串行 read</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO 线程池并行批量 read</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">命令解析、内存读写</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程串行执行</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程串行执行（无变化）</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">socket 写响应返回</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">主线程串行 write</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO 线程池并行批量 write</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">线程安全</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">无线程竞争，无需锁</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">IO 线程只操作网络缓冲区，不碰全局数据，无锁竞争</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">多核 CPU 利用</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">只能跑满单个 CPU 核心</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">网络读写分散到多个核心，提升高并发吞吐</font> |


# **Redis通信协议**
## **RESP协议**
<font style="color:#333333;">Redis是一个CS架构的软件，通信一般分两步（不包括pipeline和PubSub）：</font>

1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">客户端（client）向服务端（server）发送一条命令</font>
2. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">服务端解析并执行命令，返回响应结果给客户端</font>

<font style="color:#333333;">因此客户端发送命令的格式、服务端响应结果的格式必须有一个规范，这个规范就是</font>**<font style="color:#333333;">通信协议</font>**<font style="color:#333333;">。</font>

<font style="color:#333333;">而在Redis中采用的是RESP（Redis Serialization Protocol）协议：</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">Redis 1.2版本引入了RESP协议</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">Redis 2.0版本中成为与Redis服务端通信的标准，称为RESP2</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">Redis 6.0版本中，从RESP2升级到了RESP3协议，增加了更多数据类型并且支持6.0的新特性--客户端缓存</font>

<font style="color:#333333;">但目前，默认使用的依然是RESP2协议，也是我们要学习的协议版本（以下简称RESP）。</font>

<font style="color:#333333;">在RESP中，通过首字节的字符来区分不同数据类型，常用的数据类型包括5种：</font>

1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">单行字符串：首字节是 ‘+’ ，后面跟上单行字符串，以CRLF（ "\r\n" ）结尾。例如返回"OK"： "+OK\r\n"</font>
2. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">错误（Errors）：首字节是 ‘-’ ，与单行字符串格式一样，只是字符串是异常信息，例如："-Error message\r\n"</font>
3. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">数值：首字节是 ‘:’ ，后面跟上数字格式的字符串，以CRLF结尾。例如：":10\r\n"</font>
4. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">多行字符串：首字节是 ‘$’ ，表示二进制安全的字符串，记录字符串长度，最大支持512MB：</font>
    1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果大小为0，则代表空字符串："$0\r\n\r\n"</font>
    2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果大小为-1，则代表不存在："$-1\r\n"</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912547565-3f403c7f-4427-4c36-8453-c6a278eb729b.png" width="333" title="" crop="0,0,1,1" id="u6d5fcc2b" class="ne-image" style="font-size: 14px">

5. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">数组：首字节是 ‘*’，记录数组元素个数，再跟上元素，元素数据类型不限：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912547910-2e0987ab-d625-4005-b04c-0bd059cf29e6.png" width="1445" title="" crop="0,0,1,1" id="u8f33c374" class="ne-image" style="font-size: 14px">

## **基于Socket自定义Redis的客户端**
<font style="color:#333333;">Redis支持TCP通信，因此我们可以使用Socket来模拟客户端，与Redis服务端建立连接：</font>

```java
public class Main {
    
    static Socket s;
    static PrintWriter writer;
    static BufferedReader reader;
    
    public static void main(String[] args) {
        try {
            // 1.建立连接
            String host = "192.168.150.101";
            int port = 6379;
            s = new Socket(host, port);
            // 2.获取输出流、输入流
            writer = new PrintWriter(new OutputStreamWriter(s.getOutputStream(), StandardCharsets.UTF_8));
            reader = new BufferedReader(new InputStreamReader(s.getInputStream(), StandardCharsets.UTF_8));
            
            // 3.发出请求
            // 3.1.获取授权 auth 123321
            sendRequest("auth", "123321");
            Object obj = handleResponse();
            System.out.println("obj = " + obj);
            
            // 3.2.set name 虎哥
            sendRequest("set", "name", "虎哥");
            // 4.解析响应
            obj = handleResponse();
            System.out.println("obj = " + obj);
            
            // 3.2.set name 虎哥
            sendRequest("get", "name");
            // 4.解析响应
            obj = handleResponse();
            System.out.println("obj = " + obj);
            
            // 3.2.set name 虎哥
            sendRequest("mget", "name", "num", "msg");
            // 4.解析响应
            obj = handleResponse();
            System.out.println("obj = " + obj);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 5.释放连接
            try {
                if (reader != null) reader.close();
                if (writer != null) writer.close();
                if (s != null) s.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    
    private static Object handleResponse() throws IOException {
        // 读取首字节
        int prefix = reader.read();
        // 判断数据类型标示
        switch (prefix) {
            case '+': // 单行字符串，直接读一行
                return reader.readLine();
            case '-': // 异常，也读一行
                throw new RuntimeException(reader.readLine());
            case ':': // 数字
                return Long.parseLong(reader.readLine());
            case '$': // 多行字符串
                // 先读长度
                int len = Integer.parseInt(reader.readLine());
                if (len == -1) {
                    return null;
                }
                if (len == 0) {
                    return "";
                }
                // 再读数据,读len个字节。我们假设没有特殊字符，所以读一行（简化）
                return reader.readLine();
            case '*':
                return readBulkString();
            default:
                throw new RuntimeException("错误的数据格式！");
        }
    }
    
    private static Object readBulkString() throws IOException {
        // 获取数组大小
        int len = Integer.parseInt(reader.readLine());
        if (len <= 0) {
            return null;
        }
        // 定义集合，接收多个元素
        List<Object> list = new ArrayList<>(len);
        // 遍历，依次读取每个元素
        for (int i = 0; i < len; i++) {
            list.add(handleResponse());
        }
        return list;
    }

    // set name 虎哥
    private static void sendRequest(String ... args) {
        writer.println("*" + args.length);
        for (String arg : args) {
            writer.println("$" + arg.getBytes(StandardCharsets.UTF_8).length);
            writer.println(arg);
        }
        writer.flush();
    }
}

```

# **Redis内存回收**
## **内存过期策略**
<font style="color:#333333;">Redis之所以性能强，最主要的原因就是基于内存存储。然而单节点的Redis其内存大小不宜过大，会影响持久化或主从同步性能。我们可以通过修改配置文件来设置Redis的最大内存：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912547520-34fb2ed8-d512-4421-bec4-47c4f0c5ad10.png" width="501" title="" crop="0,0,1,1" id="ud66d1c32" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">当内存使用达到上限时，就无法存储更多数据了，无法写入新 key。</font>

<font style="color:#333333;">为了解决这个问题，Redis提供了一些策略实现内存回收：</font>**<font style="color:#333333;">内存过期策略</font>**<font style="color:#333333;">。</font>

<font style="color:#333333;">在学习Redis缓存的时候我们说过，可以通过expire命令给Redis的key设置TTL（存活时间）：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912547460-65c3f54c-9400-4f6d-b966-6ab865df0ca1.png" width="564" title="" crop="0,0,1,1" id="u34549028" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">可以发现，当key的TTL到期以后，再次访问name返回的是nil，说明这个key已经不存在了，对应的内存也得到释放。从而起到内存回收的目的。</font>

<font style="color:#333333;">Redis本身是一个典型的key-value内存存储数据库，因此所有的key、value都保存在之前学习过的Dict结构中。不过在其database结构体中，有两个Dict：一个用来记录key-value；另一个用来记录key-TTL。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912547994-9a315791-7454-4746-9b32-ac7c3ede799a.png" width="726" title="" crop="0,0,1,1" id="u1a358c00" class="ne-image" style="font-size: 14px">

每个数据库 redisDb 维护两张独立哈希字典：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">dict（主字典）：存储 key -> RedisObject，记录了 key-value；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">expires（过期字典）：存储 key -> 过期时间戳(ms)，只记录设置了 TTL 的 key，记录 key-ttl。</font>

如果没有设置过期时间，只保存在 dict；如果设置了，两个字典都保存这个key。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912548884-fe1388c0-202a-40a9-bea9-d3c2e3c7e5d6.png" width="830" title="" crop="0,0,1,1" id="ub36d95dc" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">这里有两个问题需要我们思考：</font>

1. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">Redis是如何知道一个key是否过期呢？</font>

<font style="color:#333333;">利用两个Dict分别记录 key-value对 与 key-ttl对，读写 key 时，去 expires 字典查询该 key 对应的时间戳，和当前系统时间对比。</font>

2. <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">那是不是TTL到期就立即删除了呢？</font>

### **<font style="color:#333333;">惰性删除</font>**
<font style="color:#333333;">惰性删除：顾名思义并不是在TTL到期后就立刻删除，而是在访问一个key的时候，检查该key的存活时间，发现已经过期才执行删除。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912548449-85e9ba91-38df-41cb-9a4f-ff48ed217b8d.png" width="840.6666870117188" title="" crop="0,0,1,1" id="u72ebc4c8" class="ne-image" style="font-size: 14px">

优点：CPU 开销极小，删除逻辑分摊到用户请求，无后台扫描损耗；

缺点：内存不友好。如果一批过期 key永久不再被访问，会一直占用内存，造成内存泄漏。



### **<font style="color:#333333;">周期删除</font>**
<font style="color:#333333;">周期删除：顾名思义是通过一个定时任务，周期性的抽样部分过期的key，然后执行删除。</font>

<font style="color:#333333;">抽样会随着任务推进，把所有的key都遍历一遍。</font>

<font style="color:#333333;">执行周期分成两种模式：SLOW/FAST 双模式</font>

1. **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">SLOW模式规则</font>**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">触发时机：服务初始化 initServer() 注册全局定时任务 databasesCron</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">频率：由 server.hz 控制，默认 hz=10 → 每秒执行 10 次，每 100ms 一轮；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">时间上限：单次清理耗时不超过一轮周期(如100ms)的 25%，默认最多 25ms；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">执行规则：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">循环遍历所有 DB，每个 DB 随机抽取 20 个带 TTL 的 key 判断是否过期；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除所有抽样出的过期 key，统计过期比例；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">若未超时(25ms) 且 抽样过期 key 占比 > 10%，继续抽样一轮，否则停止本轮清理。</font>
2. **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">FAST模式规则</font>**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">触发时机：每轮 epoll 事件循环执行前，调用 beforeSleep()</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">限制：两次 FAST 模式间隔不低于2ms，避免频繁扫描占用 CPU；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">时间上限：单次清理最多耗时1ms；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">执行规则：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">先判断：如果全局过期 key 比例＜10%，直接跳过不执行；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">满足条件则遍历 DB，每库抽样 20 个 key，删除过期数据；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">未超时且过期比例 > 10%，重复抽样，否则结束。</font>

<font style="color:#333333;">小总结：</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">RedisKey的TTL记录方式</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">在RedisDB中通过一个Dict记录每个Key的TTL时间</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">过期key的删除策略</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">惰性清理：每次查找key时判断是否过期，如果过期则删除</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">定期清理：定期抽样部分key，判断是否过期，如果过期则删除。定期清理的两种模式：</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">SLOW模式执行频率默认为10，间隔100ms，每次不超过25ms</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">FAST模式执行频率不固定，但两次间隔不低于2ms，每次耗时不超过1ms</font>

## **内存淘汰策略**
<font style="color:#333333;">内存淘汰：就是当Redis内存使用达到设置的上限时，主动挑选部分key删除以释放更多内存。</font>

<font style="color:#333333;">在处理每条客户端命令的入口函数 processCommand() 内部，会调用 freeMemoryIfNeeded() 检查内存：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">只读命令（GET、HGET）：不会新增内存，不会触发淘汰；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">写命令（SET、LPUSH、HSET）：会占用内存，执行前强制检查内存，超限则循环淘汰 Key，直到内存低于 maxmemory，才执行当前写入命令。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912563976-0bf22d57-dcd5-4e70-b91a-87cf7a3dd963.png" width="641" title="" crop="0,0,1,1" id="ua204af31" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">淘汰策略</font>**

<font style="color:#333333;">Redis支持8种不同策略来选择要删除的key：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">noeviction： 不淘汰任何key，但是内存满时不允许写入新数据，默认就是这种策略。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">volatile-ttl： 对设置了TTL的key，比较key的剩余TTL值，TTL越小越先被淘汰</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">allkeys-random：对全体key ，随机进行淘汰。也就是直接从db->dict中随机挑选</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">volatile-random：对设置了TTL的key ，随机进行淘汰。也就是从db->expires中随机挑选。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">allkeys-lru： 对全体key，基于LRU算法进行淘汰（</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">生产最常用</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">volatile-lru： 对设置了TTL的key，基于LRU算法进行淘汰</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">allkeys-lfu： 对全体key，基于LFU算法进行淘汰（</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">热点频繁变化场景</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">volatile-lfu： 对设置了TTL的key，基于LFU算法进行淘汰</font>

比较容易混淆的有两个：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LRU（Least Recently Used），最少最近使用。用当前时间减去最后一次访问时间，这个值越大则淘汰优先级越高。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LFU（Least Frequently Used），最少频率使用。会统计每个key的访问频率，值越小淘汰优先级越高。</font>

<font style="color:#333333;">Redis的数据都会被封装为RedisObject结构：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912564818-e767a9b6-0e6a-462e-bbd7-7e84ffd2f909.png" width="766" title="" crop="0,0,1,1" id="udb18f1c1" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">LFU的访问次数之所以叫做 </font>**<font style="color:#333333;">逻辑访问次数</font>**<font style="color:#333333;">，不是真实访问次数：LFU 计数器不是每次访问直接 + 1。</font>

<font style="color:#333333;">因为记录访问次数只有 8bit，最大值固定 255。热点key可能有会被访问成千上万次，255次不够计数。</font>

<font style="color:#333333;">所以要通过运算：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">生成0~1之间的随机数R；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">计算 P = 1 / (当前计数器值 × lfu_log_factor + 1)，（默认 lfu_log_factor=10）；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果 R < P ，则计数器 + 1，最大值封顶 255；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">计数器值越来越大，则P越来越小，P>R的概率也越来越小；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">访问次数会随时间衰减，距离上一次访问时间每隔 lfu_decay_time (默认1)分钟，计数器 -1；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">长期无人访问的 Key，计数持续降低，内存淘汰时优先被删掉。</font>

特性：计数器数值越小（低频 Key），P 越大，更容易 + 1；高频 Key 增长越来越慢，防止快速拉满 255 无法区分热度。

举个例子：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">KeyA：每秒被访问 1 万次</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">KeyB：每秒被访问 100 次</font>

不加概率增长：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">KeyA 短时间内疯狂 + 1，几天就顶到 255；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">KeyB 慢慢涨，最后也涨到 255；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">此时两个 key 计数器都是 255，淘汰时 Redis 只能认为：两个 key 热度完全一样。</font>

<font style="color:#333333;">最后用一副图来描述当前的这个流程：</font>

1. **<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">入口分支：内存充足判断</font>**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">菱形判断：内存是否充足</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">是 → 直接结束淘汰流程（E），执行用户写命令；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">否 → 进入下一层判断。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">第二层判断：策略是否为NO_EVICTION</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">是：拒绝写入，返回内存溢出错误，流程结束；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">否：进入正式淘汰逻辑。</font>
2. **<font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">分支区分：AllKeys / Volatile 筛选范围</font>**

菱形判断：是否是 AllKeys 系列策略

+ <font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">是（allkeys-lru/allkeys-lfu/allkeys-random）</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">：从全局主字典</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">db->dict</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">筛选所有 key；</font>
+ <font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">否（volatile-lru/volatile-lfu/volatile-random/volatile-ttl）</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">：仅从过期字典</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">db->expires</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">筛选带 TTL 的 key。</font>
3. **<font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">两大淘汰执行分支</font>**

**<font style="color:#1f2329;">分支 A</font>**<font style="color:#1f2329;">：RANDOM 随机淘汰（allkeys-random /volatile-random）</font>

1. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">遍历所有数据库，随机挑选 1 个符合范围的 key；</font>
2. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">直接删除该 key，释放内存；</font>
3. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">判断当前释放内存是否满足阈值：</font>
    1. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">满足 → 结束淘汰；</font>
    2. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">不满足 → 回到随机挑选逻辑，继续循环删除。</font>

**<font style="color:#1f2329;">分支 B</font>**<font style="color:#1f2329;">：LRU / LFU / TTL 近似采样淘汰（核心流程）</font>

采用**<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">eviction_pool 淘汰池</font>**存储本轮最优待删除 key，核心是<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">maxmemory-samples</font>采样机制：

1. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">初始化空的</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">eviction_pool</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">淘汰池；</font>
2. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">循环遍历每一个 DB：</font>
    1. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">随机取出</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">maxmemory-samples</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">个符合筛选范围的 key；</font>
    2. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">根据策略计算</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">idleTime</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">（数值越大，越优先删除）：</font>
        1. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">TTL 策略：</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">idleTime = maxTTL - 当前剩余TTL</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">，剩余过期时间越短，idleTime 越大；</font>
        2. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">LRU 策略：</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">idleTime = 当前时间 - key最后访问时间</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">，闲置越久 idleTime 越大；</font>
        3. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">LFU 策略：</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">idleTime = 255 - key的LFU计数</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">，访问频次越低 idleTime 越大；</font>
    3. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">校验当前 key 是否可存入淘汰池：按</font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">idleTime</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">升序存入池子，池子只保留本轮采样中</font>**<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">最适合删除</font>**<font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">的 key；</font>
    4. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">判断是否存在下一个 DB，存在则继续采样；</font>
3. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">全部 DB 采样完成后，</font>**<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">倒序从 eviction_pool 取出 idleTime 最大的 key（最优淘汰对象）</font>**<font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">；</font>
4. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">删除该 key，释放内存；</font>
5. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">判断释放内存是否达标：</font>
    1. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">满足 → 淘汰流程结束；</font>
    2. <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">不满足 → 回到 eviction_pool 初始化步骤，重新全库采样一轮。</font>
+ <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">eviction_pool 淘汰池作用</font>

Redis 不全局排序所有 key（会阻塞主线程），采用**<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">采样择优</font>**方案：每次随机取一批 key，放入淘汰池对比冷热 / 过期时间，只删除这批里最差的 key，平衡精度与主线程性能。

+ <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">idleTime 统一设计思想</font>

三种非随机策略统一用<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">idleTime</font>作为淘汰权重：**<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">数值越大，越优先删除</font>**

表格

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">策略</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">idleTime 计算公式</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">淘汰逻辑</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">volatile-ttl</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">maxTTL - 剩余存活时间</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">剩余寿命越短，值越大，优先删</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">allkeys/volatile-lru</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">当前时间 - 最后访问时间</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">闲置越久，值越大，优先删</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">allkeys/volatile-lfu</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">255 - LFU 计数器</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">访问频次越低，值越大，优先删</font> |


+ <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">循环终止条件</font>

每删除一个 key 后都会校验释放内存总量，只有内存占用低于<font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">maxmemory</font>限制时，才会停止淘汰，否则持续循环采样删除。

+ <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">Volatile 与 AllKeys 数据源区分</font>
    - <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">AllKeys：数据源 </font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">db->dict</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">（全量业务 key）</font>
    - <font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">Volatile：数据源 </font><font style="color:rgb(31, 35, 41);background-color:rgba(0, 0, 0, 0);">db->entries(expires过期字典)</font><font style="color:#1f2329;background-color:rgba(0, 0, 0, 0);">（仅设置过 expire 的 key）</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785912564334-66661113-d017-4ace-9b14-c4eacd9e9265.png" width="894.6666870117188" title="" crop="0,0,1,1" id="uf47c41f6" class="ne-image" style="font-size: 14px">