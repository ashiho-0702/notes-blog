# 初识Linux

## 操作系统概述

### 操作系统作用

- 我们所熟知的计算机是由：硬件和软件所组成。

- - 硬件：计算机系统中由电子，机械和光电元件等组成的各种物理装置的总称。
  - 软件：是用户和计算机硬件之间的接口和桥梁，用户通过软件与计算机进行交流。

而操作系统，就是软件的一类。它负责作为用户和硬件之间的桥梁，调度和管理硬件进行工作，即计算机 = 硬件 + 操作系统。

当计算机拥有了操作系统，就相当于拥有了灵魂，操作系统可以：调度CPU进行工作、调度内存进行工作、调度硬盘进行数据存储、调度网卡进行网络通讯、调度音响发出声音、调度打印机打印内容……

简单理解：操作系统就是让硬件干活的。

### 常见操作系统

个人桌面操作系统：Window、macOS

服务器操作系统：Linux

## Linux概述

### Linux内核

Linux操作系统 = 内核 + 系统级应用程序

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831320384-198fa865-c851-4bd1-aec3-d2ca529c7cc0.png)

内核提供系统最核心的功能，如：调度CPU、调度内存、调度文件系统、调度网络通讯、调度IO等。

系统级应用程序，可以理解为出厂自带程序，可供用户快速上手操作系统。

比如，播放音乐，无论用户使用自带音乐播放器或是自行安装的第三方播放器，

均是由播放器程序，调用内核提供的相关功能，由内核调度CPU解码、音响发声等。

可以看出，内核是Linux操作系统最核心的所在，系统级应用程序只是锦上添花。

Linux内核是免费开源的，任何人都可以下载内核源码并查看且修改。

可以通过：https://www.kernel.org去下载Linux内核

### Linux发行版

内核是免费、开源的，这也就代表了：

- 任何人都可以获得并修改内核，并且自行集成系统级程序
- 提供了内核 + 系统级程序的完整封装，称之为Linux发行版

不同发行版基础命令都是相同的，仅部分操作不同（如软件安装）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831321064-c4789803-4c6f-4ca8-aecf-2574d81a1c73.png)

## 虚拟机

不可能重装系统成Linux，所以要借助虚拟机来获取Linux系统环境

借助虚拟化技术，我们可以在系统中，通过软件来模拟计算机硬件，并给虚拟硬件安装真实的操作系统。

这样，就可以在电脑中，虚拟出一个完整的电脑，以供我们学习Linux系统。

### 虚拟化软件

通过虚拟化软件获取虚拟机，如VMware

把安装好的CentOS放到VMware上启动，就可以了

## 远程连接Linux系统

用 FinalShell 软件远程连接到Linux操作系统，通过 FinalShell 操作Linux系统会很方便。

- 操作

在VMware终端输入：ifconfig，复制 ip 地址

点击蓝色文件夹，再点击白色文件夹，再点击SSH连接(Linux)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831320167-2fed3d2c-0172-4eb5-9d1c-adced08b1ed5.png)

把 ip 粘贴到 “主机”

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831320168-b7fee3ec-aa24-4da6-98fb-3df6bdd96bc6.png)

最后点击“接受并保存”。

- 修改IP地址操作

点击齿轮

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831320230-4f9192fa-a79a-4302-88a1-130890b9bde8.png)

## WSL

WSL (Windows Subsystem for Linux)

微软官方给 Windows 系统加的 “Linux 模式”，直连计算机硬件，不用装虚拟机，以非常轻量化方式，就能得到 Linux 环境，

即Ubuntu操作系统环境。

打开Ubuntu：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831321179-da185259-b5d5-4e63-b6e3-635e7cb1de20.png)

## 虚拟机快照

在学习阶段我们无法避免的可能损坏Linux操作系统。

如果损坏的话，重新安装一个Linux操作系统就会十分麻烦。

VMware 虚拟机支持为虚拟机制作快照。

通过快照将当前虚拟机的状态保存下来，在以后可以通过快照恢复虚拟机到保存的状态。

- 快照操作

1. 把虚拟机关机

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831321363-1c4acd47-bac6-4af7-aa8a-b30eeda8f6b8.png)

2. 右键点击主机名，点击快照

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831321538-671913a1-0601-4815-a858-786d6cc6e94e.png)

- 恢复快照操作

1. 虚拟机关机

2. 选择要恢复的状态，再点击“转到”

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831321713-387a0d08-fb7a-4693-8356-1a77624f1601.png)

## 初识Linux文件

红色 = 压缩包 / 归档文件（.tar.gz .zip .rar 等）

绿色 = 可执行文件

白色 = 普通文本文件

蓝色 = 文件夹

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831322177-29d2b1a7-ecb1-4704-a42f-8b039ff43511.png)

# 基础命令

## Linux的目录结构

### 目录结构

Linux的目录结构是一个树型结构

Windows系统可以拥有多个盘符，如C盘、D盘、E盘；

Linux没有盘符这个概念，只有一个根目录/，所有文件都在它下面。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831323114-59833836-24fc-4a82-b74b-96a231bd5d01.png)

- 在Windows系统中，路径之间的层级关系，使用 \ 来表示

D:\data\work\hello.txt

注意：D:表示D盘，\表示层级关系

- 在Linux系统中，路径之间的层级关系，使用 / 来表示

/usr/local/hello.txt

注意：开头的/表示根目录，后面的/表示层级关系

### 当前工作目录

默认当前登录用户的 HOME 目录作为当前工作目录，所以 ls 命令列出的是 HOME 目录的内容。

要修改当前工作目录要用 cd 命令。

### HOME目录

HOME 目录：每个 Linux 操作用户在 Linux 系统的个人账户目录，路径在：/home/用户名

如，图中的 Linux 用户是 ashiho，其 HOME 目录是：/home/ashiho

根目录之下的home目录，包含当前系统所有普通用户

```
/（根目录）
└─ home/ （所有普通用户的总目录）
   ├─ jack/ （用户 jack 的私人 Home 目录，即 ~）
   ├─ tom/     （用户 tom 的私人 Home 目录）
   ├─ jerry/   （用户 jerry 的私人 Home 目录）
   └─ ...      （其他所有普通用户的目录）
```



- 两个例外

- - root 用户（管理员）：它的 Home 目录不是 /home/root，而是 /root（直接在根目录下），因为 root 是系统最高权限用户，不放在普通用户的 “走廊” 里；
  - 系统用户（比如 mysql、nginx）：这类为程序创建的 “虚拟用户”，通常没有 /home 下的目录（因为它们不需要登录操作，只用来运行程序）。

- 打开根目录

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831322755-67c3c746-76de-425e-a95a-caa46f4bf798.png)

## Linux命令入门

### Linux命令基础

命令：即Linux程序。一个命令就是一个Linux的程序，没有图形化页面，在终端中提供字符化的反馈。

在Linux输入：ls，本质是 /bin 文件夹里一个叫 ls 的可执行程序文件。

#### 命令格式

无论是什么命令，用于什么用途，在Linux中，命令有其通用的格式：

```
command [-options] [parameter]
```



- command：命令本身
- -options：[可选，非必填]命令的一些选项，可以通过选项控制命令的行为细节
- parameter：[可选，非必填]命令的参数，多数用于命令的指向目标等

- - 语法中的 [ ]，表示可选的意思

示例：

- ls -l /home/ashiho，ls 是命令本身，-l 是选项，/home/ashiho 是参数

- - 意思是以列表的形式，显示 /home/ashiho 目录内的内容

- cp -r test1 test2，cp 是命令本身，-r 是选项，test1 和 test2 是参数

- - 意思是复制文件夹 test1 成为 test2`

### ls命令入门

ls 命令的作用：列出目录下的内容

ls [-a -l -h] [Linux路径]

如不使用选项和参数，直接使用 ls 命令本体，表示：以平铺形式，列出当前工作目录下的内容

- -a选项，表示：all的意思，即列出全部文件（包含隐藏的文件/文件夹)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831322972-4a9dae2f-01d5-4280-99c1-3d8f1c96c8f9.png)

图中以 . 开头的，表示是 Linux 系统的隐藏文件/文件夹（只要以 . 开头，就能自动隐藏)。

只有通过 -a 选项，才能看到这些隐藏的文件/文件夹。

- -l 选项，表示：以列表（竖向排列）的形式展示内容，并展示更多信息

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831323564-e03d1e2a-f07f-466d-9d4e-d4013fd74f26.png)

- -h 选项，表示以易于阅读的形式，列出文件大小，如 K、M、G。且 -h 选项必须要搭配 -l 一起使用。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831323780-f28f3766-e8ec-4799-b731-5f85b425b8d5.png)

- 选项可以组合使用

写法：

ls -l -a

ls -la

ls -al

上述三种写法，都是一样的，表示同时应用 -l 和 -a 的功能。

## 目录切换相关命令

### cd 命令

cd 命令用来修改当前工作目录

- 语法：cd [Linux 路径]

- - cd 命令无需选项，只有参数，表示要切换到哪个目录下
  - cd 命令不写参数，表示回到用户的 HOME 目录

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831324226-3bebdbea-98b3-4e02-8828-0deebabd62d4.png)

### pwd 命令

通过 ls 来验证当前的工作目录，其实是不恰当的。

我们可以通过 pwd 命令，来查看当前所在的工作目录。

pwd 命令来自：Print Work Directory

- 语法：pwd

- - pwd 命令，无选项，无参数，直接输入 pwd 即可

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831324196-2e4077b2-4b31-4fb0-a731-84f5df0a0e7d.png)

## 相对/绝对/特殊路径符

### 相对路径

以当前目录为起点，前面没有 / 的路径，就是相对路径，它必须拼在当前目录后面才能生效。

不带 / 开头，拼在当前目录后面；带 / 开头，直接从根目录开始，不拼。

- 假设有路径 /home/ashiho/Desktop

- - 如果当前目录是 /home，可以 cd ashiho/Desktop（注意最前面不加 / ）
  - 如果当前目录是 /home/ashiho，可以 cd Desktop

即 当前路径 + 相对路径 = 绝对路径

### 绝对路径

以根目录为起点，到 目标文件或文件夹 的路径，就是绝对路径。

### 特殊路径符

**.** 表示当前目录，比如 cd ./Desktop 表示切换到当前目录下的 Desktop 目录内，和 cd Desktop 效果一致

**..** 表示上一级目录，比如：cd .. 即可切换到上一级目录，cd ../..切换到上二级的目录

**~** 表示HOME目录，比如：cd ~ 即可切换到HOME目录或 cd ~/Desktop,切换到 HOME 内的 Desktop 目录

## 创建目录命令

通过 mkdir 命令可以创建新的文件夹

mkdir 来自英文：Make Directory

语法：mkdir [-p] 路径

参数必填，表示 Linux路径，即要创建的文件夹的路径，相对路径或绝对路径均可

-p 选项可选，表示自动创建不存在的父目录，适用于创建连续多层级的目录

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831324554-a426cb27-2b09-4d8c-b5c4-de3516fed853.png)

注意：创建文件夹需要修改权限，请确保操作均在HOME目录内，不要在HOME外操作，

涉及到权限问题，HOME 外无法成功

## 文件操作命令

### touch

通过 touch 命令创建文件

语法：touch 路径

touch 命令无选项，参数必填，表示要创建的文件路径，相对、绝对、特殊路径符均可以使用。

### cat

通过 cat 命令查看文件内容

语法：cat Linux路径

cat 没有选项，只有必填参数，参数表示：被查看的文件路径，相对、绝对、特殊路径符都可以使用

### more

more命令同样可以查看文件内容，同 cat 不同的是：

- cat 是直接将内容全部显示出来
- more支持翻页，如果文件内容过多，可以一页页的展示

语法：more Linux路径

more 没有选项，只有必填参数，参数表示：被查看的文件路径，相对、绝对、特殊路径符都可以使用

在查看的过程中，通过空格翻页；通过 q 退出查看。

### cp

cp 命令可以用于复制 文件或文件夹，cp 命令来自英文单词：copy

语法：cp [-r] 参数1 参数2

-r 选项，可选，用于复制文件夹使用（即参数1是文件夹就要加 -r），表示递归。

参数1，Linux路径，表示被复制的文件或文件夹

参数2，Linux路径，表示要复制去的地方

- 注意

如果目标文件不存在，cp 会直接新建这个文件，把内容复制进去。

如果目标文件已经存在，cp 会直接覆盖它，不会有提醒。

如果目标写的是文件夹，cp 把文件或文件夹复制进文件夹里。

### mv

mv 命令可以用于移动文件或文件夹，mv 命令来自英文单词：move

语法：mv 参数1 参数2

参数1，Linux路径，表示被移动的文件或文件夹

参数2，Linux路径，表示要移动去的地方，如果目标不存在，则进行改名，确保目标存在

- 注意

如果参数2是文件夹，就是移动；如果是文件，就是重命名。

### rm

rm 命令可用于删除文件、文件夹

rm 命令来自英文单词：remove

语法：rm [-r -f] 参数1 参数2 …… 参数N

1. 同 cp 命令一样，-r 选项用于删除文件夹

2. -f 表示 force，强制删除（不会弹出提示确认信息）

3. rm 可以混合删，即同时删 文件夹和文件

普通用户删除内容不会弹出提示，只有 root 管理员用户删除内容会有提示，所以一般普通用户用不到 -f 选项。

参数1、参数2、…… 、参数N 表示要删除的文件或文件夹路径，按照空格隔开。

- rm 命令支持通配符 *****，用来做模糊匹配

符号 ***** 表示通配符，即匹配任意内容（包含空)，示例：test*****，表示匹配任何以 test 开头的内容

*****test，表示匹配任何以 test 结尾的内容

*test******，*表示匹配任何包含 test 的内容

例如：删除 test 开头的文件或文件夹

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831325425-2d0e3b4f-2179-40c5-ba37-ed1a8f8b7050.png)

- 演示 -f 选项

可以通过：su - root，并输入密码，密码和普通用户默认一样，可临时切换到 root 用户体验。

通过输入 exit 命令，退回普通用户。临时用root，用完记得退出，不要一直用。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831325318-4a80b048-f793-4f02-bd32-3f9e349855c9.png)

如下命令，请千万千万不要在 root 管理员用户下执行：

rm -rf /

rm -rf /*

效果等同于在 Windows上执行C盘格式化。

## 查找命令

### which

通过 which 命令，查看所使用的一系列 命令 的程序文件存放在哪里。

命令就是二进制可执行文件

语法：which 要查找的命令

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831325529-4cdf4d68-5b7f-403c-a0af-43d7aedb7085.png)

### find

同样，在Linux系统中，我们可以通过 find 命令去搜索指定的文件或文件夹。

- 按文件名查找

语法：find 起始路径 -name "被查找文件名"

起始路径表示从哪里开始搜索：

如是 / ，则是全盘搜索；如是 **.** 则是在当前目录搜索

被查找文件名，支持使用通配符 ***** 来做模糊查询。

例如：

test*，表示匹配任何以test开头的内容

*test，表示匹配任何以test结尾的内容

*test*，表示匹配任何包含test的内容

- 按文件大小查找

语法：find 起始路径 -size +(-)n[k M G]

+、- 表示大于和小于

n 表示大小数字

k M G 表示大小单位，k (小写字母) 表示 kb，M 表示 MB，G 表示 GB

示例：

查找小于10KB的文件：find / -size -10k

查找大于100MB的文件：find / -size +100M

查找大于1GB的文件：find / -size +1G

如果太多文件输出可以按 CTRL+C，停止。

## 管道符

### grep

通过 grep 命令，从**文件中**通过关键字过滤文件行。

语法：grep [-n] 关键字 文件路径

选项 -n，可选，表示在结果中显示匹配行的行号。

参数，关键字，必填，表示过滤的关键字，带有空格或其它特殊符号，建议使用双引号 "" 将关键字包围起来。

参数，文件路径，必填，表示要过滤内容的文件路径，可作为**内容输入端口**。

### wc

可以通过 wc 命令统计文件的行数、单词数量等

语法：wc [ -c -m -l -w ] 文件路径

- -c，统计字节数量
- -m，统计字符数量
- -l，统计行数
- -w，统计单词数量

参数：文件路径，被统计的文件，可作为**内容输入端口**。

### 管道符

将管道符左边命令的结果，作为右边命令的输入

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831325481-59a673a3-ddca-46e7-aaff-c96aadef4567.png)

- 查找 /usr/bin 中包含 “py” 的文件名

使用 find：find /usr/bin -name "*py*"

使用 grep：ls /usr/bin | grep py

- 统计文件中带有 a 关键字的有几行

cat test.txt | grep a | wc -l

## 重定向符

### echo

可以使用 echo 命令在命令行内输出指定内容

语法：echo 输出的内容

无需选项，只有一个参数，表示要输出的内容，带有空格或等特殊符号，建议使用双引号包围。

### 反引号

用反引号（通常也称之为飘号）`将命令包围，被包围的内容，会被作为命令执行，而非普通字符

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831325801-04540fb2-eaca-4e7e-80fc-2d6e6c1f2b43.png)

### 重定向符

\>，将左侧命令的结果，覆盖写入到符号右侧指定的文件中

\>>，将左侧命令的结果，追加写入到符号右侧指定的文件中

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831326800-71ad67dc-fa96-45ed-9c6d-ffab0105eb9b.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831326850-003dd89c-f917-47c6-962d-935e7ce1bbde.png)

### tail

使用 tail 命令，可以查看文件尾部内容，跟踪文件的最新更改

语法：tail [ -f -num ] 路径

参数，Linux路径，表示被跟踪的文件路径

选项，-f，表示持续跟踪

选项，-num 表示查看尾部多少行，不填默认10行

- 演示 -f

1. 创建第二个标签页

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831326576-da0ad243-78cc-4ab6-bb91-d16cdf140bf8.png)

2. 再在第二个页面给 test.txt 追加内容

第一个页面可以持续追踪最新更改

## vim编辑器

vi，vim 是 visual interface 的简称，是Linux中最经典的文本编辑器。

同图形化界面中的文本编辑器一样，ⅵ 是命令行下对文本文件进行编辑的绝佳选择。

vim 是 vi 的加强版本，兼容 vi 的所有指令，不仅能编辑文本，而且还具有 shell 程序编辑的功能，

可以不同颜色的字体来辨别语法的正确性，极大方便了程序的设计和编辑性。

### 工作模式

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831327021-873a8a57-6c36-4efa-83c7-428c6ab0cb3d.png)

- 命令模式(Command mode)

命令模式下，所敲的按键编辑器都理解为命令，以命令驱动执行不同的功能。

此模型下，不能自由进行文本编辑。

- 输入模式(Insert mode)

会显示 -- 插入 --

此模式下，可以对文件内容进行自由编辑。

- 底线命令模式(Last line mode)

会显示 :

输入 :wq，保存(w)并退出(q)

如果需要进入 vi/vim 编辑器编辑文件，输入：vim 文件路径

如果文件路径表示的文件不存在，那么此命令会用于编辑新文件

如果文件路径表示的文件存在，那么此命令用于编辑已有文件

### 命令模式快捷键

- 进入插入模式

| 模式     | 命令 | 描述                               |
| -------- | ---- | ---------------------------------- |
| 命令模式 | i    | 在当前光标位置进入输入模式         |
| 命令模式 | a    | 在当前光标位置 之后 进入输入模式   |
| 命令模式 | I    | 在当前行的开头，进入输入模式       |
| 命令模式 | A    | 在当前行的结尾，进入输入模式       |
| 命令模式 | o    | 在当前光标下一行进入输入模式       |
| 命令模式 | O    | 在当前光标上一行进入输入模式       |
| 输入模式 | esc  | 任何情况下输入 esc都能回到命令模式 |

| 模式     | 命令            | 描述                             |
| -------- | --------------- | -------------------------------- |
| 命令模式 | 键盘上、k       | 向上移动光标                     |
| 命令模式 | 键盘下、j       | 向下移动光标                     |
| 命令模式 | 键盘左、h       | 向左移动光标                     |
| 命令模式 | 键盘右、l       | 向右移动光标                     |
| 命令模式 | 0               | 移动光标到当前行的开头           |
| 命令模式 | $               | 移动光标到当前行的结尾           |
| 命令模式 | pageup (PgUp)   | 向上翻页                         |
| 命令模式 | pagedown (PgDn) | 向下翻页                         |
| 命令模式 | /               | 进入搜索模式                     |
| 命令模式 | n               | 向下继续搜索                     |
| 命令模式 | N               | 向上继续搜索                     |
| 命令模式 | dd              | 删除光标所在行的内容             |
| 命令模式 | ndd（n 为数字） | 删除当前光标向下 n 行            |
| 命令模式 | yy              | 复制当前行                       |
| 命令模式 | nyy（n 为数字） | 复制当前行和下面的 n 行          |
| 命令模式 | p               | 粘贴复制的内容                   |
| 命令模式 | u               | 撤销修改                         |
| 命令模式 | ctrl + r        | 反向撤销修改（重做）             |
| 命令模式 | gg              | 跳到文件首行                     |
| 命令模式 | G               | 跳到文件尾行                     |
| 命令模式 | dG              | 从当前行开始，向下全部删除       |
| 命令模式 | dgg             | 从当前行开始，向上全部删除       |
| 命令模式 | d$              | 从当前光标开始，删除到本行的结尾 |
| 命令模式 | d0              | 从当前光标开始，删除到本行的开头 |

- 底线命令模式

| 模式         | 命令       | 描述         |
| ------------ | ---------- | ------------ |
| 底线命令模式 | :wq        | 保存并退出   |
| 底线命令模式 | :q         | 仅退出       |
| 底线命令模式 | :q!        | 强制退出     |
| 底线命令模式 | :w         | 仅保存       |
| 底线命令模式 | :set nu    | 显示行号     |
| 底线命令模式 | :set paste | 设置粘贴模式 |

# 权限管控

## root

### 认识root用户

root 用户拥有最大的系统操作权限，而普通用户在许多地方的权限是受限的。

普通用户的权限，一般在其HOME目录内是不受限的，

一旦出了HOME目录，大多数地方，普通用户仅有只读和执行权限，无修改权限。

### 用户切换命令

在前面，我们接触过 su 命令切换到 root 账户。

su 命令就是用于账户切换的系统命令，其来源英文单词：Switch User

语法：su [-] [用户名]

**-** 符号是可选的，表示是否在切换用户后加载环境变量，建议带上

参数：用户名，表示要切换的用户，用户名也可以省略，默认表示切换到 root

切换用户后，可以通过 exit 命令退回上一个用户，也可以使用快捷键：ctrl+d

使用普通用户，切换到其它用户需要输入密码，如切换到 root 用户

使用 root 用户切换到其它用户，无需密码，可以直接切换

### sudo

在我们得知 root 密码的时候，可以通过 su 命令切换到 root 得到最大权限。

但是我们不建议长期使用 root 用户，避免带来系统损坏。

我们可以使用 sudo 命令，为普通的命令授权，临时以 root 身份执行。

语法：sudo 其它命令

在其它命令之前，带上 sudo，即可为这一条命令临时赋予 root 授权

但是并不是所有的用户，都有权利使用 sudo，我们需要为普通用户配置 sudo 认证

- 配置 sudo 认证

切换到 root 用户，执行 visudo 命令，会自动通过 vi 编辑器打开：/etc/sudoers

在文件的最后添加：ashiho ALL=(ALL)	NOPASSWD:ALL

其中最后的 NOPASSWD:ALL 表示使用 sudo 命令，无需输入密码。

最后通过wq保存。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831327719-80faeccd-37aa-4611-a3b8-3d9a6289acaf.png)

## 用户、用户组管理

Linux 系统中可以：配置多个用户；配置多个用户组；用户可以加入多个用户组中。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831327871-0cf72391-d17a-4d62-b213-a85ec45cfaf5.png)

Linux 中关于权限的管控级别有 2 个级别，分别是:

- 针对用户的权限控制
- 针对用户组的权限控制

比如，针对某文件，可以控制用户的权限，也可以控制用户组的权限。

- 创建用户组

groupadd 用户组名

- 删除用户组

groupdel 用户组名

- 创建用户

useradd 用户名 [-g -d] [用户组]

选项：-g 指定用户的组，不指定 -g 会创建同名组并自动加入，指定 - g 需要组已经存在，如已存在同名组，必须使用 - g

选项：-d 指定用户 HOME 路径，不指定，HOME 目录默认在：/home/ 用户名

- 删除用户

userdel [-r] 用户名

选项：-r，删除用户的 HOME 目录；不使用 - r，删除用户时，HOME 目录保留

- 查看用户所属组

id [用户名]

参数：用户名，被查看的用户，如果不提供则查看自身

- 修改用户所属组

usermod -aG 用户组 用户名

将指定用户加入指定用户组

- 使用 getent 命令，可以查看当前系统中有哪些**用户**

语法：getent passwd

共有7份信息，分别是：

用户名:密码(X):用户ID:组ID:描述信息（无用）:HOME目录:执行终端（默认bash)

ashiho:x:1000:1000:ashiho:/home/ashiho:/bin/bash

- 使用 getent 命令，同样可以查看当前系统中有哪些**用户组**

语法：getent group

包含 3 份信息，分别是：

组名称:组认证（显示为X):组ID

ashiho:x:1000:

## 查看权限控制

### 权限信息

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831328143-779d024a-0137-4e88-be1a-4925276fd66e.png)

序号1，表示文件、文件夹的权限控制信息

序号2，表示文件、文件夹所属用户

序号3，表示文件、文件夹所属用户组

#### 权限细节

解析一下序号1：权限细节

权限细节总共分为10个槽位

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831328536-7e887ce7-c3d7-42da-9838-015506e8c357.png)

- r 表示读权限

- - 针对文件可以查看文件内容
  - 针对文件夹，可以查看文件夹内容，如 ls 命令

- w 表示写权限

- - 针对文件表示可以修改此文件
  - 针对文件夹，可以在文件夹内：创建、删除、改名等操作

- x 表示执行权限

- - 针对文件表示可以将文件作为程序执行
  - 针对文件夹，表示可以更改工作目录到此文件夹，即 cd 进入

### chmod

使用 chmod 命令，修改文件、文件夹的**权限信息**。

注意，只有文件、文件夹的所属用户或 root 用户可以修改。

语法：chmod [-R] 权限 文件或文件夹

选项：-R，对文件夹内的全部内容应用同样的操作

示例：

chmod u=rwx,g=rx,o=x hello.txt，将文件权限修改为：rwxr-x--x

其中：u 表示 user 所属用户权限，g 表示 group 组权限，o 表示 other 其它用户权限

chmod-R u=rwx,g=rx,o=x test，将文件夹 test 以及文件夹内全部内容权限设置为：rwxr-x--x

#### 权限数字序号

权限可以用 3 位数字来代表，第一位数字表示用户权限，第二位表示用户组权限，第三位表示其它用户权限。

数字的细节如下：r 记为 4，w 记为 2，x 记为 1

0：无任何权限，即 ---

1：仅有 x 权限	即 --x

2：仅有 W 权限	即 - w-

3：有 w 和 x 权限	即 - wx

4：仅有 r 权限		即 r--

5：有 r 和 x 权限	即 r-x

6：有 r 和 w 权限	即 rw-

7：有全部权限	即 rwx

```
chmod 751 test.txt
rwxr-x--x
```





### chown

使用 chown 命令，可以修改文件、文件夹的所属用户和用户组。

普通用户无法修改所属为其它用户或组，所以此命令只适用于 root 用户执行。

- 语法：chown [-R] [用户][:][用户组] 文件或文件夹

选项，-R, 同 chmod, 对文件夹内全部内容应用相同规则

选项，用户，修改所属用户

选项，用户组，修改所属用户组

**:** 用于分隔用户和用户组

示例：

chown root hello.txt，将 hello.txt 所属用户修改为 root

chown :root hello.txt，将 hello.txt 所属用户组修改为 root

chown root:ashiho hello.txt，将 hello.txt 所属用户修改为 root，用户组修改为 ashiho

chown -R root test，将文件夹test的所属用户修改为root并对文件夹内全部内容应用同样规则

# 使用操作

## 快捷键

### 强制停止

- Linux 某些程序的运行，如果想要强制停止它，可以使用快捷键 CTRL + C

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831328388-bb148bd9-680e-4dbc-935f-53f086b92a6c.png)

- 命令输入错误，也可以通过 CTRL + C，退出当前输入，重新输入

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831328757-1e6761eb-a5f9-404c-8242-5214e2aa5b7f.png)

### 退出、登出

- 通过快捷键 CTRL + C，退出账户的登录

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831329483-4b512bb3-c072-4aca-8fe2-493103ef62b4.png)

- 或者退出某些特定程序的专属页面

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831329742-9a04c2c6-ddf7-4a42-ad1a-77965b842d05.png)

### 历史命令搜索

- 按 ⬆ 或 ⬇ 查看历史命令
- 可以通过 history 命令，查看历史输入过的命令

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831329950-e778c402-478b-4611-8983-1ae0a2420719.png)

- 可以通过：**!**命令前缀，自动执行上一次匹配该前缀的命令

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831330256-393e5c07-56aa-4a90-b942-1835bc134c1c.png)

- 可以通过快捷键：CTRL + r，输入内容去匹配历史命令

如果搜索到的内容是你需要的，那么：

回车键可以直接执行

键盘左右键，可以得到此命令（不执行)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831330194-ec411550-6919-4696-834d-43c11bc40c94.png)

### 光标移动

CTRL + a，跳到命令开头

CTRL + e，跳到命令结尾

CTRL + 键盘左键，向左跳一个单词

CTRL + 键盘右键，向右跳一个单词

### 清屏

通过快捷键 ctrl + l，可以清空终端内容

或通过命令 clear 得到同样效果

## 软件安装

### yum

使用 yum 为 CentOS 系统安装软件

yum：RPM 包软件管理器，用于自动化安装配置 Linux 软件，并可以自动解决依赖问题。

- 语法：yum [-y] [ install | remove | search ] 软件名

- - 选项：-y，自动确认，无需手动确认安装或卸载过程
  - install：安装
  - remove：卸载
  - search：搜索

- 条件

- - yum 命令需要 root 权限哦，可以 su 切换到 root，或使用 sudo 提权。
  - yum 命令需要联网

### apt

使用 apt 为 Ubuntu 安装软件

语法:

apt [-y] [ install | remove | search ] 软件名

用法和 yum 一致，同样需要 root 权限

apt install wget，安装 wget

apt remove wget，移除 wget

apt search wget，搜索 wget

- 转成root

输入sudo su - root

## systemctl

Linux 系统很多软件 (内置或第三方) 均支持使用 systemctl 命令控制：启动、停止、开机自启

能够被 systemctl 管理的软件，一般也称之为：服务

- 语法：systemctl start | stop | status | enable | disable 服务名

- - start：启动
  - stop：关闭
  - status：查看状态
  - enable：开启开机自启
  - disable：关闭开机自启

系统内置的服务比较多,比如:

NetworkManager：主网络服务

network：副网络服务

firewalld：防火墙服务

sshd，ssh服务（FinalShell远程登录 Linux 使用的就是这个服务）

部分软件安装后没有自动集成到 systemctl 中，我们可以手动添加。

## 软连接

在系统中创建软链接，可以将文件、文件夹链接到其它位置。

类似 Windows 系统中的快捷方式

- 语法：ln -s 参数1 参数2

- - -s选项，创建软连接
  - 参数1：被链接的文件或文件夹
  - 参数2：要链接去的目的地

实例：（ ~ 代表home目录）

ln -s /etc/yum.conf ~/yum.conf

ln -s /etc/yum ~/yum

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831330696-77ccf058-fce6-47dd-b8a9-c80499c2c332.png)

## 日期、时区

### date

通过 date 命令可以在命令行中查看系统的时间

语法：date [-d] [ +格式化字符串 ]

-d 按照给定的字符串显示日期，一般用于日期计算

格式化字符串：通过特定的字符串标记，来控制显示的日期格式

%Y 	年（四位数）

%y 	年份后两位数字

%m 	月份

%d 	日

%H 	小时

%M	分钟

%S 	秒

%s 	自 1970-01-01 00:00 UTC 到现在的秒数

- 格式化时间

如果中间带有空格，所以要使用 **双引号** 包围格式化字符串作为整体

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831331060-d9072418-34b9-401e-bcff-5aa5c3da6ee7.png)

- -d选项，可以按照给定的字符串显示日期，一般用于日期计算

-d 选项可以和格式化字符串配合一起使用

其中支持的时间标记为：

year	年

month 月

day 天

hour 小时

minute 分钟

second 秒

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831331179-dd41fbd8-31e5-4690-8136-e6e0c5ebe174.png)

### 修改时区

使用 root 权限，执行如下命令，修改时区为东八区时区

```bash
rm -f /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```



将系统自带的 localtime 文件删除，并将 /usr/share/zoneinfo/Asia/Shanghai 文件链接为 localtime 文件即可

### ntp

我们可以通过 ntp 程序自动校准系统时间

安装ntp：yum -y install ntp

- 启动并设置开机自启

```shell
systemctl start ntpd

systemctl enable ntpd
```



当 ntpd 启动后会定期的帮助我们联网校准系统的时间

- 也可以手动校准（需root权限）：ntpdate -u ntp.aliyun.com

通过阿里云提供的服务网址配合 ntpdate （安装 ntp 后会附带这个命令）命令自动校准

## IP和主机名

### IP地址

每一台联网的电脑都会有一个地址，用于和其它计算机进行通讯

IP地址主要有2个版本，V4版本和V6版本（V6很少用）

IPV4 版本的地址格式是 a.b.c.d，其中 abcd 表示0~255的数字，如 192.168.88.101 就是一个标准的IP地址

可以通过命令：ifconfig，查看本机的 ip 地址；如无法使用 ifconfig 命令，可以安装：yum -y install net-tools

- 特殊IP地址

- - 127.0.0.1

- - - 这个IP地址用于指代本机

- - 0.0.0.0

- - - 可以用于指代本机，可以在端口绑定中用来确定绑定关系。
    - 在一些IP地址限制中，表示所有IP的意思，如放行规则设置为 0.0.0.0，表示允许任意IP访问。

### 主机名

每一台电脑除了对外联络地址(IP地址)以外，也可以有一个名字，称之为主机名

无论是 Windows 或 Linux 系统，都可以给系统设置主机名

- Windows 系统主机名

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831331348-7ebdca6c-1bd0-4b05-8054-53a07ddd55a7.png)

- Linux 系统主机名：使用 hostname 查看主机名

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831331690-923dfbee-6786-4c07-9ffe-e939acd920fc.png)

使用命令来修改主机名（需root）：hostnamectl set-hostname 主机名

再重新登录 FinalShell 即可看到主机名已经正确显示。

### 域名解析

IP地址实在是难以记忆，有没有什么办法可以通过主机名或替代的字符地址去代替数字化的IP地址呢?

实际上，我们一直都是通过字符化的地址去访问服务器，很少指定IP地址

比如，我们在浏览器内打开：www.baidu.com，会打开百度的网址

其中 www.baidu.com 是百度的网址，我们称之为：域名

- 访问 www.baidu.com 的流程如下

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831332116-d4aaedfd-319f-4586-9afa-42f0b8a831fd.png)

#### 配置主机名和 IP 的映射关系

1. 在 Windows 中，用管理员身份打开记事本

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831332285-304deddb-8325-4b09-bcb3-84efdde38b37.png)

2. 打开文件

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831332341-dec7f680-0286-4b62-8476-4cd93d8983c5.png)

3. 找 C:\Windows\System32\drivers\etc，并选择所有文件，不然不会显示

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831332783-8488ac93-278b-4d50-9d72-587bf10ee2ee.png)

4. 选择打开 hosts 文件，在把IP和主机名写上并保存，就行了

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831333221-e744b7c7-d06a-485b-8005-83a95acf2cca.png)

5. 把 FinalShell 的主机，从 IP 地址改成主机名

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831333407-38ef8ea7-ca11-4eb8-abd5-b53b0acabc5d.png)

#### 配置Linux固定IP地址

当前我们虚拟机的 Linux 操作系统，其IP地址是通过 DHCP 服务获取的。

DHCP：动态获取IP地址,即每次重启设备后都会获取一次,可能导致IP地址频繁变更

- 原因1：办公电脑IP地址变化无所谓，但是我们要远程连接到Linux系统，如果IP地址经常变化我们就要频繁修改适配很麻烦
- 原因2：在刚刚我们配置了虚拟机IP地址和主机名的映射，如果IP频繁更改，我们也需要频繁更新映射关系

配置固定IP：

- 在VMwareWorkstation 中配置 IP 地址网关和网段（IP地址的范围）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831333493-8cd862c8-46c4-4066-ab99-b5d97f3d84aa.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831333386-da2c4ba9-74e2-4768-a351-0fb331b3b050.png)

如子网IP和子网掩码显示为灰色，无法修改，就点击窗口左下角带盾牌的“更改设置”

修改子网IP为如图所示

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831334421-7747a7e0-6cc4-47fa-a741-57de31e92a0e.png)

点击 NAT 设置

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831334488-470a2ea8-b1d1-4f11-83cf-997ac366bd55.png)

修改网关IP为如图所示

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831334826-0a779ef6-8051-40ef-853b-665e52f8941f.png)

- 在Linux系统中手动修改配置文件，固定IP

先切换到 root 用户；

使用vim编辑：vim /etc/sysconfig/network-scripts/ifcfg-ens33 文件，填入如下内容

输入：:set nu 查看行数

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831334861-e850da39-2887-4ab0-baf2-c2fb95d59b00.png)

把第四行的dhcp改成static，再在最后添加：

IPADDR="192.168.88.130"

NETMASK="255.255.255.0"

GATEWAY="192.168.88.2"

DNS1="192.168.88.2"

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831335154-da1d8beb-9725-4699-b3cd-5e66c0ae9948.png)

最后执行 systemctl restart network 重启网卡，执行 ifconfig 即可看到 ip 地址固定为 192.168.88.130 了

## 网络传输

### 下载和网络请求

#### ping命令

可以通过 ping 命令，检查指定的网络服务器是否是可联通状态

- 语法：ping [-c num] ip或主机名

- - 选项：-c，检查的次数，不使用-c选项，将无限次数持续检查
  - 参数：ip或主机名，被检查的服务器的ip地址或主机名地址

#### wget命令

wget 是非交互式的文件下载器，可以在命令行内下载网络文件

语法：wget [-b] url

选项：-b，可选，后台下载，会将日志写入到当前工作目录的 wget-log 文件

参数：url，下载链接

注意：无论下载是否完成，都会生成要下载的文件，如果下载未完成（显示红色），请及时清理未完成的不可用文件。

如果是后台下载想查看下载进度，输入：tail [ -f ] wget-log

-f 表示持续跟踪

#### curl命令

curl 可以发送 http 网络请求，可用于：下载文件、获取信息等

语法：curl [-o] url

选项：-o，用于下载文件，当url是下载链接时，可以使用此选项保存文件

参数：url，要发起请求的网络地址

示例：

- 向 cip.cc 发送请求

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831336124-365b175e-6840-4d89-a2bb-7f556c1d3e3e.png)

- 向百度发送请求

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831336557-f7b5d334-1242-42c7-8331-39b5d3538c7f.png)

### 端口

端口，是设备与外界通讯交流的出入口。

- 物理端口：又可称之为接口，是可见的端口，如USB接口，RJ445网口，HDMI端口等
- 虚拟端口：是指计算机内部的端口，是不可见的，是用来操作系统流和外部进行交互使用的

- - - IP 可以确定计算机，端口可以确定程序

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831336489-e67960b3-d1d7-4223-9a5e-44c7124c4713.png)

Linux 可以支持 65535 个端口，这6万多个端口分为 3 类进行使用：

- 公认端口：1~1023，通常用于一些系统内置或知名程序的预留使用，如 SSH 服务的 22 端口，HTTPS 服务的 443 端口

- - - 非特殊需要，不要占用这个范围的端口

- 注册端口：1024~49151，通常可以随意使用，用于松散的绑定一些程序 / 服务
- 动态端口：49152~65535，通常不会固定绑定程序，而是当程序对外进行网络链接时，用于临时使用。

如图中，计算机A的微信连接计算机B的微信，A使用的50001即动态端口，临时找一个端口作为出口

计算机B的微信使用端口5678，即注册端口，长期绑定此端口等待别人连接

#### 查看端口占用

- 使用 nmap 命令去查看端口的占用情况

先安装nmap：yum -y install nmap

语法：nmap 被查看的IP地址

查看本机：127.0.0.1，被四个端口占用

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831336908-51abd5a4-ae03-4e50-8ed4-7afa55e0fa38.png)

- 可以通过netstat命令，查看**指定端口**的占用情况

安装netstat：yum -y install net-tools

语法：netstat -anp | grep 端口号

## 进程管理

程序运行在操作系统中，是被操作系统所管理的。

为管理运行的程序，每一个程序在运行的时候，便被操作系统流注册为系统中的一个：进程

并会为每一个进程都分配一个独有的：进程ID(进程号)

### 查看进程

可以通过ps命令查看Linux系统中的进程信息

语法：ps [ -e -f ]

选项：-e，显示出全部的进程

选项：-f，以完全格式化的形式展示信息(展示全部信息)

一般来说，固定用法就是：ps -ef 列出全部进程的全部信息

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831337493-85c5fafb-678a-4845-adb0-a3faca56734d.png)

从左到右分别是：

UID：进程所属的用户ID

PID：进程的进程号ID

PPID：进程的父ID(启动此进程的其它进程)

C：此进程的CPU占用率(百分比)

STIME：进程的启动时间

TTY：启动此进程的终端序号，如显示 **?**，表示非终端启动

TIME：进程占用CPU的时间

CMD：进程对应的名称或启动路径或启动命令

- 查看指定线程

列出信息太多，无法精确找到所指定进程，可以使用管道符配合 **grep** 来进行过滤，如:

ps -ef | grep tail，即可准确的找到 tail 命令的信息

### 关闭线程

- 在Windows系统中，可以通过任务管理器选择进程后，点击结束进程从而关闭它

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831337591-e6b76520-c05a-4a1f-9b6f-0391ae4c1c2c.png)

- 同样，在Linux中，可以通过 kill 命令关闭进程

语法：kill [ -9 ] 进程ID

选项：-9，表示强制关闭进程。不使用此选项会向进程发送信号要求其关闭，但是否关闭看进程自身的处理机制。

## 主机状态

### 查看系统资源占用

通过 top 命令查看 CPU、内存使用情况，类似Windows的任务管理器

语法：直接输入 top 即可，默认每 5 秒刷新一次，按 q 或 CTRL+C 退出

- 第一行

top：命令名称；14:39:58：当前系统时间；up 3:21：启动了3小时21分钟，2 users：2个用户登录，load：1、5、15分钟负载

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831337886-a1dc82f0-6cda-44fb-8b5a-5a922b267f3e.png)

- 第二行

Tasks：171个进程；2 running：2个进程在运行；169 sleeping：169个进程睡眠；0个停止进程；0个僵尸进程

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831338000-9faf74b7-ed54-485e-a10d-a62f6e625c47.png)

- 第三行

%Cpu(s)：CPU使用率；us：用户CPU使用率；sy：系统CPU使用率；ni：高优先级进程占用CPU时间百分比；id：空闲CPU率

wa：IO等待CPU占用率；hi：CPU硬件中断率；si：CPU软件中断率；st：强制等待占用CPU率

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831338412-cb8e386a-0619-495b-a1a7-3fbcfb0acd75.png)

- 第四、五行

Kib Mem：物理内存；total：总量；free：空闲；used：使用；buff/cache：buff 和 cache 占用

KibSwap：虚拟内存(交换空间)；total：总量；free：空闲；used：使用；buff/cache：buff 和 cache 占用

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831339195-4e779a4b-d8b2-47d2-8e6d-3fa90912f16e.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831339566-8791c451-f1c2-42c6-a434-1241fb55315c.png)

PID：进程id

USER：进程所属用户

PR：进程优先级，越小越高

NI：负值表示高优先级，正表示低优先级

VIRT：进程使用虚拟内存，单位KB

RES：进程使用物理内存，单位KB

SHR：进程使用共享内存，单位KB

S：进程状态（S休眠，R运行，Z僵死状态，N负数优先级，I空闲状态）

%CPU：进程占用CPU率

%MEM：进程占用内存率

TIME+：进程使用CPU时间总计，单位10毫秒

COMMAND：进程的命令或名称或程序文件路径

#### top命令选项

| 选项 | 功能说明                                                     | 示例 / 补充说明                               |
| ---- | ------------------------------------------------------------ | --------------------------------------------- |
| -p   | 只显示某个进程的信息                                         | top -p 1234（查看 PID 为 1234 的进程）        |
| -d   | 设置刷新时间，默认刷新间隔为 5 秒                            | top -d 2（每 2 秒刷新一次）                   |
| -c   | 显示产生进程的完整命令（默认仅显示进程名）                   | top -c（直观看到进程的完整启动指令）          |
| -n   | 指定刷新次数，达到次数后自动退出 top 命令                    | top -n 3（刷新 3 次后退出）                   |
| -b   | 以非交互 / 非全屏模式运行，适合批量输出或重定向到文件，常配合 -n 使用 | top -b -n 3 > /tmp/top.log（输出 3 次到文件） |
| -i   | 不显示闲置（idle）或僵尸（zombie）进程，仅展示活跃进程       | top -i（简化输出，聚焦核心进程）              |
| -u   | 筛选并显示指定用户启动的所有进程                             | top -u root（查看 root 用户的进程）           |

#### top交互式选项

| 按键 | 功能说明                                                     | 补充说明                                |
| ---- | ------------------------------------------------------------ | --------------------------------------- |
| h    | 显示帮助画面                                                 | 快速查看所有交互按键的使用说明，按q退出 |
| c    | 显示产生进程的完整命令；再次按下恢复默认显示                 | 等同于命令行 -c 参数                    |
| f    | 进入项目选择界面，自定义需要展示的进程监控项                 | 按空格选中或不选                        |
| M    | 按进程驻留内存大小（RES）从高到低排序                        | 大写 M，快速定位内存占用高的进程        |
| P    | 按 CPU 使用百分比从高到低排序                                | 大写 P，默认排序方式                    |
| T    | 按进程运行时间 / 累计占用 CPU 时间排序                       | 大写 T，定位运行最久的进程              |
| E    | 切换顶部系统内存总量的显示单位（KB/MB/GB 等）                | 大写 E，调整整体内存显示粒度            |
| e    | 切换单个进程内存的显示单位（KB/MB/GB 等）                    | 小写 e，精准查看进程内存占用            |
| 1    | 切换 CPU 核心的显示方式（汇总 / 分核心显示平均负载、启动时间等） | 数字 1，多核服务器常用                  |
| i    | 隐藏闲置 / 无用进程；再次按下恢复默认显示                    | 等同于命令行 -i 参数，简化输出          |
| t    | 切换 CPU 状态信息的显示样式（文字 / 图形化）                 | 小写 t，直观查看 CPU 使用率占比         |
| m    | 切换内存 / 交换分区信息的显示样式（文字 / 图形化）           | 小写 m，直观查看内存使用率占比          |

### 查看磁盘

- 使用df命令，可以查看硬盘的使用情况

语法：df [-h]

选项：-h，以更加人性化的单位显示

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831339847-0a6450b0-edd6-49a7-9225-9a3c02c970c7.png)

- 可以使用 iostat 查看CPU、磁盘的相关信息

语法：iostat [-x] [num1] [num2]

选项：-x，显示更多信息

num1：数字，几秒刷新一次；num2：数字，刷新次数

rrqm/s：每秒这个设备相关的读取请求有多少被Merge了（当系统调用需要读取数据的时候，VFS将请求发到各个FS，

如果FS发现不同的读取请求读取的是相同Block的数据，FS会将这个请求合并Merge，提高IO利用率，避免重复调用）

wrqm/s：每秒这个设备相关的写入请求有多少被Merge了。

rsec/s：每秒读取的扇区数；sectors

wsec/：每秒写入的扇区数。

rKB/s：每秒发送到设备的读取请求数

wKB/s：每秒发送到设备的写入请求数

avgrq-sz：平均请求扇区的大小

avgqu-sz：平均请求队列的长度。毫无疑问，队列长度越短越好。

await：每一个IO请求的处理的平均时间（单位是微秒毫秒）。

svctm：表示平均每次设备I/O操作的服务时间（以毫秒为单位）

%util：磁盘利用率

### 查看网络状态

可以使用 sar 命令查看网络的相关统计（sar命令非常复杂，这里仅简单用于统计网络）

语法：sar -n DEV num1 num2

选项：-n，查看网络，DEV表示查看网络接口

num1：刷新间隔（不填就查看一次结束）；num2：查看次数（不填无限次数）

信息解读：

IFACE	本地网卡接口的名称

rxpck/s	每秒钟接受的数据包

txpck/s	每秒钟发送的数据包

rxKB/S	每秒钟接受的数据包大小，单位为KB

txKB/S	每秒钟发送的数据包大小，单位为KB

rxcmp/s	每秒钟接受的压缩数据包

txcmp/s	每秒钟发送的压缩包

rxmcst/s	每秒钟接收的多播数据包

## 环境变量

### 环境变量作用

在讲解 which 命令的时候，我们知道使用的一系列命令其实本质上就是一个个的可执行程序

比如，cd 命令的本体就是：/usr/bin/cd 这个程序文件。

无论当前工作目录在哪，直接输入 cd 就能执行程序，而不用完整的输入 /usr/bin/cd，就是借助环境变量的：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831339992-2b20ee22-a9da-40a2-a590-77cef6d71152.png)

环境变量是操作系统在运行的时候，记录的一些关键性信息，用以辅助系统运行。

在 Linux 系统中执行：env 命令即可查看当前系统中记录的的环境变量

环境变量是一种 Key=Value 型结构，即键值对。

### 符号$

在Linux系统中，$ 符号被用于取"变量"的值，即根据 键(变量名) 来取得它所记录的值。

环境变量记录的信息，除了给操作系统自己使用外，如果我们想要取用，也可以使用。

取得环境变量的值就可以通过语法：$环境变量名

比如：echo $PATH

就可以取得PATH这个环境变量的值，并通过echo语句输出出来。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831340035-3c120cf3-bf97-4f4e-81ec-6a354ecd6bad.png)

想要这个值和其它内容拼接在一起输出，可使用 { } 标注变量是谁

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831341289-09bcc87d-4844-4754-b950-59692eac7bcf.png)

### 在Linux配置环境变量

Linux 环境变量可以用户自行设置，其中分为：

- 临时设置

语法：export 变量名=变量值

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831341288-79459f6a-caa8-4fdf-baee-0f72dc0b0fbe.png)

- 永久生效

针对**当前**用户生效，配置在当前用户的：~/**.**bashrc（注意这有个点 **.** ）

针对**所有**用户生效，配置在系统的：/etc/profile

并通过语法：source 配置文件，进行立刻生效，或重新登录Finalshell生效

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831341799-168fa515-219a-472d-8036-858a5b827df3.png)

重启 FinalShell 也能生效，即永久生效

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831342240-45be97f3-ecd3-4090-9ea5-7346a533d7af.png)

### 自定义PATH

- 创建文件夹 myenv，新建文件 hello，修改文件的执行权限

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831342258-aed56df0-53f3-4b77-b2c0-c3b077223dab.png)

- 文件里写要执行的命令

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831343114-b1ec955d-1545-4ee3-95a3-3f71feaa288a.png)

- 配置所有用户永久生效

值是原来的PATH，再加上刚创建的文件夹的路径

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831343890-9b5b83cd-9d9f-4825-a86f-d8241df8eb59.png)

## 上传和下载

### 下载

我们可以通过 FinalShell 工具，方便的和虚拟机进行数据交换。

在 FinalShell 软件的下方窗体中，提供了Linux的文件系统视图，可以方便的：

- 浏览文件系统，找到合适的文件，右键点击下载，即可传输到本地电脑。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831343146-92995d6a-2b11-4f37-bb60-9ad736805dac.png)

- 注意

普通用户无法看到 root 文件夹

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831343823-3c5119c5-5f0c-4d74-8ed4-6a655f838726.png)

转换成 root 用户

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831344010-b09ef939-5ec8-4325-a21a-a8833ae6434a.png)

就可以看到了

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794831345121-cfc54fb8-f89a-49ac-946f-1e7d9a2fe241.png)

### 上传

- 浏览文件系统，点击合适的目录，将本地电脑的文件拖进去，即可方便的上传数据到 Linux 中。

### rz、sz命令

除了通过 FinalShell 的下方窗体进行文件的传输以外，也可以通过rz、sz命令进行文件传输。

rz、sz命令需要安装，可以通过：yum -y install lrzsz

- rz命令：进行上传

语法：rz

会弹出窗口让你选择要上传的文件

- sz命令：进行下载

语法：sz 要下载的文件

## 压缩、解压

市面上有非常多的压缩格式

zip格式：Linux、Windows、MacOS，常用

7zip：Windows系统常用

rar：Windows系统常用

tar：Linux、MacOS常用

gzip：Linux、MacOS常用

在 Windows 系统中常用的软件如：winrar、bandizip 等软件，都支持各类常见的压缩格式

### tar、gzip

Linux 和 Mac 系统常用有 2 种压缩格式，后缀名分别是：

- .tar，称之为 tarball，归档文件，即简单的将文件组装到一个 tar 的文件内，并没有太多文件体积的减少，仅仅是简单的封装
- .gz，也常见为 .tar.gz，gzip 格式压缩文件，使用 gzip 压缩算法将文件压缩到一个文件内，可以极大的减少压缩后的体积

针对这两种格式，使用 tar 命令均可以进行压缩和解压的操作

语法：

tar [ -c -v -x -f -z -C ] 参数1 参数2 ... 参数N

-c，**创建**压缩文件，用于压缩模式

-v，显示压缩、解压过程，用于查看进度

-x，解压模式

-f，**指定**要创建的文件，或要解压的文件，-f 选项必须在所有选项中位置处于最后一个

因为 -f 后面必须紧跟 要生成的压缩包名字

-z，gzip 模式，不使用 -z 就是普通的 tarball 格式，在所有选项中位置处于第一个

-C，选择解压的目的地，用于解压模式，该选项单独使用，与其它选项分开

#### 常用压缩组合

- tar -cyf test.tar 1.txt 2.txt 3.txt

将 1.txt 2.txt 3.txt 压缩到 test.tar 文件内，体积不变

- tar -zcvf test.tar.gz 1.txt 2.txt 3.txt

将 1.txt 2.txt 3.txt 压缩到 test.tar.gz 文件内，使用 gzip 模式，体积减少

- 注意

小文件压缩后体积反而会加大，因为压缩的文件本身太小了，而压缩包有固定的头部信息（表头），

表头体积比原文件还大，所以整体反而变大。

#### 常用解压组合

- tar -xvf test.tar

解压 test.tar，将文件解压至当前目录

- tar -xvf test.tar -C /home/ashiho

解压 test.tar，将文件解压至指定目录 /home/ashiho

- tar -zxvf test.tar.gz -C /home/ashiho

以 gzip 模式解压 test.tar.gz，将文件解压至指定目录 /home/ashiho

### zip

可以使用 zip 命令，**压缩**文件为 zip 压缩包

语法：zip [-r] 参数1 参数2 ... 参数N

-r，被压缩的包含文件夹的时候，需要使用 -r 选项，和 rm、cp 等命命令的 -r 效果一致

示例：

- zip test.zip a.txt b.txt c.txt

将 a.txt b.txt c.txt 压缩到 test.zip 文件内

- zip -r test.zip test a.txt b.txt

将 test 文件夹和 a.txt b.txt 文件，压缩到 test.zip 文件内

### unzip

使用 unzip 命令，解压 zip 压缩包

语法：

unzip 参数 [-d]

-d 指定要解压去的位置，同 tar 的 -C 选项

参数，被解压的 zip 压缩包文件

示例：

unzip test.zip 将 test.zip 解压到当前目录

unzip test.zip -d /home/ashiho 将 test.zip 解压到指定文件夹内 /home/ashiho

### 区别

| 格式       | 特点                                             | 适合场景                 |
| ---------- | ------------------------------------------------ | ------------------------ |
| **tar.gz** | 能完整保留文件权限、所有者，压缩率更高、体积更小 | 服务器打包、备份、自己用 |
| **zip**    | **全平台通用**，所有系统都兼容                   | 发给别人、跨系统传输     |

- 总结：Linux 上优先用 tar，跨平台用 zip