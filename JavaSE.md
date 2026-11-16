## **小知识**

### **cmd操作**

cd = Change Directory → 切换文件夹

如果想打开文件或程序，直接输入名字，不需要加 cd。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655482139-3468e464-2676-4cc9-8a91-e1248a116956.png)

### **环境变量**

#### **打开步骤**

1. 打开此电脑，右键，点击属性

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655481145-25bcffed-346e-4ebd-b05c-9809ce5ab300.png)



2. 点击高级系统设置

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655480953-e0ae22a6-648c-4363-859d-d03ec6e839f5.png)



3. 点击环境变量

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655480687-5d5ef2d1-a91a-4b77-8a21-bc2b41383896.png)



4. 选择用户变量并不通用，所以选择系统变量的 Path

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655481590-570961c5-9eb3-4e61-803d-2add5f7da4a0.png)



#### **作用**

把这些路径放进环境变量 Path，系统就会记住这些位置。

以后你在命令行里输任何命令 / 软件名，系统会自动去这些文件夹里找

没环境变量时，必须写完整路径才能运行：D:\QQ\Bin\qq.exe 

加了环境变量后，这样就能运行：qq.exe

- 常见用处

Java、Python、Git、Node、Maven……

所有开发工具必须配环境变量，否则用不了。

安装的 jdk21 也要配到环境变量中

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655482318-2502fa0e-b82b-4602-9a07-7aa645b21d4f.png)

### **JDK**

JDK (Java Development kit)，Java开发工具包，包含：

JVM虚拟机：Java程序运行的地方

核心类库：Java已经写好的东西，我们可以直接用。

开发工具：javac(编译)、java(运行)、jdb(调试)、jhat(内存分析)…

JDK 包含 JRE，JRE 包含 JVM

jdk 的 bin 文件夹

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655482669-eddec2fb-c54a-4ce2-9583-97a3733dbe7a.png)

javac 把 .java 文件编译成 .class文件（即字节码文件），java 再运行 .class 文件

- 流程

javac 编译 → 生成 .class → java 命令启动 JRE → JRE 启动 JVM → JVM 运行代码



#### **JRE**

JRE = Java Runtime Environment，Java 运行环境

包含：JVM、核心类库、运行工具

编译成 .class 文件后，就不需要编译工具、调试工具等等，所以Java把运行要用的工具单独抽离出来。

特点：不能写代码、不能编译、只能运行 Java 程序

交给 JVM 运行代码

#### **JVM**

JVM = Java Virtual Machine

真正跑代码的虚拟机

Java 代码并不是直接运行在系统中的，而是运行在**虚拟机**中的



### **Java版本**

#### **JavaSE**

JavaSE（Java Standard Edition，标准版）

通过 JVM 运行；

- 核心功能

字符串处理、集合、IO 流、多线程、面向对象、异常处理等

#### **JavaEE**

JavaEE（Java Platform, Enterprise Edition，企业版）

通过服务器（Tomcat/Jetty）运行；

- 功能

Servlet（处理网页请求）、JSP（动态网页）

Spring/SpringBoot（JavaEE 最主流的框架）

数据库连接（JDBC 是 JavaSE 的，但企业级用法属于 JavaEE）

分布式、微服务、服务器部署等



## **预备操作**

### **在空项目建立project**

右键项目名，点击新建，点击模块module。

右键src点击新建，点击软件包package。

右键软件包名，点击新建，点击Java类class。

### **小知识**

编译后的 .class 文件在工程路径下的 out 文件夹里

保存类文件的源文件名必须与有 public 修饰的类名相同：

如public class A1，源文件为A1.java

如类中有class B1，源文件还是A1.java

因为一个类中只能有一个 public 类

### **idea快捷键**

CTRL + Alt + T 包围方式

CTRL+D 复制当行到下一行

Ctrl+SHIFT+⬆/⬇ 上下移动当前代码

CTRL+/ 单行注释

CTRL+shift+/ 多行注释

Ctrl键+F2键 结束运行

shift键+F6 批量改变量名

Ctrl + Y 删除行

Ctrl + U 定位到接口

Ctrl + Shift + U 切换大小写

Ctrl + Shift + V 打开剪切板

**Shift + Enter 换行**

**Ctrl + Alt + L 设置代码格式**

**Ctrl + Alt + M 提取方法**

**F2 定位到报错处**

Ctrl + W 选中括号内

Ctrl+Alt+Shift+T 重构

Alt+Shift+C 最近更改

Ctrl+Shift+Backspace 定位最后编辑位置

Alt+Shift-+点击 添加光标

AIt+⬅/➡ 切换标签页

Ctrl+[ / ] 将文本光标移至匹配的大括号

Ctrl+Shift+C 复制路径

Debug：

F8 下一行

F9 下一个断点

F7 进入方法

Shift+F8 跳出方法



### **常用操作**

打开别人发的工程：点击打开，复制路径

重命名模块和目录，不能单一模块或者目录

删除模块：先移除，再删除

导入模块：先复制模块的工程中，再在文件中点击新建，点击现有源中的模块，复制路径



### **常见缩写**

sout ：System.out.println();

main：public static void main(String[] args) { ... }

soutm：打印当前方法名

fori：for (int i = 0; i < ; i++) { ... }



## **基础语法**

### **关键字**

1. 数据类型相关（定义变量用）

| 关键字  | 含义                                          | 例子                  |
| ------- | --------------------------------------------- | --------------------- |
| int     | 整数类型                                      | int age = 18;         |
| String  | 字符串类型（注意：String 不是关键字！是类名） | String name = "张三"; |
| boolean | 布尔类型（true/false）                        | boolean flag = true;  |
| double  | 浮点类型（小数）                              | double score = 99.5;  |

2. 流程控制相关（做判断 / 循环）

| 关键字 | 含义     | 例子                           |
| ------ | -------- | ------------------------------ |
| if     | 条件判断 | if (age > 18) {...}            |
| for    | 循环     | for (int i=0; i<10; i++) {...} |
| else   | 否则     | if (...) {...} else {...}      |
| while  | 循环     | while (flag) {...}             |

3. 类 / 方法相关（面向对象）

| 关键字 | 含义                    | 例子                         |
| ------ | ----------------------- | ---------------------------- |
| class  | 定义类                  | public class Test {...}      |
| public | 公共权限                | public void sayHi() {...}    |
| static | 静态（不用 new 就能用） | static String name = "李四"; |
| void   | 方法无返回值            | public void run() {...}      |

其他常用关键字

| 关键字 | 含义     | 例子                          |
| ------ | -------- | ----------------------------- |
| new    | 创建对象 | String[] arr = new String[5]; |
| null   | 空引用   | String name = null;           |
| return | 返回值   | return 100;                   |

### **字面量**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655483253-3aa39899-6f63-4fba-b349-93cffd11197e.png)

字面量 = 直接写在代码里的「具体值」（比如 18、"张三"、true）；

基本 / 引用数据类型 = 给这些 “具体值” 分类的「容器类型」；

System.out.println(888);//打印数字可以不加引号

System.out.println(**"**88\t**"**);//但如有转义字符就要加上引号

- 字符转义字面量

| 转义字面量 | 含义          | 例子                                              |
| ---------- | ------------- | ------------------------------------------------- |
| \n         | 换行          | System.out.println("你好\n世界"); → 换行输出      |
| \t         | 制表符（Tab） | System.out.println("姓名\t年龄"); → **对齐输出**  |
| \"         | 双引号        | String s = "他说：\"你好\""; → 输出 他说："你好"  |
| \\         | 反斜杠        | String path = "C:\\Java\\jdk"; → 表示 C:\Java\jdk |

\t：在打印的时候，把前面字符串的长度**补齐到8**，或者8的整数倍。最少补1个空格，最多补8个空格。

- 空值

null 是 Java 里唯一 “没有类型归属” 的字面量

运用场景：只能给**引用类型**赋值（String、数组、自定义类等），不能给基本类型赋值；

### **变量**

变量是内存中的一块区域，是用来存储数据的容器，方便后面使用。

eg：

第二行是给变量重新赋值，即盒子age里的20替换成10，

int 是用来声明变量，变量只需声明一次，但可以多次赋值；

第二行给已声明的变量重新赋值，不需要重复声明int，所以不用写

写成 int age = 10; 反而会报错

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655483221-8607ccbb-676e-4303-b35e-b91ff8bf3630.png)

打印变量不需加引号

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655483391-4014bc72-4b52-42de-a111-b4cf3a368dd0.png)

打印变量又想在后面加\t

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655483940-c08dc5d4-cacf-468e-bb79-ad9c0663a50b.png)

#### **数据存储**

在计算机中，任意数据都是以**二进制**的形式来存储的。

字符存储：存储的是字符对应ASCII码值（十进制）的二进制形式

图片存储也是用二进制，图片由无数的像素点组成，像素点的数据：用0-255*255*255表示颜色

声音存储也是二进制

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655484703-5bdaacbc-d7e5-415f-aaae-0f710a922979.png)

- 不同进制在代码中的表现形式

二进制：由0和1组成，代码中以 **0b** 开头

十进制：由0~9组成，前面不加任何前缀

八进制：由0~7组成，代码中以 **0** 开头

十六进制：由0~9还有a~f组成，代码中以 **0x** 开头



十进制转二进制：

一般是逢10进1，二进制是逢2进1

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655484753-3d29a657-ce12-4287-9f33-6b1b28a610e3.png)

第二种方法：除二取余

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655485122-2387a527-b916-4974-9cfc-cc08d9a84f84.png)



### **数据类型**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655485603-956fdeb0-5b4c-4e41-bcf8-1106befb8538.png)

注意：String是引用数据类型，不是基本数据类型

byte,short,int,**long**,**float**,double 表数范围由小变大

当两个 int 类型数值相除时，结果会自动舍弃小数部分**保留整数**：

int a = 3; int b = 2; a/b = 1

char只能存放一个字符，并用单引号界定内容，如'a'、'3'、'#'

注意：随便写一个整数字面量默认是int类型的，334254235555这个数据虽然没有超过long的范围，但是超过了int的范围，所以报错；

如果希望334254235555这个是long类型，需要显示的指定，加上 L

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655485054-23d78fb6-6b6a-431c-8dd7-46270bfafe3c.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655485645-f27bf755-e939-4ccf-b4ce-8f7b2b9e1cb7.png)



浮点型

浮点数字面量的默认类型是 double，double 的取值范围和精度都比 float 大，

这种赋值可能会导致精度损失 ，编译器会报错，提示可能会损失精度。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655485756-2f441241-4e7e-4cf9-bd08-b4cb38dbbd51.png)



### **标识符**

标识符：就是给类，方法，变量等起的名字。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655486394-aa3964f2-e30b-4c78-8374-f955568b7c37.png)

合法：26个字母，数字（不能排头），下划线，＄美元符号

### **方法**

#### **介绍**

方法(method)是程序中最小的执行单元。

重复的代码、具有独立功能的代码可以抽取到方法中。

用处：可以提高代码的复用性、可维护性

#### **格式**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655486509-61552c91-f925-4120-b5da-ff5f65d6c8c0.png)

例如：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655486810-49a9d809-cbec-434f-9a3d-3edfe8717971.png)

方法的使用：必须进行调用才可以执行方法

修饰符：控制方法的访问权限（如 public、private）或特性（如 static、final）。

返回值类型：方法执行后返回的数据类型（如 int、String），若不返回任何值则用 void。

方法名：自定义的标识符，遵循命名规范（如驼峰式 calculateSum）。

参数列表：方法接收的输入数据，无参数时为空。

形参：形式参数，是指方法定义中的参数

实参：实际参数，方法调用中的参数

方法的返回类型和参数的数据类型可以完全不同，如public **double** add(**int** a, **double** b)或 public **double** add(**int** a, **int** b)，

当返回类型与参数类型不同时，编译器会自动进行**隐式类型**转换。



**方法重载**

在同一个类中，可以定义多个 方法名相同，但参数列表不同的方法，这就是方法重载。

注意：返回值 / 修饰符不影响重载，方法重载只关心方法**名称相同**，**形参列表不同**（类型不同，个数不同，顺序不同）

作用：简化调用，只需要都叫 sum()，Java 会根据你传的参数自动匹配

如果没有重载，你要实现 “求和” 功能，就得写一堆名字不同的方法：

```java
// 不用重载：方法名必须不一样，调用者要记一堆名字
public static int sum2Int(int a, int b) { return a + b; }
public static int sum3Int(int a, int b, int c) { return a + b + c; }
public static double sum2Double(double a, double b) { return a + b; }

// 调用时：你得记住“sum2Int、sum3Int、sum2Double”等不同名字
int res1 = sum2Int(1,2);
int res2 = sum3Int(1,2,3);
double res3 = sum2Double(1.1, 2.2);
```

重载写法：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655487494-3b618d77-903f-4a3e-a383-f7b376afd2f7.png)

错误写法：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655487330-e093f166-8197-4415-8089-9c17838d87d9.png)



#### **方法内存**

方法调用的基本内存原理

具体的讲解在一维数组的Java内存分配

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655489327-f90673f8-6362-45d3-b129-ca9f756ee660.png)

传递基本数据类型时，传递的是真实的数据，形参的改变，**不影响**实际参数的。

传递引用数据类型时，传递的是地址值，形参的改变，**影响**实际参数的值。

```java
public static void main(String[] args) {
    int[] arr = {1, 2, 3};
    change(arr);
    System.out.println(Arrays.toString(arr));//4,2,3，有修改
    int n = 1;
    change(n);
    System.out.println(n);//1，无修改
}

private static void change(int n) {
    //change(int n) 修改的是方法栈帧内的拷贝值，所以 main 中的 n 不变。
    n = 2;
}

private static void change(int[] arr) {
    //change(int[] arr) 修改的是堆中的数组，所以 main 中的 arr 会变化
    arr[0] = 4;
}
```

### **类型转换**

1. **自动类型转换**

从 **“表示范围小” 的类型转换为 “表示范围大”** 的类型

从 “表示范围小” 的类型转换为 “表示范围大” 的类型也可以是**强制类型转换**，例如：

int a = 3; int b = 2; System.out.println((double) a/b);

注意英文不能用String而是用char

表示范围由小到大示意图：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655487798-d772db8c-2ffd-4138-9a3e-66b21b70b37f.png)

int num = 100;

double result = num; // int自动转换为double，结果为100.0

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655488769-c38b16cf-abf5-45bc-9c5f-6349673395d0.png)

1. double a = 2.2;

a = 10;//打印出来为10.0

2. int a = 10;

int b = 2;

double c = a+b;//打印出来是12.0

在表达式中，小范围类型的变量，会自动转换成表达式中较大范围的类型，再参与运算。

注意事项：

- 表达式的最终结果类型由表达式中的**最高类型**决定。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655488984-7ac2d57d-0bd2-428b-9b38-97446f0e28b2.png)

- byte short char 三种类型的数据在运算的时候，都会直接**先提升为 int**，然后再进行运算。

char 是字符怎么自动转化成 int？把字符换成它对应的编码数字。

例如：

```java
byte a = 1;
byte b = 2;
byte c1 = a+b;//报错
byte c2 = (byte) (a+b);//不报错
```

再例如：

用 byte 会报错

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655489018-85ac93e9-8e9a-4258-a7e9-194abbe17486.png)

需改成 int 不报错

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655489140-f9bee094-67c3-4774-a3ad-bdda38a8a225.png)

2. **强制类型转换**

从 **“表示范围大” 的类型转换为 “表示范围小”** 的类型，必须显式声明

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655490308-a17a29a6-c67b-4139-bb45-02b4b51df279.png)

注意事项：

强制类型转换可能造成数据（丢失）溢出；浮点型强转成整型，直接丢掉小数部分，保留整数部分返回

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655491681-34de83eb-c59b-4db2-98dd-1a25516a072b.png)

为什么要数据转换

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655491379-8849f4d7-404d-4be2-a6db-ebce779c3d91.png)



### **输入输出**

可以让用户键盘输入用户名和年龄，然后打印出来

输入字母用**String**，而不用 **char**

- 第一套体系

nextLine() 接收字符串，读一整行（包括空格）。

- 第二套体系

next() 接收字符串，读 空格 / 回车分隔的单个单词，

nextInt() 方法获取用户输入的整数，

nextDouble() 方法获取用户输入的浮点数。

弊端：键盘录入的两套体系不能混用的。

先用 nextInt，再用 nextLine 会导致下面的 nextLine 接受不到数据，

因为 nextInt 不接收回车，回车被下面的 nextLine 接收了，就输入不了其他数据了。

Scanner 本质是一个 “IO 流对象”（和文件、网络、控制台交互的工具），创建它时，系统会分配输入连接、内存、CPU 小资源等

sc.close() 核心作用是释放 Scanner 占用的系统资源，不关闭的话，这些资源会被 Scanner 一直占用，直到程序结束。

```java
Scanner sc = new Scanner(System.in);
sc.close();
```

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655490873-187a243a-744a-48d3-a8a9-6d9a1d491150.png)

```java
//题目：以下代码可能出现什么问题？
Scanner scanner = new Scanner(System.in);
System.out.print("输入年龄: ");
int age = scanner.nextInt();
System.out.print("输入姓名: ");
String name = scanner.nextLine();

//答案：nextLine读取到空字符串
//解释：scanner.nextInt() 只会读取数字，不会消费末尾的回车键 \n
//缓冲区里还残留换行符
//紧接着执行 nextLine()，它直接读到残留的换行，立刻结束，得到空字符串
```



```java
//题目：以下代码的输出结果是什么？
double price = 123.456;
System.out.printf("价格: %.2f元", price);

//答案：123.46
```



```java
//题目：以下代码的输出结果是什么？
System.out.printf("%10s|", "Java"); 
System.out.printf("%-10s|", "Java");

//答案：      Java|Java      |
//%10s表示右对齐，总宽度10个字符；
//%-10s表示左对齐，总宽度10个字符。"Java"有4个字符，所以会补6个空格。
```



### **运算符**

#### **算术运算符**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655491850-161e9eaf-7ca0-4342-9f94-7030cde05d68.png)

##### **个十百小练习**

键盘录入一个三位数，将其拆分为个位、十位、百位后，打印在控制台

公式总结：

个位：数值%10

十位：数值 / 10%10

百位：数值 / 100%10

千位：数值 / 1000%10

#### **+符号做连接符**

条件：当 + 两侧的操作数中至少有一个是字符串（String 类型）

如果**多个 +** 连续出现，会从左到右依次运算，一旦遇到字符串，后续的 + **都会被当作连接符**

 int a = 10;

 int b = 20;

 int c = 5;

 String s = "abc";

- 从左到右运算：先算10 + 20（加法），再与字符串拼接

 String result1 = s + a + b; 

 System.out.println(result1); // 输出：abc1020

- 括号改变优先级：先将(a + b)的结果与字符串拼接

 String result2 = s + (a + b); 

 System.out.println(result2); // 输出：abc30

- 左侧先出现字符串，后续 + 均为连接符

 String result3 = a + s + b + c; 

 System.out.println(result3); // 输出：10abc205

- 'b' 是 char 类型。在表达式中，**char 会自动转换为 int 类型**（使用其 Unicode 编码值 98），因此这里的 + 是加法运算

 String result4 = a +‘b'+ s; 

 System.out.println(result4); // 输出：108abc

- int a = 10; String c = **"**20**"**; （c为字符串）

String result = a + c; // "10" + "20" = "1020"



#### **自增自减运算符**

自增：++

作用：放在某个变量前面或者后面，对变量自身的值加1

自减：--

作用：放在某个变量前面或者后面，对变量自身的值减1

注意：++、-- 只能操作变量，不能操作字面量的。

eg：a++可以，++a也可以，但3++不行

a++与++a的区别

- int a = 5;

int result1 = a++; 

此时result1 = 5，a=6

- int b = 5;

int result2 = ++b;

此时result2 = 6，b=6



#### **赋值运算符**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655492546-b0e55ce3-4520-4414-893c-3be9390c6cdd.png)

复合赋值运算符会**自动隐含强制类型转换**，这与直接使用算术运算符不同：

a1+= a2 等价于a1=(byte)(a1+a2):



#### **关系运算符**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655492654-4b742a26-65a3-4aa5-a00f-677ff24bdbf1.png)

判断数据是否满足条件，最终会返回一个判断的结果，这个结果是布尔类型的值：true或者false。

注意：在java中判断是否相等一定是“==”，千万不要把“==”误写成“=”。

#### **三元运算符**

表达式？值1：值2

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655493152-c5105842-deed-42fd-a357-a45f2ec10ad6.png)

找出三个值的最大值

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655492934-f7b36a7b-511f-4a60-9adc-6f875e220190.png)



#### **逻辑运算符**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655494574-a571cba7-2508-4bc2-a9b4-ded678c45fa7.png)

int a = 1;

int b = 1;

System.out.println (a>1000&& ++b>1)；//false（&&发现左边条件为False右边直接不执行）此时b=1

System.out.printin (a>1000& +b>1)；//false（&即便发现左边条件为false右边依然执行）此时b=2

```java
boolean a = true;
boolean b = false;
System.out.println(a || b && false);
//优先级：&& 高于 ||
最后打印为：true
```

- 优先级

一元运算符（++、--、!） > 乘除 > 加减 > 比较 > 逻辑与 && > 逻辑或 || > 赋值运算符

#### **位运算**

| 运算符 | 名称     | 核心规则                   | 例子（3&2=2 对比）  |
| ------ | -------- | -------------------------- | ------------------- |
| &      | 按位与   | 全 1 才 1，否则 0          | 3&2=2               |
| \|     | 按位或   | 有 1 就 1，全 0 才 0       | 3\|2=3（11\|10=11） |
| ^      | 按位异或 | 不同为 1，相同为 0         | 3^2=1（11^10=01）   |
| ~      | 按位取反 | 0 变 1，1 变 0（含符号位） | ~3=-4               |
| <<     | 左移     | 二进制左移，右边补 0       | 3<<1=6（11→110）    |
| >>     | 右移     | 二进制右移，左边补符号位   | 3>>1=1（11→1）      |

### **程序流程控制**

#### **分支结构**

##### **if**

if分支的三种形式

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655494827-de7ff003-b3a3-4f5f-adf2-2c0cbd439b89.png)

具体例子：

数字可**从小写到大**，这段代码是**按顺序执行的递进**判断，

前面的条件会 “过滤” 掉不符合的情况，后面的条件可以**省略前面已验证过的判断：**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655494407-17cdbbd6-b096-4558-ad39-fa518e08b9f2.png)

从大写到小则需两个条件合并：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655494246-87b455a1-511e-4e83-9122-496aaed28c88.png)

##### **switch**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655495095-1438b69e-b00c-48a8-b2d0-d02930913bd2.png)

注意：

1. switch的表达式类型只能是 byte、short、int、char。JDK5开始支特枚举。JDK7开始支持string，不支持double、float(即不支持小数)、Long
2. case给出的值不允许重复，且只能是字面量，不能是变量。
3. 正常使用switch的时候，不要忘记写**break**，否则会出现穿透现象。
4. default 后一般不写 break

如有重复代码，通过穿透现象合并，简化重复代码

周一二三四没有break，会穿透到周五执行代码

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655495680-f3cd95dc-3c5a-4e97-bde6-b20219b48930.png)

也可以简写成：

```java
case "周一","周二","周三","周四","周五":
    System.out.println("好好学习");
    break;
//最简
case "周一","周二","周三","周四","周五"->
    System.out.println("好好学习");
```

- 使用枚举的switch语句中，case标签应该如何写

使用枚举代替魔法数字

```java
enum Color { RED, GREEN, BLUE }
Color color = Color.RED;
switch (color) {
    case RED:
        System.out.println("红色");
        break;
    case GREEN:
        System.out.println("绿色");
        break;
    case BLUE:
        System.out.println("蓝色");
        break;
}
```

###### **新特性**

- 传统 switch：要写break，漏写会导致 case 穿透

```java
int num = 2;
String result;
switch (num) {
    case 1:
        result = "周一";
        break; // 必须写 break，否则会“穿透”到下一个case
    case 2:
        result = "周二";
        break;
    default:
        result = "未知";
}
System.out.println(result); // 输出 周二
```

- 新语法 switch：不用写 break，自动终止当前 case、可以直接返回值给变量。

```java
int num = 2;
// 直接赋值，无需 break，不会穿透！
String result = switch (num) {
    case 1 -> "周一";
    case 2 -> "周二";
    default -> "未知";
};
System.out.println(result); // 输出 周二
```

##### **if与switch区别**

一般用if分支，if可有**区间**判断，**多种**分支形式

当前条件是区间的时候，建议使用if分支结构来实现

当条件是与一个一个的值比较的时候，建议用switch更合适

#### **循环结构**

循环条件的计算结果是boolean类型

##### **for**

简写 fori，再回车

如果倒着遍历，则 forr

需求：求1-100之间的数据和

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655495706-ea0d2347-26bd-47ab-a5ac-6f369fd995d4.png)

需求：求1-100之间的奇数和

法一：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655496087-1d6f528f-ccd1-4334-9224-856cea20323a.png)

法二：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655496114-d59e3fe1-27af-4a83-bcd0-d422ee0f26ae.png)

##### **while**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655496777-7714acdc-bd97-4cd8-a89d-b475469184dd.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655497104-e8632b98-4fbd-4d56-b52f-176a8e10304f.png)

使用规范：知道循环几次：使用 for；不知道循环几次建议使用：while。

即用while**计算循环次数**

计算一张纸折叠多少次才到山峰高度：

- 用while语句写（主要）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655497443-510637ac-5149-416d-af5f-38c2fbfb90e8.png)

- 用 if 语句写：注意count定义在循环上面，不是里面，这样才能打印出来

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655497503-9e99e92b-868c-4c63-a02f-6e08f000fb96.png)

答案为27次

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655498492-e014efe2-f75b-441a-8ee4-43cc2e6432ce.png)

- 因为while循环的 i 定义在循环上面，而不是里面

###### **数字回文**

```java
Scanner sc = new Scanner(System.in);
System.out.println("请输入数字：");
int num = sc.nextInt();
int temp = num;//保存num的原始值到temp，因为后续num会被修改
int num2 = 0;//存储反转后的数字
while (num!=0) {
    //num最后会除到零，所以不能等于零
    int ge = num % 10;
    num /= 10;//去除原来的个位数，121->12
    num2 = num2 * 10 + ge;//num2 = 0→1→12→121
}
System.out.println(num2 == temp);
```

##### **do-while**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655498132-ce7f7378-8754-496c-8e63-ed01a3543a11.png)

特点：先执行后判断，说明它一定会执行一次

即使循环条件为false，都会先循环一遍

##### **死循环**

死循环的写法

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794656729010-3ea4b21f-12df-400d-933a-8ebf094a493a.png)



##### **循环嵌套**

打印十行十列个星星：

- for循环（主要）

初始化部分（**int i = 0**）只在整个 for 循环开始前**执行一次**，i不会归零，仅通过迭代更新

当外层循环 i=0 时，执行 int j = 0，当外层循环 i=1 时，重新执行 int j = 0

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794656747297-3790d64a-844a-413a-96cd-b4fff9e3ca80.png)

- while循环

**int j = 0**是外层循环体的第一行代码，因此每次外层循环执行时，都会重新声明j并初始化为 0

**int i = 0**是在外层循环之外声明的，不会归零

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794656758882-e7075ce9-bcd7-40c0-b2ce-a74dd55ea246.png)

打印九九乘法表

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794656768304-5f037ea4-91af-4def-84a4-a20921e13a36.png)

- for循环（主要）

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794656778360-eef5e9d0-57f5-45f6-9bd4-d620fde79026.png)

- while循环

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794656847852-07a846d0-b1b9-4201-b6a7-3bedb116b000.png)



##### **跳转控制语句**

- break：结束整个循环。
- continue：跳过本次循环，继续执行下次循环

**注意事项**：

break：只能用于结束**所在循环(内层循环)**，或者结束所在**switch分支**的执行。

continue：只能在循环中进行使用。

break跳出循环，return跳出方法



- 带标签跳转

标签必须紧跟在循环语句前面。

配合 break/continue，用来跳出多层嵌套循环。

break outer; 跳出标签对应的循环，跳出外层循环

continue outer; 跳到标签循环的下一轮

```java
outer: //标签
for(int i=1;i<=3;i++){
    for(int j=1;j<=3;j++){
        if(j==2){
            break outer; //直接跳出outer标记的外层循环
        }
        System.out.println(i+"-"+j);
    }
}
```



#### **综合实战**

##### **简易计算器**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655500869-167849d7-eed8-44b2-8c4b-4b02b9cbfa92.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655501052-c3d6f6dd-b05f-4e6e-a2fc-3aedeea3ee66.png)

##### **猜数字**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655501180-52bc4083-34f8-40f2-9cef-564896c7c3bc.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655501292-dace25a4-8470-4311-9f09-9b1be1798001.png)

##### **验证码**

每位只能是数字或大写字母或小写字母

```java
public static void main(String[] args) {
    System.out.println(getCode(4));
}

private static String getCode(int n) {
    //n位验证码，大小写字母和数字随机组合
    String code = "";
    for (int i = 0; i < n; i++) {
        //大写字母0，小写字母1，数字2
        int type = (int) (Math.random() * 3);//[0,1)*3=[0,2]
        switch (type) {
            case 0 -> {
                char big = (char) ('A' + (int) (Math.random() * 26));//[0,1)*26=[0,25]
                code += big;
            }
            case 1 -> {
                char small = (char) ('a' + (int) (Math.random() * 26));
                code += small;
            }
            case 2 -> {
                int num = (int) (Math.random() * 10);//[0,1)*10=[0,9]
                code += num;
            }
        }
    }
    return code;
}
//用StringBuilder类
private static String getCode(int n) {
    //n位验证码，大小写字母和数字随机组合
    StringBuilder code = new StringBuilder();
    for (int i = 0; i < n; i++) {
        //大写字母0，小写字母1，数字2
        int type = (int) (Math.random() * 3);//[0,1)*3=[0,2]
        switch (type) {
            case 0 -> {
                char big = (char) ('A' + (int) (Math.random() * 26));//[0,1)*26=[0,25]
                code.append(big);
            }
            case 1 -> {
                char small = (char) ('a' + (int) (Math.random() * 26));
                code.append(small);
            }
            case 2 -> {
                int num = (int) (Math.random() * 10);//[0,1)*10=[0,9]
                code.append(num);
            }
        }
    }
    return code.toString();
}
```

##### **找质数**

找到质数prime

```java
public static void main(String[] args) {
    for (int i = 0; i < 100; i++) {
        if (prime(i)) {
            System.out.print(i+" ");
        }
    }
}

private static boolean prime(int x) {
    if (x<=1)return false;
    if (x==2)return true;
    for (int i = 3; i*i <= x; i++) {
        //2已经判断过了，所以从3开始
        //循环到x平方根就行
        if (x%i==0)return false;
    }
    return true;
}
```

### **一维数组**

#### **静态初始化**

静态初始化数组：定义数组的时候，数据已经确定好了。

静态初始化数组时，不能同时指定长度：

如 int[4] nums = {10, 20, 30, 40}; 为错误写法（在c语言中即合法）

int[] nums = {10, 20, 30, 40};为正确写法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655501698-f135689b-d661-47d9-8722-20a4e13908c6.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655505284-3e60caf5-22e7-4f39-8565-e98d969d3d60.png)

```java
[I@7cc355be
[ → 代表这是一个数组
I → 代表数组类型是 int（整数）
@7cc355be → hashCode() 的十六进制值，
这是 JVM 生成的哈希码（用来唯一标识对象），不同虚拟机、不同运行时间，这个值都会变
```

| 打印结果                | 含义              |
| ----------------------- | ----------------- |
| [I@xxx                  | int 数组          |
| [S@xxx                  | short 数组        |
| [D@xxx                  | double 数组       |
| [Ljava.lang.String;@xxx | String 字符串数组 |

#### **动态初始化**

- 快捷写法：new int[].var
- 遍历数组快捷写法：数组名.fori

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655504500-f5315682-4991-4d5f-a328-23ac58470b8b.png)

#### **小练习**

- 遍历数组

错点：

1. 数组里有 null，遍历会报空指针异常。所以遍历时要添加条件（数组元素!=null）。
2. 数组长度可以一开始就只有3个位置，再加一个元素就新建数组，新数组长度 = 旧数组长度 + 1。

再使用 System.arraycopy(students,0,students1,0,students.length); 把旧数组元素拷贝给新数组。

1. 数组删除元素，即数组不再引用它。

```java
static Scanner sc = new Scanner(System.in);
public static void main(String[] args) {
    Student s1 = new Student("a001", "jack", 22);
    Student s2 = new Student("a002", "tom", 20);
    Student s3 = new Student("a003", "rose", 18);
    Student[] students = new Student[4];
    //4个位置，但只放了3个元素。数组里有 null，遍历会报空指针异常
    students[0] = s1;
    students[1] = s2;
    students[2] = s3;
    String id;
    while (true) {
        System.out.println("输入学生学号：");
        id = sc.next();
        if (!notEqual(students,id)) {
            System.out.println("学号不能重复！");
        }else break;
    }
    System.out.println("输入学生名字：");
    String name = sc.next();
    System.out.println("输入学生年龄：");
    int age = sc.nextInt();
    Student s4 = new Student(id, name, age);
    students[3] = s4;
    System.out.println(Arrays.toString(students));
    while (true) {
        int index = findStuById(students);
        //删除学生，即数组不再引用它
        if (index!=-1){
            students[index] = null;
            System.out.println("删除成功！");
            break;
        }else {
            System.out.println("删除失败");
        }
    }
    System.out.println("学生信息："+ Arrays.toString(students));
}

private static int findStuById(Student[] students) {
    System.out.println("输入学生学号（删除）：");
    String inputId = sc.next();
    for (int i = 0; i < students.length; i++) {
        if (students[i]!=null&&students[i].getId().equals(inputId)) {
            //数组里有 null，遍历会报空指针异常，所以要判断不等于null
            return i;
        }
    }
    return -1;
}

private static boolean notEqual(Student[] students, String id) {
    for (Student student : students) {
        if (student!=null && student.getId().equals(id)){
            return false;
        }
    }
    return true;
}
```

- 数字加密

某系统的数字密码（大于0），比如1983，采用加密方式进行传输。

规则：先得到每位数，然后每位数都加上5，再对10求余，最后将所有数字反转，得到一串新数。

eg：1983，加密后：8346

```java
Scanner sc = new Scanner(System.in);
System.out.println("请输入密码：");
int num = sc.nextInt();
int n = num;
int count = 0;
while (num!=0){
    num /= 10;
    count++;
}
int[] arr = new int[count];
int a = 1;
//把每位数写进数组元素
for (int i = arr.length-1; i >= 0; i--) {
    arr[i] = n/a%10;
    a *= 10;
    //加密：加上5，再对10求余
    arr[i] = (arr[i]+5)%10;
}
//加密：首尾交换
for (int i = 0,j = arr.length-1; i < j; i++,j--) {
    int t = arr[i];
    arr[i] = arr[j];
    arr[j] = t;
}
//把数组元素组合成数字,8346
int sum = 0;
for (int i = 0; i < arr.length; i++) {
    sum = sum*10 + arr[i];
}
System.out.println("加密后："+sum);
```

- 存 5 名学生的成绩，用数组存储，并打印出最高分、最低分、平均分。

错点：max、min 初始化为 arr[0]，不能初始化为 0 。

因为如果数组元素都是负数，则 max 就成 0 了；或者数组元素都大于 0，则 min 则成 0 了。

```java
public static void main(String[] args) {
    int[] arr = new int[5];
    Scanner sc = new Scanner(System.in);
    for (int i = 0; i < arr.length; i++) {
        System.out.println("请输入第"+(i+1)+"位学生的成绩：");
        arr[i] = sc.nextInt();
    }
    int max = arr[0];
    int min = arr[0];
    int sum = arr[0];
    for (int i = 1; i < arr.length; i++) {
        sum+=arr[i];
        if (arr[i]>max){
            max = arr[i];
        }else if (arr[i]<min){
            min = arr[i];
        }
    }
    System.out.println("最高分：" + max);
    System.out.println("最低分：" + min);
    System.out.println("平均分：" + sum*1.0/arr.length);
}
```

- 交换数组首尾元素

```java
public static void main(String[] args) {
    int[] arr = new int[]{1,2,3,4,5};
    for (int i = 0,j = arr.length-1; i < j; i++,j--) {
        int t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }
    System.out.println(Arrays.toString(arr));
}
```

- 斗地主小游戏

开发一个简易版的斗地主游戏，要求只完成做牌（存储54张牌）、洗牌。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655504322-118ea11e-f654-4856-9126-ae95491a33a0.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655503620-6f5196c2-ff2f-4a41-a9e7-3a8b8306440a.png)

- 双色球

红球：从 1-33 个数字中选 6 个（不可重复）；蓝球：从 1-16 个数字中选 1 个；

注意红球号码不可重复，用户输入的号码也不能重复。

```java
public static void main(String[] args) {
    int[] arr = new int[7];
    //生成随机球
    for (int i = 0; i < 7; i++) {
        if (i == 6) {
            arr[i] = (int) (Math.random() * 16 + 1);
        } else {
            while (true) {
                int num = (int) (Math.random() * 33 + 1);
                if (notEqual(arr,num)){
                    arr[i] = num;
                    break;
                }
            }
        }
    }
    System.out.println(Arrays.toString(arr));
    //用户输入球
    Scanner sc = new Scanner(System.in);
    int[] arr1 = new int[7];
    for (int i = 0; i < arr1.length; i++) {
        if (i <= arr1.length - 2) {
            while (true) {
                System.out.println("请输入第" + (i + 1) + "个红色号码：");
                int n = sc.nextInt();
                if (n < 1 || n > 33||!notEqual(arr1, n)) {
                    System.out.println("数字输入错误！");
                } else {
                    arr1[i] = n;
                    break;
                }
            }
        } else {
            while (true) {
                System.out.println("请输入蓝色号码：");
                int n = sc.nextInt();
                if (n < 1 || n > 16) {
                    System.out.println("数字输入错误！");
                } else {
                    arr1[i] = n;
                    break;
                }
            }
        }
    }
    int countRed = 0;
    int countBlue = 0;
    //判红
    for (int i = 0; i < arr.length - 1; i++) {
        for (int j = 0; j < arr.length - 1; j++) {
            if (arr1[i] == arr[j]) {
                countRed++;
                break;
            }
        }
    }
    //判蓝
    if (arr[6] == arr1[6]) {
        countBlue++;
    }
    if (countRed == 6) {
        if (countBlue == 1) {
            System.out.println("恭喜你中了一千万！！！");
        } else System.out.println("恭喜你中了五百万！！！");
    } else if (countRed == 5) {
        if (countBlue == 1) {
            System.out.println("恭喜你中了三千块！");
        } else System.out.println("恭喜你中了两百块");
    } else if (countRed == 4) {
        if (countBlue == 1) {
            System.out.println("恭喜你中了两百块");
        } else System.out.println("恭喜你中了十块");
    } else if (countRed == 3) {
        if (countBlue == 1) {
            System.out.println("恭喜你中了十块");
        } else System.out.println("你没中，你个辣鸡");
    } else if (countRed == 2) {
        if (countBlue == 1) {
            System.out.println("恭喜你中了五块");
        } else System.out.println("你没中，你个辣鸡");
    } else if (countRed == 1) {
        if (countBlue == 1) {
            System.out.println("恭喜你中了五块");
        } else System.out.println("你没中，你个辣鸡");
    } else if (countRed == 0) {
        if (countBlue == 1) {
            System.out.println("恭喜你中了五块");
        } else System.out.println("你没中，你个辣鸡");
    }
}

private static boolean notEqual(int[] arr, int num) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i]==num)return false;
    }
    return true;
}
```



#### **Java内存分配**

Java 虚拟机把内存分成 5 块区域：

1. 栈（Stack）：放 局部变量、方法执行。

局部变量 即为 基本数据类型变量（int, char, boolean...）、数组的引用变量、对象的引用变量。

特点：随方法进栈出栈，自动释放。

2. 堆（Heap）：放 new 出来的对象（对象里面装的：数组的真实数据，对象里面的成员变量）

引用变量在栈；真实对象 / 数组在堆；栈里的变量只是指向 堆的地址。

基本类型的值存在栈里，是因为它们大小固定、数据简单、占空间极小。（int 固定 4 字节、double 固定 8 字节）

引用类型的值不能存在栈里，因为它们因为数据大、不固定、复杂。

特点：靠垃圾回收释放

3. 方法区（Method Area）：放 代码、类信息、静态变量

特点：类加载后就一直存在。

4. 本地方法栈（Native Method Stack）：执行 Java 以外的代码（C/C++）

5. 程序计数器（PC 寄存器）：记录执行到哪一行代码

例如：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655505177-f713e8cc-ce36-4079-afce-24b2b41d8427.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657466895-30a9fc78-d141-4a49-829f-3d94fe5d5159.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657475038-d1b8ba34-927c-42d8-819b-de5839ec8700.png)

#### **数组复制**

以下哪种数组复制方法性能最高？

- A. for循环逐个复制
- B. System.arraycopy()
- C. Arrays.copyOf()
- D. clone()方法

System.arraycopy()是原生方法，直接在JVM层面进行内存复制，性能最高，是 JDK 内部很多复制方法的底层依赖。

Arrays.copyOf 内部就是调用 System.arraycopy，但是多了 创建新数组、判断长度、返回数组的 Java 层开销。

数组的clone()也是 native 方法，底层复制整个数组。但只能完整复制整个数组，不能指定起始位置、复制部分片段；内部实现和arraycopy有差异，性能略弱于 System.arraycopy。



### **二维数组**

#### **定义**

数组中的每个元素都是一个一维数组，这个数组就是二维数组。

#### **静态初始化**

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657497546-f1d39efd-47db-48b1-9ce1-fc47aaaebbcc.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657505707-10508379-c3a4-4236-8704-180d02aa6684.png)



#### **动态初始化**

行数一定要填，不然报错

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657515684-b152645f-45a4-40a1-a6ed-b044abeb4255.png)

三行五列

```java
//以下三种初始化均正确
int[][] matrix = new int[3][4];
int matrix[][] = new int[3][4];
int[] matrix[] = new int[3][4];
```

#### **遍历**

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657535565-3cc9149a-9a87-4816-b693-594f3f714c95.png)

#### **内存**

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657569598-accd9649-e449-4d6c-89c4-37f35ccd709b.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657579373-ebd205c2-9081-4214-a390-8eed6b6ef56e.png)

#### **综合练习**

##### **一维转二维**

int0 tempArr={0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15};//共16个数据

要求：打乱一维数组中的数据，并按照 4 个一组的方式添加到二维数组中。

- 方法一

```java
int[] arr = new int[]{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15};
int[][] arr2 = new int[4][4];
for (int i = 0; i < arr.length; i++) {
    arr2[i / 4][i % 4] = arr[i];
}
System.out.println(Arrays.deepToString(arr2));
```

- 方法二

```java
int[] arr = new int[]{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15};
int[][] arr2 = new int[4][4];
int index = 0;
for (int i = 0; i < arr2.length; i++) {
    for (int j = 0; j < arr2[i].length; j++) {
        arr2[i][j] = arr[index];
        index ++;
    }
}
```

##### **数字华容道**

n=4

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657605158-fd83b93e-219e-4384-a520-b8bf799dd0ef.png)

n=5

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657614075-17b9ba74-b821-401e-801b-cb68626eef26.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657624635-204b457c-2392-4ea9-8976-0043a7f4b6ae.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655508650-5141d00d-1ff6-425a-bd50-4d4342b27313.png)

### **GUI**

Graphical User Interface又称图形用户接口

- Swing 常用组件

| 组件名             | 作用                             |
| ------------------ | -------------------------------- |
| **JFrame**         | 主窗口（最外层容器）             |
| **JPanel**         | 面板（放按钮、输入框的中间容器） |
| **JButton**        | 按钮                             |
| **JLabel**         | 管理文字和图片                   |
| **JTextField**     | 单行输入框                       |
| **JPasswordField** | 密码框                           |
| **JTextArea**      | 多行文本域                       |
| **JCheckBox**      | 复选框                           |
| **JRadioButton**   | 单选框                           |
| **JOptionPane**    | 弹出框（提示、确认）             |
| **JMenuBar**       | 最上层菜单栏                     |

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655509128-bac47876-4acb-454b-9e05-b4aa6a860be6.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655509327-316087d6-3c78-4345-bfd4-add8d17a95ff.png)

已经解决了，if里面不要写removeall,removall写在if上面当成员变量，还有if下面不能写return这里remove了还要再初始化图片



### 面向对象

#### **入门**

单个数据用变量存储；同类型批量数据用数组存储；

而面向对象存储的核心是 “属性 + 行为” 的封装，

属性一般封装到**实体类**（User/Product/Order），行为封装到**业务类**（UserService/OrderService）**。**

把 “事物的属性” 和 “事物做的行为”（**方法**）打包成 “类”，再通过 “对象”(类的具体实例) 存储具体数据。

eg：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659531443-2893dd90-9adc-4dec-8d46-0c35bd8c9c8f.png)



##### **对象的成员变量的默认值规则**

成员变量有默认值，局部变量没有默认值。

因为成员变量在堆里，Java **自动清零**（就是设置默认值）防脏数据，堆里自动初始化。

存储在栈帧的局部变量表中，分配时 JVM 不会自动清零，只是复用之前栈内存的位模式，所以局部变量在使用前必须被显式初始化，否则编译不通过。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659531707-4e98a786-0dfe-4e1a-aa52-32a2756d1d8c.png)



##### **内存分配**

局部变量存储在栈里，变量 t1 存储的是堆中 t1 对象的地址，JVM 先通过栈中 t1 存储的地址（如 0x0012AB）找到堆中的 t1 对象，

堆里的对象，会保存一个指向方法区类的地址，让对象知道自己属于哪个类，能调用哪些方法，

类 存储在方法区里面，方法区里的方法需要调到栈中执行，执行完之后就清理掉。

当 main 方法也执行完，在栈顶弹出，则局部变量就没有了，没有变量在引用对象，则堆中的对象也被清理。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659532214-cdb55747-3fb3-4288-825a-f92cc1d15bda.png)



#### **类的基本语法**

##### **构造器**

自动生成构造器：右键鼠标，点击生成，点击构造函数，按确定是有参，按无选择是无参

Java 里的对象 “出生”（用new创建）时，也会先执行 “**构造器**”，给对象做 “**初始化**”。

每创建一次对象，就会调用一次构造方法，主要干两件事：

1. 给对象在堆内存中 “开辟空间”；
2. 给对象的属性做 “初始化”（比如给name设默认值null，或直接设成我们想要的值）。

特点：构造器的名字必须和**类名一样**（比如test类的构造器就叫test()），而且**没有返回值**（连void都不能写）。

作用：作用是给成员变量进行初始化的；无参则设为默认值，有参则赋值。

- 构造器禁忌：不要调用可重写（非 private、非 final）的成员方法。

- - 先执行父类构造器，再执行子类构造器
  - 如果父类构造函数里面调用了一个可以被子类重写的方法：此时子类对象还没有完成初始化，子类成员变量还是默认值，但却执行了子类重写后的方法，会读到未初始化的数据，产生诡异 bug。

###### **无参构造器**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659531506-61086794-80eb-44db-b188-2f93c74548ff.png)

- 你自己写了构造器（不管是无参还是有参），Java 就不再提供默认的无参构造器。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659531475-aa571731-08a7-4d6e-a3ae-ae1897a8cf18.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659532398-a59d8204-f5fd-41d0-820f-b21e711cb4df.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659532639-52a8d025-2f1a-4af5-acd4-f3135f6ff200.png)

###### **有参构造器**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659532721-aa0cf376-9072-4889-978e-e1b8745b5053.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659532891-de123121-f9e9-4cd5-8628-a06cf6e0953b.png)

如果为类定义了有参数构造器，类默认的无参数构造器就没有了，

此时如果还想用无参数构造器，就必须自己手写一个无参数构造器出来。

###### **应用**

- 在不同类传集合

1. 构造器传参（推荐）

数据封装在类内部，依赖由外部传入，符合封装、依赖注入思想。

```java
public class Test {    
    public static void main(String[] args) throws Exception {
        ArrayList<String> list = new ArrayList<>();
        list.add("aaa");
        list.add("bb");
        list.add("c");
        new CeShi(list).start();
    }
}
public class CeShi {
    private ArrayList<String> list;
    public CeShi(ArrayList<String> list) {
        this.list = list;
    }

    public void start() {
        System.out.println("已进入测试类");
        System.out.println("打印集合："+ list);
    }
}
```





2. 静态变量

仅适用于传递常量

```java
public class Test {
    static ArrayList<String> list = new ArrayList<>();

    public static void main(String[] args) throws Exception {
        list.add("aaa");
        list.add("bb");
        list.add("c");
        new CeShi().start();
    }
}
public class CeShi {

    public void start() {
        System.out.println("已进入测试类");
        System.out.println("打印集合："+ Test.list);
    }
}
```



##### **this**

this 就是一个变量，可拿到对象的地址。哪个对象调用方法，this 就拿到哪个对象。

- 作用

解决变量名称冲突问题，主要用来区分成员变量和局部变量。

```java
Student s1 = new Student();
s1.setName("jack");
//s1调用这个方法，则this.name = name;则此处的this指s1对象
```

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659533734-e3cc1bd4-eb01-4794-9726-5c81e0374b94.png)



- 链式编程

方法链式调用通常通过返回 this 来实现，操作同一个对象。



###### **就近原则**

Java 在使用一个变量时：优先找离自己最近的，找不到再往上找。

方法内局部变量或方法参数（近），类成员变量（远）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659533700-09e45791-20a0-4c4c-9905-246e81965e95.png)

###### **成员变量与局部变量**

1. 定义位置不同

成员变量：定义在类中，方法之外（包括构造方法、普通方法、静态方法等）。

例：public **class** Person { String name; // 成员变量 }

局部变量：定义在方法内、方法参数中、代码块（如if/for块）内。

例：public void run() { int count = 1; // 局部变量 }

2. 作用范围不同

成员变量：作用范围是整个类，在类的所有方法中都能访问。

局部变量：作用范围仅限定义它的方法或代码块，出了这个范围就无法访问。

3. 默认值不同

成员变量：如果没有手动赋值，会有默认值（如int默认 0，String默认null，布尔值默认false）。

局部变量：没有默认值，必须手动赋值后才能使用，否则编译报错。

4. 内存存储位置不同

成员变量：随着对象的创建存储在堆内存中，对象被回收时变量也随之销毁。

局部变量：存储在栈内存中，方法或代码块执行结束后立即销毁。

5. 修饰符使用不同

public、private等修饰符是用来修饰类、**成员变量**、方法的，而在方法内部定义的变量（局部变量）不能加这些修饰符。

局部变量是在方法、代码块内部定义的变量，它的作用范围仅限于当前方法或代码块，不存在 “访问权限” 的问题。



##### **封装**

把描述对象状态的属性（数据）和操作这些数据的方法（行为）绑定在同一个类中，形成一个独立的整体，对外隐藏内部实现细节，只暴露必要的访问接口。

把类的属性（变量）和方法（行为）包装起来，不让外部直接访问 / 修改内部数据，只对外提供安全的访问入口。

举例：你每天用手机打电话、发消息，但你不需要知道手机内部的电路板怎么工作、电池怎么供电；

手机厂商把 “复杂的内部零件” 藏起来，只给你留了 “简单的按键 / 屏幕”（也就是你能操作的部分）。

这种 “**隐藏内部细节，只暴露必要操作**” 的设计，就是**封装的核心思想**。

- 封装的写法步骤

1. 成员变量私有化：用private修饰

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659533789-f7a6ff9a-7560-4ca4-9c22-5c9994952d4d.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659533933-70b08b70-f7f6-41c8-9595-aee545cbffdc.png)

2. 提供公共访问方法：写 getter 和 setter 方法

封装属性时，get和set方法有固定写法：

get属性名()：用来读属性（比如getScore()、getName()）；

set属性名(参数)：用来写属性，还能在方法里加 “**校验规则**”（比如成绩不能超过 100）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659534295-04ba4f07-ff80-407b-9346-5cccf8b1ade1.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659534735-6c558d2f-4fda-48c6-8ea4-5c7dc4a05333.png)

自动生成setter和getter：右键鼠标，点击生成，选择setter和getter，按shift键选择全部





##### **JavaBean(实体类)**

**JavaBean** 是**封装**思想的一种 “具体体现”

条件：

- 类必须是 “公开的”（用 public 修饰）
- 必须有 “无参构造器”；

如类里没有任何其他构造器（比如有参构造器），这时候 Java 会自动给你加一个默认的无参构造器；

如写了有参构造器，则必须手动添加无参构造器

- 属性必须全部 “私有”（用 private 修饰）
- 必须有 “get/set 方法”
- 不写行为方法，那是 service 类写的

**实体类的核心特点：“只装数据，少做逻辑”**

实体类的主要作用是**存储数据**，而不是做复杂的计算或操作。它就像一个 “数据载体”，里面主要包含：

一堆**属性**（比如学生的姓名、年龄、成绩）；

这些属性的**get/set 方法**（用来存数据和取数据）；

有**构造器**（用来创建对象时初始化数据）。

但通常不会有**复杂的业务逻辑**（比如不会在实体类里写 “计算全班平均分” 这样的代码）。

实体类的对象只负责数据存取，而对数据的业务处理交给其他类的对象来完成，以实现数据和数据业务处理相分离。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659534929-a13a381e-55bc-4060-a40b-45a85dbffa22.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659535155-d592561f-4d1b-4a36-ba7e-0d825efe456a.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659535358-1b38e0ce-4952-49d5-aec1-a19ce6349b14.png)



##### **static**

###### **修饰成员变量**

- 实例变量（对象的变量）：无static修饰，属于对象，每个对象单独拥有自己的变量，

就像每个学生都有自己的 “姓名” “年龄”，各不相同。

- 静态变量（类变量）：有static修饰，属于类，在计算机里只有一份，会被类的全部对象共享，

就像一个班级里，“班级名称” 是所有学生共用的。

静态变量存在堆中的静态区里。

静态变量被方法修改后，就会永久修改。而不是原先的值。

访问方式：

类名.静态变量（推荐）

对象名.静态变量（不推荐）



static String classname；

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659535761-0362035b-32dd-4836-89a3-80ec0a9b5725.png)



- 案例：记录创建了多少个用户对象

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659536258-24e391ca-04c6-4445-a549-a92966a99e18.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659536287-bfb8008b-37dc-42c1-bb67-19958c7d5c8b.png)



###### **修饰方法**

- 静态方法：有static修饰的成员方法，属于类。

特点：不需要创建对象，直接通过类名就能调用。

类名.静态方法（推荐）

对象名.静态方法（不推荐）

- 实例方法：无static修饰的成员方法，属于对象。

必须先创建对象才能调用

对象名.实例方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659536807-df353f2f-073f-4f03-88cf-0dcf86ba776a.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659536510-18d9f12e-e287-49ab-b240-f63db5f9a536.png)

区别：如果这个方法只是为了做一个功能且不需要直接访问对象的数据，这个方法直接定义成静态方法。

如果这个方法是对象的行为，需要访问对象的数据，这个方法必须定义成实例方法。



![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659537032-b2a5af55-e87a-4252-af33-397a714c5199.png)



###### **静态方法的应用**

- 静态方法的应用场景：可设计工具类、测试类。
- 好处：工具类中的方法都是静态方法，每个类方法都是用来完成一个功能的；

提高了代码的复用性；调用方便，提高了开发效率。

JavaBean是描述一类事物的，因为工具类不是描述一个事物的，只是提供方法，创建它的对象无意义，

所以把工具类的构造器私有化，使得外界无法创建工具类的对象。

```java
public final class Utils { // final类不会被继承
    private Utils() {} // 构造器私有化，不会被实例化

    public static void print(){
        // 静态方法直接被类名调用，不用创建对象
        // 实例方法需要创建对象调用，此时对象只是为了调用方法，而对象会占内存
        System.out.println(666);
    }
}
```



###### **注意事项**

- 静态方法可以直接访问静态成员，不可以直接访问实例成员

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659538828-dbbaf23f-f4b4-47cc-af91-d97ef8eb6c4e.png)

调用实例方法需要对象调用，静态方法没有对象，所以无法调用实例方法；

调用静态方法只需要用类名调用，所以实例方法可以调用静态方法。

```java
public void print() {
    run();//用对象才能调实例方法，那print()方法哪来的对象
}
public void run() {}

//实际是
public void print(Person this) {
    this.run();
    //默认藏了一个 this
}
```

那实例方法 print() 哪来的对象？

```java
Person p = new Person();
p.print();
```

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659538109-d78a2b55-05ab-4a35-a536-cafd7f094c73.png)



- 实例方法既可以直接访问实例成员，也可以直接访问静态成员

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659537972-855aef9f-b1f9-4799-a5ee-e8405ae34fd9.png)



- 实例方法可出现this关键字，静态方法不可以出现this关键字

this = 当前正在调用这个方法的对象，调用静态方法是用类名调用，不是用对象调用，所以没有 this，

调用实例方法才需要对象。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659538223-abf29973-97bd-4ffc-b9fc-c8088e1baaa7.png)



#### **综合项目**

##### **文字版格斗游戏**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659727707-94c20712-eb38-4f2e-8f1c-ce8d8f54ddd6.png)

```java
public static void main(String[] args) {
    Character c1 = new Character("张山", 100, '男');
    Character c2 = new Character("李石", 100, '女');
    c1.show();
    c2.show();
    while (true) {
        c1.hit(c2);
        if (c2.getBlood()==0){
            System.out.println(c1.getName()+"K.O了"+c2.getName());
            break;
        }
        c2.hit(c1);
        if (c1.getBlood()==0){
            System.out.println(c2.getName()+"K.O了"+c1.getName());
            break;
        }
    }
}
public class Character {
    private String name;
    private int blood;
    private char gender;
    private String face;
    String[] boyFaces= {"风流俊雅","气宇轩昂","相貌英俊","五官端正","相貌平平","一塌糊涂","面目狰狞"};
    String[] girlFaces ={"美奂绝伦","沉鱼落雁","婷婷玉立","身材娇好","相貌平平","相貌简陋","惨不忍睹"};
    //attack 攻击描述：
    String[] attacks_desc={
            "%s使出了一招【背心钉】，转到对方的身后，一掌向%s背心的灵台穴拍去。",
            "%s使出了一招【游空探爪】，飞起身形自半空中变掌为抓锁向%s。",
            "%s大喝一声，身形下伏，一招【劈雷坠地】，捶向%s双腿。",
            "%s运气于掌，一瞬间掌心变得血红，一式【掌心雷】，推向%s。",
            "%s阴手翻起阳手跟进，一招【没遮拦】，结结实实的捶向%s。",
            "%s上步抢身，招中套招，一招【劈挂连环】，连环攻向%s。"
    };
    //injured 受伤描述：
    String[] injureds_desc={
            "结果%s退了半步，毫发无损",
            "结果给%s造成一处瘀伤",
            "结果一击命中，%s痛得弯下腰",
            "结果%s痛苦地闷哼了一声，显然受了点内伤",
            "结果%s摇摇晃晃，一跤摔倒在地",
            "结果%s脸色一下变得惨白，连退了好几步",
            "结果『轰』的一声，%s口中鲜血狂喷而出",
            "结果%s一声惨叫，像滩软泥般塌了下去"
    };
    public String setFace(char gender) {
        if (this.getGender()=='男'){
            int i = (int)(Math.random()*boyFaces.length);
            return boyFaces[i];
        }else if (this.getGender()=='女'){
            int i = (int)(Math.random()*girlFaces.length);
            return girlFaces[i];
        }else return "路人甲";
    }

    public void show(){
        System.out.println("姓名："+name);
        System.out.println("血量："+blood);
        System.out.println("性别："+gender);
        System.out.println("容貌："+face);
    }

    public Character(String name, int blood, char gender) {
        this.name = name;
        this.blood = blood;
        this.gender = gender;
        this.face = setFace(gender);
    }

    public void hit(Character c){
        int attack = (int)(Math.random()*45+1);
        int i = (int)(Math.random()*attacks_desc.length);
        if (c.getBlood() - attack<0){
            c.setBlood(0);
        }else c.setBlood(c.getBlood()-attack);
        System.out.printf(attacks_desc[i] + "造成了" + attack + "点伤害，" + c.getName() + "还剩下" + c.getBlood() + "点血。",this.getName(),c.getName());
        System.out.println();
        if (attack<=5){
            System.out.printf(injureds_desc[0],c.getName());
        } else if (attack<=10) {
            System.out.printf(injureds_desc[1],c.getName());
        } else if (attack<=20) {
            System.out.printf(injureds_desc[2],c.getName());
        } else if (attack<=25) {
            System.out.printf(injureds_desc[3],c.getName());
        } else if (attack<=30) {
            System.out.printf(injureds_desc[4],c.getName());
        } else if (attack<=35) {
            System.out.printf(injureds_desc[5],c.getName());
        } else if (attack<=40) {
            System.out.printf(injureds_desc[6],c.getName());
        } else if (attack<=45) {
            System.out.printf(injureds_desc[7],c.getName());
        }
        System.out.println();
    }
    //其余get/set方法不展示
}
```

##### **电影信息展示**

展示每部电影名称、价格

用户查电影编号能搜出相关电影

(1,"功夫熊猫",48); (2,"泰坦尼克号",58); (3,"哪吒",66); (4,"阿凡达",88);

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659726741-edcadcdf-a724-4c94-904a-2087bf8b6302.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659726909-3041178f-28ed-40ce-9793-4ec1f839761d.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659727302-151893fa-7745-4fa8-b67b-5e8a611b10cb.png)

#### **继承**

##### **认识继承**

作用：可以把多个子类中重复的代码抽取到父类中，子类可以直接使用，减少代码冗余，提高代码的复用性。

- Java中提供了一个关键字**extends**，用这个关键字，可以让一个类和另一个类建立起父子关系。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659727090-90ab8dc3-e9f2-43e8-9a03-d7dc872c782d.png)

- 子类能继承父类什么

因为构造方法要和自己的类名一样，所以不能继承父类的。

父类成员变量私有，子类可以通过get/set方法获取。

父类 static、final 方法：能被继承，但不能被重写。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659728388-e17e1d7e-2669-4a6b-aaae-1c5dddc7d19a.png)

- 继承后子类的对象是由子类、父类共同完成的。

eg：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659728624-02582b5e-2b28-4462-b963-7c51c441902a.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659729608-08d2a298-e81e-4b5b-a66b-afda33a9e9c4.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659729932-3498caad-80f7-4685-94b9-d59a7982e1c6.png)



##### **修饰符**

就是用来限制类中的成员（成员变量、成员方法、构造器）能够被访问的范围。

private < 缺省 < protected < public

成员变量一般用private，成员方法一般用public，其余看实际需求决定

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659730353-9f35c297-9c5b-4a9c-a36d-867f763db6d8.png)

读到不同类私有成员变量的方法：getter、调用与私有变量同类的方法、反射

- 题目：执行结果是什么

```java
package com.example;

class TestClass {
    private static int privateCount = 0;

    public void showCounts() {
        System.out.println("Private: " + privateCount);
    }
}

public class Main {
    public static void main(String[] args) {
        TestClass test = new TestClass();
        test.showCounts();
    }
}
```



- 结果：0



##### **继承的特点**

- 单继承模式：一个类只能继承一个直接父类

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659729987-191e443f-5b84-4f8e-b104-282826ea2783.png)

- 多层继承：Java不支持多继承(多个爸爸)，但支持多层继承(爸爸继承爷爷)
- 祖宗类：Java中所有的类都是Object类的子类
- 就近原则（找变量）：优先访问自己类中，自己类中的没有才会访问父类

先在子类**局部**范围找，再去子类**成员**范围找，然后**父类成员**范围找，如果父类范围还没有找到则报错。

如果子父类中，出现了重名的成员，会优先使用子类的，如果此时一定要在子类中使用父类的怎么办？

可以通过super关键字，指定访问父类的成员：

super.父类成员变量/父类成员方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659730234-ff6daea6-25db-4abb-8f5d-81307fa12dcd.png)

- 虚方法表（找方法）：

默认不加 private /static/final 的方法，都是虚方法，因为它们可以被重写，可以有多态。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659731365-3b364f2b-24c6-4cca-9714-083a697c5955.png)

如果子类重写某方法，则会用重写后的方法覆盖自己虚方法表中原先父类的方法。

看对象实际类型 → new A()；去 A 的虚方法表里找 method2()。

看对象实际类型 → new B()；去 B 的虚方法表里找 method2()。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659731825-5f1a79a4-af60-4a62-be10-fa8acc2f9a78.png)

##### **方法重写**

当子类觉得父类中的某个方法不好用，或者无法满足自己的需求时，子类可以重写一个**方法名称、参数列表一样**的方法，去覆盖父类的这个方法，这就是方法重写。

重写小技巧：使用@Override注解，他可以指定Java编译器，检查我们方法重写的格式是否正确，代码可读性也会更好。

快捷写法：写完父类的run()方法，在子类中直接打run()，会自动弹出相应键

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659731229-9888ca76-0a29-439c-8ef1-92cdf434d819.png)



方法重写的其它注意事项：

1. 子类重写父类方法时，访问权限必须大于等于父类该方法的权限（public>protected>缺省)。
2. 重写的方法返回值类型，必须与被重写方法的返回值类型一样，或者范围更小。
3. private、static、final 方法不能被重写，即只有被添加到虚方法表中的方法才能被重写。

因为子类无法继承父类的私有方法；静态方法属于类，无法重写。



###### **应用场景**

注意：

- 直接输出对象，默认会调用 Object 的 toString 方法，返回对象的地址信息。
- 输出对象的地址实际上是没有什么意义的，开发中更希望输出对象时看对象的内容信息，

所以子类需要**重写** Object 的 toString 方法。

快捷写法：打toString，选择第一个

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659731462-1a41807b-ba50-4982-9d62-48b9da9f024a.png)



![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659731910-37cc6e89-5ece-452e-90ab-7b7255d334a3.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659732317-3a093d4a-df41-43ac-82a2-a3c4ee5a3e77.png)



##### **子类构造器**

- 子类中所有的构造方法默认先执行父类中的无参构造，再执行自己的构造器

因为子类在初始化的时候，有可能会使用到父类中的数据，如果父类没有完成初始化，子类将无法使用父类的数据。

所以子类初始化之前，一定要调用父类构造方法先完成父类数据空间的初始化。

- 怎么调用父类构造方法的？

默认情况下，子类全部构造器的第一行代码都是 super()，不写也存在，且必须在第一行。

如果想调用父类有参构造，必须手动写 super(...) 进行调用。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659732488-d2f57160-fd2b-4a29-939b-1e9cf24eff26.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659732776-b68509e9-fdd1-4501-8dca-9501b401101c.png)

###### **应用场景**

完成父类构造器继承属性的初始化**赋值**，同时完成自身特有属性的初始化**赋值**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659733520-60fc98c5-6dcf-459a-a76d-4bfd5919d032.png)

this()用于在同一个类的构造器中调用该类的其他构造器（这些被调用的构造器可称为 “兄弟构造器”）。

应用：给数据做默认值时会用到

this()的使用限制：

1. 位置限制：this() 必须是构造器的第一行代码（和 super() 规则类似，两者不能同时出现，因为第一行只能有一个）；

1. 1. JVM执行顺序：①分配堆内存 → ②给成员变量赋默认初始值 → ③执行构造函数业务代码。
   2. 对象的初始化动作要优先全部做完，才能执行当前构造函数里自己的业务代码。

1. 参数匹配：this(...) 的参数列表（数量、类型、顺序）必须严格匹配当前类中某个已存在的构造器；
2. 避免循环：不能出现 “构造器 A 调用构造器 B，构造器 B 又调用构造器 A” 的循环调用，否则会编译报错。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659733450-d08de037-04de-4471-8c34-0943f5331af9.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659733925-4d40484c-e4f5-464f-bb4e-34de6e69d5ae.png)

```java
@AllArgsConstructor
@NoArgsConstructor
@Data
public class Animal {
    private int age;
    private String color;
}

@AllArgsConstructor
@NoArgsConstructor
@Data
public class Dog extends Animal{}
//为什么创建狗对象不能用全参构造器创建对象
//Animal 有字段：age、color
//Dog 自己没有写任何字段
//@AllArgsConstructor 只给当前类自己的字段生成构造方法，不会把父类字段算进去。
//所以 new Dog(3, "白色") 会报错
```



#### **多态**

##### **认识多态**

多态是在**继承/实现**情况下的一种现象，表现为：对象多态、行为多态。

多态的前提：有继承/实现关系；存在父类变量指向子类对象；存在方法重写。

为了多个子类调用同一个方法，会自动执行子类自己的实现。

方法调用：编译看左边，运行看右边。

javac 编译代码的时候，会看左边的父类中有没有这个方法，如果有编译成功，如果没有编译失败。

java 运行代码的时候，实际上运行的是子类重写的方法。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659902873-f13d7dd8-412f-4cf3-8800-e935301691ce.png)

变量调用：编译看左边，运行也看左边。

多态的一个注意事项：多态是**对象、行为**的多态，Java中的属性（**成员变量**）不谈多态。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659902833-0f0e8b67-aa3b-4cfa-8836-a71475f4472c.png)



##### **多态的好处与问题**

好处：

- 在多态形式下，右边对象解耦合的，更便于扩展和维护。

简单说，多态的解耦合就是：让 "指挥的" 和 "干活的" 分离开，两边可以各自变化，互不干扰。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659902535-fdb00341-a2e7-43d0-b81c-0056be39988e.png)

把baby换成elder照样会运行

- 定义方法时，使用父类类型的形参，可以接收一切子类对象，扩展性更强、更便利。

代码中把go方法声明为static，是为了在main方法中可以直接调用它，省去创建test对象的步骤，让代码更简洁

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659902775-1a714488-6469-4745-8ead-c2efb5aec6fb.png)

问题：

- 多态下不能使用子类的独有功能

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659902778-f8c01f1c-c6e0-4414-ac50-375f8f330858.png)

##### **多态的类型转换**

1. 类型转换的形式

自动类型转换：父类变量名=new子类();

强制类型转换：子类变量名=(子类)父类变量;

作用：可以把对象转换成其真正的类型，从而解决了多态下不能调用子类独有方法的问题。

- 不是将p1转换为baby类型了吗，为什么不能用p1.cry() ？

因为强制类型转换并没有改变原变量（p1）的声明类型，只是创建了一个新的子类类型变量（b）来**指向同一个对象**。因此，必须通过新的子类变量（b）才能调用子类的独有方法cry()。

p1 的声明类型 → 还是 people（没变！），只是它指向的对象是 baby

b 的声明类型 → 是 baby，它和 p1 指向同一个 baby 对象

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659903485-c7ff0890-5e36-4ed3-b140-dc0fd05bd04c.png)

类如局部变量的强制类型转换：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659903974-b39b1cfe-3b22-4e00-af2d-a9705f075e76.png)

2. 注意：

存在继承/实现时，就可以进行强制类型转换，编译阶段不会报错。

但在运行时，如果发现对象的真实类型与强转后的类型不同会报错(ClassCastException)。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659904155-8159513e-d6bb-48a9-b45c-1682ebd0240c.png)

3. 强制类型转换前

使用instanceof关键字，判断当前对象的真实类型

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659904455-779ea967-c92a-44d5-ad0b-c2cf4c96b9e1.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659904605-e3af2563-e517-4d1d-8e7d-fc605689c30f.png)

```java
public void keepPet(Animal animal,String sth){
    //如果不用多态，就要写
    //keepPet(Dog dog,String something)
    //keepPet(Cat cat,String something)
    if (animal instanceof Dog dog){
        System.out.println("年龄为"+age+"岁的"+name+"养了一只"+dog.getColor()
                +"的"+dog.getAge()+"的狗");
        dog.eat(sth);
    }
    if (animal instanceof Cat cat){
        System.out.println("年龄为"+age+"岁的"+name+"养了一只"+cat.getColor()
                +"的"+cat.getAge()+"的猫");
        cat.eat(sth);
    }
}
```



#### **final关键字**

##### **认识**

final关键字是最终的意思，可以修饰：类、方法、变量。

- 修饰类：该类被称为最终类，特点是不能被继承了。

util工具类一般用final修饰

```java
public final class Utils { // final类不会被继承
    private Utils() {} // 构造器私有化，不会被实例化

    public static void print(){
        // 静态方法直接被类名调用，不用创建对象
        // 实例方法需要创建对象调用，此时对象只是为了调用方法，而对象会占内存
        System.out.println(666);
    }
}
```

- 修饰方法：该方法被称为最终方法，特点是不能被重写了。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659904565-ab645460-5f72-459f-8a69-5133d5ff20bf.png)

- 修饰变量：该变量有且仅能被赋值一次。

1. final修饰局部变量：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659905040-e9b950a0-cfc9-4710-95d8-5f856c64e6fb.png)

2. final修饰静态变量：

这个变量今后被称为常量，可以记住一个固定值，并且程序中不能修改了，通常这个值作为系统的配置信息

单个单词：全部大写；

多个单词：全部大写，单词之间用下划线隔开。

```java
static final int NUM = 100;
```

3. final修饰实例变量一般无意义

- final修饰变量的注意：

final 修饰基本类型的变量，变量存储的数据不能被改变。

final 修饰引用类型的变量，变量存储的地址不能被改变，但地址所指向对象的内容是可以被改变的。

```java
//修饰自定义对象
final Dog dog = new Dog();
dog.setAge(3);
dog.setAge(4);
dog.setAge(5);
System.out.println(dog.getAge());
//修饰数组
final int[] arr = {1,2,3};
arr[0] = 3;
//修饰集合
final List<String> list = new ArrayList<>();
list = new ArrayList<>(); // ❌ 报错！不能换地址
list.add("hello")//可以改内容
```

虽然字符串是引用类型，但字符串的内容是不可变的

```java
//String源码
private final byte[] value;
//final 修饰数组，则数组的地址不能变
//private 修饰数组，外面的类不能访问此数组，所以无法修改里面的值
```



##### **常量**

使用了 static final 修饰的成员变量被称为常量

作用：常用于记录系统的配置信息。

常量的名称，**全部大写**，多个单词用**下划线连接**

一般会新建 constant 包，把常量都存在里面，需要时直接找包

常量类与工具类一样，用 final 修饰，构造器私有化。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659906298-44360556-3f8a-4942-a1f2-07314150f598.png)

#### **适配器设计模式**

当一个接口中抽象方法过多，但只要使用其中一部分的时候，就可以适配器设计模式。

书写步骤：

编写中间类XXXAdapter，实现对应的接口；

对接口中的抽象方法进行空实现；

让真正的实现类继承中间类，并重写需要用的方法；

为了避免其他类创建适配器类的对象，中间的适配器类用 abstract 进行修饰。

如果直接写实现类，就要实现所有的方法，但只需要重写方法五，其他方法用不到。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659905942-44d44d55-2d1e-49f3-8da6-f3d6baf82280.png)

可以让中间类去实现接口，对接口里所有的方法进行空实现

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659905963-a5f39459-6695-48e9-b81c-5c3398bb7db6.png)

然后让实现类继承中间类

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659906111-91d88ac7-034a-4b83-bfdc-a9a9fa83d2cd.png)

#### **单例类(设计模式)**

架构师学的

一个问题通常有种解法，其中肯定有一种解法是**最优的**，这个最优的解法被人总结出来了，称之为**设计模式**。

设计模式有20多种，对应20多种软件开发中会遇到的问题。

- 目的：让某个类在整个程序中只能创建出**一个对象**

举个简单的例子：比如你做一个游戏，需要一个 "游戏设置" 类来保存音量、画质等参数。这个类就适合做成单例，因为：

游戏里只需要一套设置；

无论在哪个界面修改设置，其他地方都能看到最新的设置。

- 好处：

1. 节省内存（不会重复创建相同的对象）
2. 保证数据一致性（所有地方操作的都是同一个对象）
3. 方便全局访问（不用到处传递对象）

- 写法：

1. 把构造器设为**私有**，阻止外部随便创建对象。

私有构造器：只有这个类自己内部的代码可以调用构造器创建对象，其他类（包括子类）都不能直接调用这个构造器创建对象

1. 自己在类内部创建一个**唯一**的对象，一般会用**静态变量。**
2. 提供一个公开的方法让外部获取这个唯一实例，一般用**静态方法**，因为静态方法的特点是：不需要创建对象，直接通过类名就能调用。如果getInstance()不是静态方法，那我们得先创建一个对象才能调用它，但单例类的构造器是私有的，我们根本无法在外部创建对象。

饿汉式单例类：类一加载到内存就立刻创建唯一实例，早早就准备好了。

启动时就占用内存，不管后续用不用。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659906023-ff7a094d-ca47-474a-accd-95fee6d4da66.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659907319-fd8d08ab-e30b-442f-b630-24d2417efed8.png)

懒汉式：直到第一次调用getInstance()方法时，才会创建实例，用时再准备。

不用时不占内存，节省资源。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659907323-fa83f469-1b69-4088-971e-e191b4545c05.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794659907379-eae54c98-a29a-45f1-890a-ff5ab4eb1d83.png)



#### **枚举类**

专门用来定义**固定数量的常量集合。**

替代常量类（更规范）：以前可能用public static final定义一组常量，现在用枚举更清晰。

举个生活中的例子：

一周只有 7 天（周一到周日）；性别只有男、女；季节只有春、夏、秋、冬

特点：

1. 枚举类中的第一行，只能写枚举类的对象名称，且要用逗号隔开，每个常量会记住枚举类的一个对象。
2. 枚举都是最终类，不可以被继承，枚举类都是继承java.lang.Enum类的。
3. 枚举类的构造器都是私有的（写不写都只能是私有的），因此，枚举类对外不能创建对象。

父类 Enum 重写了 toString()方法，返回枚举常量的名称

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660300473-d1c97c55-efeb-4f9f-b984-04c44f58b708.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660300851-6c894378-3e17-4047-9715-f1dde9c9e7b3.png)

- 应用场景

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660300912-2b9f2989-1e10-4633-8650-c8cb73c19054.png)



#### **抽象类**

简单来说，它就像是一个 "模板" 或 "蓝图"，规定了子类必须实现哪些方法，但它自己不能被直接用来创建对象。

抽象类的注意事项：

1. 抽象类中不一定要有抽象方法，有抽象方法的类必须是抽象类。
2. 抽象类可以有普通方法。
3. 抽象类中的成员：成员变量、方法、构造器

抽象类不能创建对象，那构造方法有什么用？可以给子类创建对象。

抽象类最主要的特点：

1. 抽象类**不能创建对象**，仅作为一种特殊的父类，让子类继承并实现。
2. 一个类继承抽象类，必须重写完抽象类的**全部抽象方法**，否则这个类也必须定义成抽象类。

抽象方法：只有方法声明，没有方法体

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660300827-a35bdcd3-b454-42f5-87bf-238a0dc97074.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660301489-b6b1914c-acde-41e7-8979-e2ebcb29a315.png)

抽象类的好处：

父类知道每个子类都要做某个行为，但每个子类要做的情况不一样，父类就定义成抽象方法，

交给子类去重写实现我们设计这样的抽象类，就是为了更好的支持**多态**。

抽象类模板方法设计模式：

- 提供一个方法作为完成某类功能的模板，模板方法封装了每个实现步骤，但允许子类提供特定步骤的实现。
- 模板方法设计模式可以：提高代码的复用、并简化子类设计。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660302236-59fabf01-68ce-4921-8700-a351e7ffb0cc.png)

解决：抽出一个父类，父类中还抽取一个模板方法给子类直接用

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660302373-ac4ed5a5-40ae-4545-9bf9-cf689c1c7a5c.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660302189-a4fa2115-1129-4271-a1c4-787071a321b6.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660302812-06d3bea6-b6b0-47b6-acaa-f67c245a1777.png)

#### **接口**

##### **介绍**

注意：Java 命名规范中接口名通常**首字母大写**

JDK8之前，接口中只能定义**常量**和**抽象方法**。

接口中定义的变量默认是常量，即被 public static final 修饰，必须在声明时初始化，且不能被修改。

接口中定义的方法默认是**抽象方法**，即被 public abstract 修饰，**只有方法声明，没有方法实现**。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660302905-1ac1d995-9d89-4565-a292-5b055126c82e.png)

- 注意：接口不能创建对象

接口是用来被类**实现**(implements)的，实现接口的类称为实现类，一个类可以同时**实现多个接口**。

实现类实现接口，需要重写接口的全部抽象方法，否则此类必须定义成抽象类

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660303265-937456c3-0556-4d2f-9503-27fec449d83b.png)

有实现类后，可以创建实现类的变量，用此变量去调接口的方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660303435-18e0e185-b421-43c5-accf-c8e02dfe681b.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660303642-458d28ce-9fb7-44bc-8437-5c75fba3690e.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660303952-ec3e2ecd-255b-4bef-a2cf-e1543dbab803.png)

接口的好处：

- 弥补了类单继承的不足，一个类同时可以实现**多个接口**，使类的角色更多，功能更强大。
- 让程序可以面向接口编程，这样程序员就可以灵活方便的切换各种业务实现（更利于程序的**解耦合**），一个接口可换不同的实现类

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660304742-c4aa2d51-2b36-4465-a760-0b3e49de4710.png)

- 类和类的关系

继承关系，只能单继承，不能多继承，但是可以多层继承

- 类和接口的关系

实现关系，可以单实现，也可以多实现，还可以在继承一个类的同时实现多个接口

- 接口和接口的关系

继承关系，可以单继承，也可以多继承



##### **JDK8的新特性**

接口中可以定义有方法体的方法。（默认、静态)

允许在接口中定义默认方法，需要使用关键字 default 修饰

作用：解决接口升级的问题

默认方法：

- 接口中默认方法的定义格式

范例：public default void show(){}

- 接口中默认方法的注意事项

默认方法不是抽象方法，所以不强制被重写。但是如果被重写，重写的时候去掉default关键字；

public可以省略，default不能省略；

如果实现了多个接口，多个接口中存在相同名字的默认方法，子类就必须对该方法进行重写。

静态方法：

- 接口中静态方法的定义格式

范例：public static void show(){}

- 接口中静态方法的注意事项

静态方法只能通过接口名调用，不能通过实现类名或者对象名调用；

静态方法不会被子类重写，因为静态方法不在虚方法表中；

public可以省略，static不能省略。



##### **JDK9的新特性**

接口中可以定义私有方法。

作用：在多个 default 方法中的重复代码，可以抽取到 private 方法中，此方法只为接口提供服务，不需要外类访问。

普通私有方法为默认方法服务；静态私有方法为静态方法服务。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660305055-56d5f00f-fa41-4f84-ba46-023247f55954.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660305311-3697f489-9085-4875-a9ea-ee62a13e3625.png)



##### **综合案例**

设计一个班级学生的信息管理模块，学生的数据有：姓名、性别、成绩

功能1：要求打印出全班学生的信息；功能2：要求打印出全班学生的平均成绩。

以上功能的业务实现是有多套方案的，比如：

第1套方案：能打印出班级全部学生的信息；能打印班级全部学生的平均分。

第2套方案：能打印出班级全部学生的信息（**包含男女人数**）；能打印班级全部学生的平均分（**要求是去掉最高分、最低分**）。

要求：系统可以支持灵活的切换这些实现方案。

接口类：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660304731-6125da4e-545c-4325-bebf-c87856dc90f9.png)

主程序：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660305563-a656ca88-85ff-4d0e-91cc-38c18758e0c5.png)

实现类1：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660306234-4196505f-6ec9-448f-aab2-dbcb95f148a3.png)

实现类2：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660306558-1541a142-e258-464e-89dc-2719af353e4e.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660306728-d440d1b7-c507-415e-a02d-cc7b77446785.png)



##### **抽象类和接口的区别**

相同点：

1、多是抽象形式，都可以有抽象方法，都不能创建对象。

2、都是派生子类形式：抽象类是被子类继承使用，接口是被实现类实现。

3、一个类继承抽象类，或者实现接口，都必须重写完他们的抽象方法，否则自己要成为抽象类或者报错！

4、都能支持的多态，都能够实现解耦合。

不同点：

I、抽象类中可以定义类的全部普通成员，接口只能定义常量，抽象方法(JDK8新增的三种方式)

2、抽象类只能被类单继承，接口可以被类多实现。

3、一个类继承抽象类就不能再继承其他类，一个类实现了接口（还可以继承其他类或者实现其他接口）。

4、抽象类体现模板思想：更利于做父类，实现代码的**复用性，复用性**就是避免 “重复编写相同逻辑”，例如抽象类可以定义成员变量（如属性），子类继承后可以直接使用这些变量，避免每个子类重复定义相同的属性。

5、接口更适合做功能的解耦合：**解耦合性**更强更灵活。



##### **综合案例**

某智能家居系统，可以让用户选择要控制的家用设备（吊灯，电视机，洗衣机)，并可以对它们进行**打开**或者**关闭**操作。

数据存储：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660306886-6e9b6cef-ef82-4a84-99e0-78c2c3551151.png)

主程序：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660307313-b3a5e4b7-9432-48cd-871d-2a554a5b1536.png)

子类：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660307862-b31e388a-11dd-4ba4-ae61-665a066ad8cc.png)

接口：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660307913-ddeb189a-d359-4257-b53c-797820af43f7.png)

功能控制：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660308544-511afdda-3a92-481b-8a57-2463b4a2f2f3.png)



#### **代码块**

##### **局部代码块**

没啥用，已淘汰。

在方法内部的代码块

作用：提前结束变量生命周期

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660415523-b5dbae68-1a15-40a3-bc18-36d0472e20fe.png)



##### **静态代码块**

重点

格式：static{}

特点：有static修饰，属于类，与**类一起优先**加载，自动执行一次

作用：完成类的**初始化**，例如对**静态变量**的初始化**赋值**。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660415681-f57a8042-04de-4edc-a037-38fc60329cdd.png)

对静态变量的初始化赋值

直接打印数组时，得到的不是数组里的具体元素，而是一串看不懂的 “内存地址”（比如 [I@1b6d3586）。

而 Arrays.toString(arr) 能帮我们把数组里的元素 “整齐地” 变成字符串

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660415735-1d94b0b1-93ca-459d-8e5c-0b725efecb70.png)

可以初始化数据，这样测试其他功能不需要每次都注册一个账号或添加数据

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660416019-84f3fcab-b1ee-490e-82c1-452bf95c58f8.png)



##### **构造代码块**

也称实例代码块

格式：{}

特点：每次**创建对象**时自动执行，且在**构造器前执行**。

作用：可以把多个构造方法中重复的代码抽取出来，避免在多个构造方法里重复写同样的代码。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660416045-0c617476-17a5-4f40-be4f-6c035f8c0620.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660417220-cb41dd38-ab26-45d1-bb59-57d6d6e6aa17.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660417202-d3e94c64-452b-4474-bbb5-00ba5fd6a9fe.png)



#### **内部类**

##### **介绍**

什么时候用到内部类？

B类表示的事物是A类的一部分，且B单独存在没有意义。

比如：汽车的发动机，ArrayList的迭代器，人的心脏等等

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660417265-13b442e5-c995-4db0-ba71-5cbf5a3025f8.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660417475-5804cbcb-3d39-4438-929d-18f5c5ae8325.png)

##### **成员内部类**

定义在另一个类的内部，且没有 static 关键字修饰的类，因为无 static 修饰，所以属于外部类的对象持有的，

它就像一个类的 “成员变量” 一样，依赖于外部类的**对象**而存在。

成员内部类可以被一些修饰符所修饰，比如：private，默认，protected，public

成员内部类创建对象的格式：

外部类名称.内部类名称 对象名 = new外部类名称().new内部类名称();

内部类可以**直接访问**外部类的所有成员，包括 private 修饰的变量和方法；

外部类要访问内部类的成员，必须创建对象。

```java
new Outer().new Inner();//因为内部类相当于外部类的成员变量，所以与下面代码类似
new Outer().name;
```

如果内部类私有化，就外部类编写方法，提供内部类对象

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660417573-8c337790-bb19-4d80-877d-582c7937eb49.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660418719-3d5c6571-ad1e-4e93-8748-7b5f2e708564.png)

- 在局部内部类中 this 的用法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660418805-d989d5d9-0aa3-4c48-b058-ca71e736a69e.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660419694-f35a812d-e365-4103-b64e-a244f51ad08a.png)

##### **静态内部类**

有static修饰的内部类，属于外部类自己持有。

静态内部类创建对象的格式：

外部类名.内部类名 对象名 = new 外部类名.内部类名(…)；

静态内部类只能访问外部类的**静态成员**（静态变量、静态方法），不能直接访问外部类的非静态成员，

想要访问非静态的需要创建变量。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660418823-528a266c-ce90-46b1-be06-93cd1fa4f19b.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660418756-6fd1e4ac-0f00-4f69-8cab-fa68217681d7.png)



##### **局部内部类(没用)**

局部内部类是定义在在方法中、代码块中、构造器等执行体中。



##### **匿名内部类(重点)**

它可以临时创建一个没有名字的实现类，不需要再另外创一个实现类

特点：匿名内部类本质就是一个**子类**，并会立即创建出一个**子类对象**。

接口不用匿名内部类的麻烦写法：需要单独创一个实现类。

如果实现类只要使用一次，就可以用匿名内部类简化代码。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660419980-53de1634-c23f-43e3-95b1-9fba1b211a37.png)

使用匿名内部类：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660420156-9fdc38cd-140d-492c-858e-e9b1d7550abe.png)

类不用匿名内部类的麻烦写法：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660420458-213c6c6d-6984-4092-9d44-1e54b378a8c3.png)

类用匿名内部类：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660421063-8aad4bf5-b059-4072-b202-697f77c0b3b6.png)

匿名内部类在开发中的常见形式：通常作为一个对象参数传输给方法

不用匿名内部类的麻烦写法：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660421465-d52d7962-a770-4cd3-8644-9c7add072236.png)

用匿名内部类：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660421653-8fbd35ce-766b-489c-802a-25379c1e59cb.png)

一般调用别人的接口或类的时候，才会用匿名内部类

###### **使用场景1：按钮绑定点击事件**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660421763-cd7b4cf0-4548-4443-a3af-6b795276a197.png)

###### **使用场景2：给数组排序**

Java 的 Arrays.sort() 方法支持传入一个 “比较器”（Comparator 接口）来指定排序规则，

而匿名内部类可以直接作为这个**比较器的实现。**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660422210-b2e09925-e7e1-427b-ae32-993107b71f2b.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660422564-b489c5fd-4e48-4756-a18c-c7c511333559.png)



#### **函数式编程**

##### **lambda**

###### **认识**

数学中的函数示例：2x+1

Java中的函数(Lambda表达式)：(x) -> 2x+1

使用Lambda函数替代某些匿名内部类对象，从而让程序代码更简洁，可读性更好。

格式：(参数) -> { 方法体 }

注意：Lambda表达式只能替代函数式接口的匿名内部类！！

函数式接口：有且仅有**一个抽象方法**的**接口**

注意：将来我们见到的大部分函数式接口，上面都可能会有一个@Functionallnterface的注解，该注解用于约束当前接口必须是函数式接口。

```java
@FunctionalInterface
public interface MyInterface {
    void run();  //抽象方法只能有一个
    //函数式接口允许有很多个非抽象方法：
    default void eat(){}
    static void sleep(){}
    private void drink(){}
}
```



###### **lambda的省略规则**

具体规则：

1. 参数类型全部可以省略不写。
2. 如果只有一个参数，参数类型省略的同时“()”也可以省略，但多个参数不能省略“()”
3. 如果Lambda表达式中只有一行代码，花括号可以不写，同时要省略分号。如果这行代码是 return 语句，也必须去掉 return。

```java
Integer[] arr3 = {1, 5, 2, 3, 4};
//原先的代码
Arrays.sort(arr3, new Comparator<>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        //参数一o1：表示在无序序列中，遍历得到的每一个元素
        //参数二o2：有序序列中的元素
        //简单理解：o1-o2:升序排列：o2-o1:降序排序
        return o2 - o1;
    }
});

//简化后的代码，把new和方法名删去，再加上箭头
Arrays.sort(arr3, (Integer o1, Integer o2) -> {
            return o2 - o1;
        }
);

//更简化的代码
Arrays.sort(arr3, (o1, o2) -> o2 - o1);
```

###### **综合训练**

定义数组并存储一些人对象，利用 Arrays 中的 sort 方法进行排序

要求1：属性有姓名、年龄、身高。

要求2：按照年龄的大小进行排序，年龄一样，按照身高排序，身高一样按照姓名的字母进行排序。

(姓名中不要有中文或特殊字符，会涉及到后面的知识)

```java
Person p1 = new Person("jack", 22, 180.5);
Person p2 = new Person("tom", 22, 175);
Person p3 = new Person("mike", 22, 175);
Person p4 = new Person("rose", 18, 160.5);
Person p5 = new Person("jake", 20, 170);
Person[] people = {p1, p2, p3, p4, p5};
Arrays.sort(people, ((o1, o2) -> {
    int age = o1.getAge() - o2.getAge();
    double height = o1.getHeight() - o2.getHeight();
    int name = o1.getName().length() - o2.getName().length();
    if (age!=0) return age;
    if (height>0) {
        //防止0.1强制转化int为0，就不是正数或负数
        return 1;
    }else if (height<0){
        return -1;
    }
    return name;
}));
```



##### **方法引用**

- 概念

把已有的方法拿来用，当作函数式接口中抽象方法的方法体

- 条件

1. 引用处必须是函数式接口
2. 被引用的方法需要已经存在
3. 被引用方法的形参和返回值需要跟抽象方法的形参和返回值保持一致

```java
public class Test {
    public static void main(String[] args) {
        //eg：数组倒序
        Integer[] arr = new Integer[]{3, 1, 2, 5, 4, 6};
        //lambda
        Arrays.sort(arr, (o1, o2) -> o2 - o1);
        //方法引用
        Arrays.sort(arr, Test::subtraction1);//静态方法
        Arrays.sort(arr, new Test()::subtraction2);//实例方法
        System.out.println(Arrays.toString(arr));
    }

    public static int subtraction1(int n1, int n2) {
        return n2 - n1;
    }
    public int subtraction2(int n1, int n2) {
        return n2 - n1;
    }
}
```

###### **静态方法引用**

:: 是 Java 的方法引用符号，意思是直接把元素传给这个方法用

条件：lambda 里只有一行代码；代码只是把参数直接传给一个方法，没有任何计算

格式：类名::静态方法

使用场景：如果某个Lambda表达式里只调用一个静态方法，并且“→”前后参数的形式一致，就可以使用静态方法引用。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660423020-1f0313e0-8dc1-4826-9e62-1b526c79c929.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660423287-f9f9adf3-426e-4ae3-beef-703b81e16e26.png)

###### **实例方法引用**

格式：对象名::实例方法

本类：this::方法名

父类：super::方法名

静态方法中没有 this，注意 this 和 super 引用处不能是静态方法

使用场景：如果某个Lambda表达式里只是通过对象名称调用一个实例方法，

并且“→”前后参数的形式一致，就可以使用实例方法引用。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660423411-5b34e9e8-17d8-49b6-b5d8-c8ff8519025e.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660423859-621f866f-2af5-4c06-b3cd-235f30a8718e.png)

```java
ArrayList<Integer> list = new ArrayList<>();
list.forEach(i -> System.out.println(i));

// out 是 System 类的一个 **静态成员变量**
PrintStream out = System.out;
// 调用的是 out 对象的 println() 方法
out.println(元素);
//System.out是一个对象（PrintStream 实例）
//println是这个对象里的实例方法，所以是实例方法引用
```

注意：在项目中不能使用System.out.println()，首先System.out.println只是将信息输出到控制台，使用logger日志是可以输出到指定的文件中，方便后续查看，还有更严重的问题就是将信息直接输出到控制台会导致内存占用增大，甚至溢出，项目崩溃等问题。

###### **特定类型**

特定类的名称::方法

使用场景：如果某个Lambda表达式里只是调用一个特定类型的实例方法，并且前面参数列表中的第一个参数是作为方

法的主调，后面的所有参数都是作为该实例方法的入参的，则此时就可以使用特定类型的方法引用。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660424320-76c5d6f1-709b-4f69-8192-bac28bd14a61.png)

###### **构造器**

格式：类名::new

使用场景：如果某个Lambda表达式里只是在创建对象,并且"→"前后参数收情况一致,就可以使用构造器引用。

集合里面存储姓名和年龄，要求封装成 Person 对象并收集到 List 集合中

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660424445-4c8ee29d-534e-44a2-9d0a-6f71b3b0c5de.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660424735-0383f7b3-8958-4c86-a5ee-f31b7e7fd872.png)

```java
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list, "aaa,12", "bb,14", "c,32");
//匿名内部类
List<Person> list1 = list.stream()
        .map(new Function<String, Person>() {
            @Override
            public Person apply(String s) {
                return new Person(s.split(",")[0], Integer.parseInt(s.split(",")[1]));
            }
        }).toList();
//lambda
List<Person> list2 = list.stream()
        .map(s -> new Person(s.split(",")[0], Integer.parseInt(s.split(",")[1]))).toList();
//方法引用
List<Person> list3 = list.stream().map(Person::new).toList();
System.out.println(list3);
```

###### **类名引用成员方法**

格式：类名::方法

要求：

1. 返回值需要保持一致。
2. 被引用方法的形参，需要跟抽象方法的第二个形参到最后一个形参一样，如果没有第二个参数，说明被引用方法是无参的。

抽象方法第一个参数：表示被引用方法的调用者，决定了可以引用哪些类中的方法

在 Stream 流当中，第一个参数一般都表示流里面的每一个数据。

假设流里面的数据是字符串，那么使用这种方式进行方法引用，只能引用 String 这个类中的方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660424881-33cab2cf-b141-4671-a25c-fe792bef550c.png)

```java
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list,"aa","bbb","csd");
list.stream().map(new Function<String, String>() {
    @Override
    public String apply(String s) {
        return s.toUpperCase();
    }
});
list.stream().map(String::toUpperCase).forEach(System.out::println);
```

###### **引用数组的构造方法**

格式：数据类型::方法

```java
ArrayList<Integer> list = new ArrayList<>();
Collections.addAll(list,1,2,3,4);
Integer[] arr1 = list.stream().toArray(value -> new Integer[value]);
Integer[] arr2 = list.stream().toArray(Integer[]::new);
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list, "a,1", "bb,2", "ccc,3");
//收集到Person类型的数组中，要用map转化成Person类型
Person[] arr = list.stream().map(Person::new).toArray(Person[]::new);
```



### **API**

API（Application Programming Interface）：应用程序编程接口

简单理解：API 就是别人已经写好的东西，我们不需要自己编写，直接使用即可。

#### **String**

##### **介绍**

字符串的内容是不会发生改变的，它的对象在创建后不能被更改。

```java
String name = "jack";
//变量 name 保存 "jack" 的地址
name = "tom";
//不是修改了字符串，是换了一个对象
//变量 name 改成保存 "tom" 的地址
System.out.println(name);
```

String代表字符串，它的对象可以封装字符串数据，并提供了很多方法完成对字符串的处理

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655509375-4bcd6ecd-a92b-45ab-9774-2f1e237874c4.png)

字符串转化成字符数组：

String s = "123";

char[] chars = s.toCharArray;

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655509262-4132ba58-844c-4a9d-bd6c-68621375fc44.png)

##### **转字符串**

| 方式                | null 情况               | 非 String 对象                                               | 本质         |
| ------------------- | ----------------------- | ------------------------------------------------------------ | ------------ |
| obj.toString()      | null → NPE 空指针       | 正常调用 toString，只能对象调用                              | 调用对象方法 |
| (String) obj        | null → 结果 null        | 非 String → ClassCastException，只适合对象本身就是 String 实例，如：Object str = "hello"; | 类型强制转型 |
| String.valueOf(obj) | null → 返回字符串"null" | 调用 toString，支持基本类型和对象                            | 静态工具方法 |

##### **内存分配**

String创建对象的区别：

只要是以 String str = "xxx" 方式写出的字符串对象（使用字面量创建的字符串），会存储到字符串常量池（在堆里），且**相同内容**的字符串只**存储一份**；

当使用双引号直接赋值时，系统会检查该字符串在串池中是否存在。不存在：创建新的；存在：复用。

通过new方式创建字符串对象，每 new 一次都会产生一个**新的对象**放在堆内存中。

== 号比较的是栈里的内容，不是堆里的。

所以 s1 == s2，是因为它们都指向常量池的同一块地址。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657667806-9f1da554-2274-473a-96f2-2501d632739e.png)

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657678956-17642963-706b-4c58-a4cb-5f71e44241f0.png)

```java
String s1 = sc.next();
String s2 = "jack";
System.out.println(s1==s2);//false
//键盘输入的字符串，是 new 出来的字符串对象，不会进常量池
```

- **String提供的常用方法**

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794657737737-fc196418-85ba-4a1d-bfb4-03a6b9face6f.png)

- trim() = 去掉字符串【前面】和【后面】的所有空格

```java
String s = "  hello  world ass  ";
s = s.trim();//"hello  world ass"
```

- 字符串比较

下列哪个方法可以安全地比较两个可能为null的字符串？

A. str1.equals(str2)

B. str1 == str2

C. Objects.equals(str1, str2)

D. str1.compareTo(str2) == 0



A：不安全。如果 str1 == null，调用 .equals() 直接抛出 **NullPointerException 空指针异常**。



B：错误。== 比较的是**对象地址**，不是字符串内容。

底层源码逻辑：

```java
public static boolean equals(Object a, Object b) {
    return (a == b) || (a != null && a.equals(b));
}
```

C：会先判断 a 是否为 null，**不会空指针**。

D：不安全。str1 如果是 null，调用 compareTo() 直接空指针。



##### **案例**

- 敏感词屏蔽

```java
String s = "ascnmdesb,tmd";
String[] str = new String[]{"tmd","cnm","sb"};
for (int i = 0; i < str.length; i++) {
    s = s.replace(str[i], "***");
}
System.out.println(s);
```

- 字符转数字

```java
//字符串转数字
String s = "6";
int n1 = Integer.parseInt(s);
//字符转数字
char c = '6';
int n2 = c - '0';
```

- 获取字符串的每一个字符

```java
//方法一：charAt
String str = "ashiho";
for (int i = 0; i < str.length(); i++) {
    char c = str.charAt(i);
    System.out.println(c);
}
//方法二：charArray，转成字符数组
char[] charArray = str.toCharArray();
for (int i = 0; i < charArray.length; i++) {
    System.out.println(charArray[i]);
}
```

- 实现随机产生验证码，验证码的每位可能是数字、大写字母、小写字母。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658008231-b38b5bad-61ec-400a-9785-534eb19d23e3.png)



#### **StringBuilder**

- 普通 String 拼接

String 是不可变的，所以每次 + 都会新建一个字符串对象；

循环里拼接会极慢、浪费内存。

```java
String s = "";
s += "壹";
s += "佰";
s += "贰";
```

- StringBuilder

可变字符串，不会频繁创建新对象，速度极快。

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658147787-b7b2028d-242c-4f11-9804-17b1c455c3d5.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655512324-c5390418-ba59-4066-b176-c9c8a9bc4c2e.png)

字符串回文就可以用 reverse() 做。

- 使用StringBuilder的场景

1. 字符串的拼接
2. 字符串的反转



#### **StringJoiner**

带分隔符拼接多个字符串。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655512346-79e77174-f3e5-4b0a-bc2b-4b01b363aaf4.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655512412-6d4afcb9-ba59-4ef2-bc7f-f63cf0edb2bf.png)



#### **底层原理**

##### **字符串存储的内存原理**

字符串直接赋值会复用字符串常量池

new出来不会复用，而是开辟一个新的空间



##### **==号比较的到底是什么？**

比较栈里的东西



##### **字符串拼接的底层原理**

- 拼接时无变量参与

```java
//编译器直接优化成 "abc"
String s = "a"+"b"+"c";
```

- 拼接时有变量参与

1. JDK8 以前

\+ 拼接底层 = new StringBuilder() + append() + toString()

toString () 底层源码：return new String(value, 0, count); ，所以在堆中创建空间。

```java
//在常量池中创建 "a"
String s1 = "a";
//常量池创建"b"，new了个StringBuilder，在堆内存中new一个String对象，值是"ab"
//所以"ab"不会进常量池
String s2 = s1+"b";
//常量池创建"c"，又new了个StringBuilder，又在堆内存中new一个String对象，值是"abc"
String s3 = s2+"c";
//最后常量池有"a"、"b"、"c"，堆内存有"ab"、"abc"，一个创建了四个对象

//上述例子创建了多个StringBuilder对象，低效；
//如果手动new StringBuilder 拼接，全程只用一个对象，高效。
```

2. JDK8 以后

系统会预估要字符串拼接之后的总大小，把要拼接的内容都放在数组中，再new一个字符串对象。

就不用像JDK8以前创建这么多对象，但是“预估”也是需要时间的，也很浪费时间。



##### **StringBuilder提高效率原理图**

底层是一个可变数组，可以一直往数组后面加内容，

且只需要new一个对象，不浪费内存。



##### **StringBuilder源码分析**

长度（length）：已经存了多少字符

容量（capacity）：最多能存多少字符



当添加的字符大于容量时，会自动扩容，且新容量 = 旧容量 × 2 + 2，即16 -> 34 -> 70 -> ...

最终新容量 = MAX( 需要的最小容量, 默认扩容大小 )

```java
//这是分开append的，每次扩容所需容量都小于默认扩容大小
//初始长度 = 0，初始容量 = 16
StringBuilder sb = new StringBuilder();
//长度变成 5，容量还是 16
sb.append("hello");
//长度21，容量扩容为34
sb.append("1234567890123456");
//长度35，容量扩容为70
sb.append("12345678901234");
//一次性append，长度大于默认扩容大小，按实际的为准
StringBuilder sb = new StringBuilder();
sb.append("hello123456789012345612345678901234");//长度35，容量35
```

#### **Math**

sqrt() 开平方根

cbrt() 开立方根

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658220713-a08599b1-7b8a-479c-a7d6-0e3d5b6e87a3.png)

abs 的小bug：

以 int 类型为例，取值范围：-2147483648 ~ 2147483647；

如果没有正数与负数对应，那么传递负数结果有误，-2147483648 没有正数与之对应，所以 abs 结果产生 bug。

```java
System.out.println(Math.abs(-2147483648));//-2147483648
System.out.println(Math.abs(-2147483647));//2147483647
```

#### **System**

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658239540-ceab8968-909a-4bfd-9aab-0f28e4e20030.png)

可以用 currentTimeMillis() 计算程序运行的毫秒数；

arraycopy() 注意：

1. 如果数据源数组和目的地数组都是基本数据类型，那么两者的类型必须保持一致，否则会报错；
2. 在拷贝的时候需要考虑数组的长度，如果超出范围也会报错；
3. 如果数据源数组和目的地数组都是引用数据类型，那么子类类型可以赋值给父类类型。

#### **Runtime**

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658271847-85aafed6-027f-401e-89d1-0f9312cdc768.png)



#### **Object**

Object 是 Java 中的顶级父类。所有的类都直接或间接的继承于 Object 类。

任意类的构造方法，在第一行有隐藏的 super()，即默认访问父类的无参构造；

为什么是访问无参而不是有参，因为顶级父类 Object 类只有无参构造。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658361424-f2da4c04-a6ea-4f0a-9eac-ce82fc2f00ef.png)

System.out.println() 当我们打印一个对象的时候，底层会调用对象的 toString 方法，把对象变成字符串，

然后再打印在控制台上，打印完毕换行处理。默认情况下，因为 Object 类中的 toString 方法返回的是地址值，

如果想要看到对象内部的属性值，就要重写父类 Object 类中的 toString 方法。

Object 的 equals() 重写前比较的是对象的地址值；重写之后的 equals 方法比较的就是对象内部的属性值了。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658360700-b73a4a98-09e9-4441-9ed3-44847d987b65.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658363313-46eb06bf-d102-42cb-8e7a-bf2982fd0cb2.png)

clone() 被克隆的 JavaBean 类要实现 Cloneable 的接口，Cloneable 接口没有抽象方法，表示当前的接口是一个标记性接口。

现在 Cloneable 表示一旦实现了，那么当前类的对象就可以被克隆；如果没有实现，当前类的对象就不能克隆。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658361411-97c80df7-b8da-4325-b2cb-5dbb2fd983a6.png)

克隆出来的对象 与 被克隆的对象 指向同一个对象，即为浅克隆。

克隆时重新创建一个不同的对象，即为深克隆。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658361530-ddc6e418-2006-43dd-8891-cdbd06fd1efc.png)





Object 类的克隆为浅克隆，想要深克隆就要重写方法。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655515799-257a2754-1f3c-4601-827a-7faecfb6a825.png)

#### **Objects**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655517572-2c99484f-06e5-455f-839a-a92a1145ed52.png)

equals() 方法的底层会判断 s1 是否为 null，如果为 null，直接返回 false；

如果 s1 不为 null，那么就利用 s1 再次调用 equals 方法；

此时 s1 是 Student 类型，所以最终还是会调用 Student 中的 equals 方法。

如果没有重写，比较地址值，如果重写了，就比较属性值。

#### **Biglnteger**

对象一旦创建，内部记录的值不能发生改变，且字符串的数字必须是整数类型。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655517983-1b3de664-63cd-4a1c-9d62-bd7c7740e4d3.png)

静态方法 能表示范围比较小，在 long 的取值范围之内，如果超出 long 的范围就不行了。

在内部对常用的数字：-16~16进行了优化。提前把 -16 ~ 16 先创建好BigInteger的对象，如果多次获取不会重新创建新的。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655516741-7d6b2b51-8cf7-4b07-a91b-bf8d8006e427.png)



**成员方法**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658478026-5149a442-06d9-48d0-aff1-4fbde2ff2c55.png)

- 存储原理

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658476375-9c1a9e98-4923-49eb-ba0d-f5df0156986e.png)

数组中最多能存储元素个数：21亿多

数组中每一位能表示的数字：42亿多

BigInteger 能表示的最大数字为：42亿的21亿次方

#### **BigDecimal**

小数运算精度失真的原因，所需位数超过小数部分 bit 位数。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658476506-4d883eb5-d54b-4ab4-9934-c5bf3d258c01.png)

```java
//用double类型的小数创建对象（不准确）
BigDecimal bd1 = new BigDecimal(0.09);//0.0899...
//通过传递字符串表示的小数来创建对象（准确）
BigDecimal bd2 = new BigDecimal("0.09");//0.09
//通过静态方法创建对象（准确）
BigDecimal bd3 = BigDecimal.valueOf(0.09);
/*如果要表示的数字不大，没有超出double的取值范围，建议使用静态方法；
如果要表示的数字比较大，超出了double的取值范围，建议使用构造方法；
如果我们传递的是0~10之间的整数，包含0，包含10，那么方法会返回已经创建好的对象，不会重新new。*/
```



静态方法的底层也是 new 出来的。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658475596-d6318428-7d0d-40a6-b63b-7f833d4d189e.png)

- 成员方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658477377-8760a312-8e64-4ec8-9657-cc310085516e.png)

舍入模式：RoundingMode.HALF_UP

UP：远离零方向舍入的舍入模式

DOWN：向零方向舍入的舍入模式

CEILING：向正无限大方向舍入的舍入模式

FLOOR：向负无限大方向舍入的舍入模式

HALF_UP：四舍五入

- 存储原理

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658477300-d05d125a-87e6-4af7-bae1-76deaf4422d1.png)





#### **正则表达式**

##### **校验字符串**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655520368-3a6b3e77-ae52-46ae-ac2a-e04734da9b8c.png)

```java
System.out.println("aa".matches("[abc][abc]"));//true
System.out.println("a".matches("[abc]"));//true
System.out.println("z".matches("[abc]"));//false
```

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655519846-ae372979-f592-45f6-9f8f-81a9e9f414b7.png)

所以 /d 要写成 //d，前面加上转义字符，让后面的转义字符变成普通的杠。

```java
System.out.println("1".matches("."));//true
System.out.println("我1".matches(".."));//true
System.out.println("1".matches("\\d"));//true
System.out.println("a".matches("\\D"));//true
System.out.println("a".matches("\\W"));//false
System.out.println("!".matches("\\W"));//true
System.out.println("!".matches("\\S"));//true
System.out.println(" ".matches("\\s"));//true
System.out.println("".matches("."));//false
System.out.println("".matches(".?"));//true
System.out.println("".matches(".*"));//true
System.out.println("".matches(".+"));//false
System.out.println("aaa".matches(".{3}"));//true
System.out.println("aaaaa".matches(".{3,4}"));//false
//忽略大小写的书写方式
//在匹配的时候忽略abc的大小写
System.out.println("ABC".matches("(?i)abc"));//true
//忽略bc的大小写
System.out.println("aBC".matches("a(?i)bc"));//true
//只忽略b的大小写
System.out.println("aBc".matches("a((?i)b)c"));//true
```



- 验证手机号

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655713927-4b9a6cf1-c944-43b9-8718-f0d008364489.png)



- 验证座机号

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655715406-6bde57c6-f2d8-4b33-b1a0-3d9892c4631e.png)



- 验证邮箱号码

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655715697-20628425-72c7-40fe-9e92-19ac7cd2b292.png)



- 验证身份证

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655715670-3afe4cb3-bad5-4e78-8458-0d7f53c87603.png)



##### **爬虫**

###### **基础**

```java
String str="Java自从95年问世以来，经历了很多版木，目前企业中用的最多的是Java8和Java11,"+
        "因为这两个是长期支持版本，下一个长期支持版木是Java17，相信在未来不久Java17也会逐渐登上历史舞台";
//获取正则表达式对象
Pattern p = Pattern.compile("Java\\d{0,2}");
//拿着文本匹配器从头开始读取，寻找是否有满足规则的子串
//如果没有，方法返回false；如果有，返回true。
//在底层记录子串的起始索引和结束索引+1
Matcher m = p.matcher(str);
while (m.find()) {
    //方法底层会根据find方法记录的索引进行字符串的截取
    //subString(起始索引，结束索引)；包头不包尾
    String s1 = m.group();
    System.out.println(s1);
}
```



###### **带条件爬取**

```java
//需求一：
//爬取版本号为8,11,17的Java文本，但是只要Java,不显示版本号。
//?理解为前面的数据Java
//=表示在Java后面要跟随的数据
//但是在获取的时候，只获取前半部分
Pattern p = Pattern.compile("Java(?=8|11|17)");
//需求二：
//爬取版本号为8,11,17的Java文本。正确爬取结果为：Java8 Java11 Java17 Java17
Pattern p = Pattern.compile("Java(8|11|17)");
Pattern p = Pattern.compile("Java(?:8|11|17)");
//需求三：
//爬取除了版本号为8,11,17的Java文本
Pattern p = Pattern.compile("Java(?!8|11|17)");
```



###### **贪婪 / 非贪婪爬取**

String str="Java自从95年问世以来，abbbbbbbbbbbbaaaaaaaa经历了很多版木，目前企业中用的最多的是Java8和Java11,"+ "因为这两个是长期支持版本，下一个长期支持版木是Java17,相信在未来不久Java17也会逐渐登上历史舞台"; //只写+和*表示贪婪匹配 //+?和*?表示非贪婪匹配 //贪婪爬取：在爬取数据的时候尽可能的多获取数据；abbbbbbbbbbbb //非贪婪爬取：在爬取数据的时候尽可能的少获取数据；ab Pattern p = Pattern.compile("ab+");

###### **成员方法**

![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658665692-677d8804-ab8e-4780-8713-2da217975d2f.png)

```java
//要求1：把字符串中三个姓名之间的字母替换为vs
String s="小诗诗dqwefqwfqwfwq12312小丹丹sdasda23432小惠惠";
String resut1 = s.replaceAll("[\\w&&[^_]]+","vs");
System.out.println(resut1);
//要求2：把字符串中的三个姓名切割出来
String[] arr = s.split("[\\w&&[^_]]+");
System.out.println(Arrays.toString(arr));
```



###### **分组**

每组是有组号的，也就是序号。

规则1：从1开始，连续不间断。

规则2：以左括号为基准，最左边的是第一组，其次为第二组，以此类推。

- 捕获分组：后续还要继续使用本组的数据。

内部使用：\\组号；外部使用：$组号

```java
//内部使用
//需求1：判断一个字符串的开始字符和结束字符是否一致？只考虑一个字符
//举例：a123a b456b 17891 &abc&
String regex1 = "(.).+\\1";//表示把第一组数据拿出来再用一次
System.out.println("a123a".matches(regex1));

//需求2：判断一个字符串的开始部分和结束部分是否一致？可以有多个字符
//举例：abc123abc b456b 123789123 &:@abc&:@
String regex2 = "(.+).+\\1";
System.out.println("&:@abc&:@".matches(regex2));

//需求3：判断一个字符串的开始部分和结束部分是否一致？开始部分内部每个字符也需要一致
//举例：aaa123aaa bbb456bbb 111789111 &&abc&&
// (.):把首字母看做一组；\\2:把首字母拿出来再次使用；*:作用于\\2，表示后面重复的内容出现0次或多次
String regex3 = "((.)\\2*).+\\1";
System.out.println("&&abc&&".matches(regex3));
//外部使用
//将字符串：我要学学编编编编程程程程程程 替换为：我要学编程
String s = "我要学学编编编编程程程程程程";
System.out.println(s.replaceAll("(.)\\1+", "$1"));
```



- 非捕获分组：分组之后不需要再用本组数据，仅仅是把数据括起来。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655715124-8a84a6af-cae0-41c2-9f58-e3b984175c81.png)



#### **JDK7时间类**

##### **Date**

Date 类是一个DK写好的 Javabean 类，用来描述时间，精确到毫秒。

```java
//利用空参构造创建的对象，默认表示系统当前时间
Date d = new Date();//19:11:48
//利用有参构造创建的对象，表示指定的时间
Date date = new Date(0L);//08:00:00
//修改时间，单位为毫秒
date.setTime(1000L);//08:00:01
//获取当前时间的毫秒值
long time = date.getTime();
```



##### **SimpleDateFormat**

格式化：把时间变成我们喜欢的格式。

解析：把字符串表示的时间变成Date对象。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794655717438-8e800bab-fca5-4d51-b068-74f7f2459676.png)

```java
//空参构造
SimpleDateFormat f1 = new SimpleDateFormat();
Date date = new Date();
System.out.println(f1.format(date));//2026/4/2 22:34
//有参构造
SimpleDateFormat f = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.println(f.format(date));//2026-04-02 22:34:58
System.out.println(f.parse("2026-04-02 22:34:58"));//Thu Apr 02 22:34:58 CST 2026
```



##### **Calendar**

Calendar 代表了系统当前时间的日历对象，可以单独修改、获取时间中的年，月，日。

Calendar 是一个抽象类，不能直接创建对象。

底层原理：

会根据系统的不同时区来获取不同的日历对象，默认表示当前时间。

会把时间中的纪元，年，月，日，时，分，秒，星期，等等的都放到一个数组当中，

数组索引 1 是年，2 是月，5 是日；一般写成 Calendar.YEAR 等等。

月份：范围0~11，如果获取出来的是 0，那么实际上是 1 月。

星期：在老外的眼里，星期日是一周中的第一天

1（星期日）2（星期一）3（星期二）4（星期三）5（星期四）6（星期五）7（星期六）



- 构造方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658765264-5edc49bf-5140-4d88-9edb-679dd3caec5f.png)

- 成员方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658765543-cefbbc03-af62-4e20-bb0e-6423019f3d4b.png)

```java
Calendar c = Calendar.getInstance();
Date date = new Date();
c.setTime(date);
System.out.println(c.get(Calendar.YEAR));//2026
c.set(Calendar.YEAR,2020);
System.out.println(c.get(Calendar.YEAR));//2020
c.add(Calendar.YEAR,1);//2021
```

#### **JDK8时间类**

JDK7：多线程环境下会导致数据安全的问题

JDK8：时间日期对象都是不可变的，解决了这个问题

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658766442-83403dff-bdc0-434f-85e6-be53bfe9c4b7.png)

##### **ZoneId**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658765882-6963fd9b-08c0-4f91-8dd2-00cd863a6cfd.png)

```java
//获取所有时区
Set<String> zoneIds = ZoneId.getAvailableZoneIds();
System.out.println(zoneIds);
System.out.println(zoneIds.size());//604
//获取系统默认时区
ZoneId zoneId = ZoneId.systemDefault();//Asia/Shanghai
//获取指定时区
ZoneId zoneId1 = ZoneId.of("Asia/Shanghai");
```

##### **Instant**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658766146-1441b24c-40a5-4bd7-a74c-4ffaa05b3d2d.png)

```java
//获取当前时间
Instant now = Instant.now();
//根据秒获取对象
Instant instant = Instant.ofEpochSecond(0L);
//指定时区
ZonedDateTime zdt = now.atZone(ZoneId.of("Asia/Shanghai"));
//判断
boolean after = now.isAfter(instant);
boolean before = now.isBefore(instant);
//减少时间
Instant minus = instant.minusSeconds(1);
//增加时间
Instant plus = instant.plusSeconds(1);
```



##### **ZoneDateTime**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658766813-c0398541-eced-4471-8133-e3ece5e97c9d.png)



##### **DateTimeFormatter**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658767184-59d44887-63c7-45c9-a8a9-60c5b62ea654.png)



##### **LocalDateTime**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658767586-0efbfb44-72ea-419e-9d89-b0b2e5f1d6a2.png)



##### **Period**

计算年月日时间间隔

```java
LocalDate now = LocalDate.now();
LocalDate bir = LocalDate.of(2007, 7, 2);
Period period = Period.between(bir, now);
System.out.println(period.getYears());
System.out.println(period.getMonths());
System.out.println(period.getDays());
System.out.println(period.toTotalMonths());
```



##### **Duration**

计算时间（时、分、秒、毫秒）的差值

```java
LocalDateTime now = LocalDateTime.now();
LocalDateTime bir = LocalDateTime.of(2007, 7, 2, 0, 0, 0);
Duration duration = Duration.between(bir, now);
System.out.println(duration.toDays());
System.out.println(duration.toHours());
System.out.println(duration.toMinutes());
System.out.println(duration.toMillis());//毫秒
System.out.println(duration.toNanos());//纳秒
```



##### **ChronoUnit**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658769767-af07ec67-4fc5-4ea5-af63-42483064cb5c.png)



##### **判断闰年**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658768486-f3cd60c1-ad4d-4e90-87ab-d5fc164c2fae.png)

#### **Arrays**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794658768336-caffaf6a-1cc6-43b4-b0b2-47f6566a3da2.png)

```java
int[] arr = {1,3,4,6,7,9,11};
//binarySearch
//如果要查找的元素是不存在的，返回的是：-插入点-1
System.out.println("索引为："+Arrays.binarySearch(arr, 3));//索引为：1
//copyOf
int[] arr2 = Arrays.copyOf(arr,arr.length);//[1, 3, 4, 6, 7, 9, 11]
//copyOfRange：范围包头不包尾
int[] arr1 = Arrays.copyOfRange(arr, 0, arr.length);//[1, 3, 4, 6, 7, 9, 11]
//fill
Arrays.fill(arr,2);//[2, 2, 2, 2, 2, 2, 2]
//sort
//默认排序：基本类型数组、引用类型数组都能用；底层为快速排序
int[] arr3 = {1,5,2,3,4};
Arrays.sort(arr3);//[1, 2, 3, 4, 5]
Integer[] arr3 = {1, 5, 2, 3, 4};
//自定义排序：只能用引用类型数组；底层为插入排序加二分查找
//默认把0索引的数据当做是有序的序列，1索引到最后认为是无序的序列。
//遍历无序的序列得到里面的每一个元素，假设当前遍历得到的元素是A元素
//把A往有序序列中进行插入，在插入的时候，是利用二分查找确定A元素的插入点
//拿着A元素，跟插入点的元素进行比较，比较的规则就是compare方法的方法体
//如果方法的返回值是负数，表示当前要插入的元素是小的，拿着A继续跟前面的数据进行比较
//如果方法的返回值是正数，表示当前要插入的元素是大的，拿着A继续跟后面的数据进行比较
//如果方法的返回值是0，也拿着A跟后面的数据进行比较。直到能确定A的最终位置为止。
Arrays.sort(arr3, new Comparator<>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        //参数一o1：表示在无序序列中，遍历得到的每一个元素
        //参数二o2：有序序列中的元素
        //简单理解：o1-o2:升序排列：o2-o1:降序排序
        return o2-o1;
    }
});
System.out.println(Arrays.toString(arr3));//[5, 4, 3, 2, 1]
```





### **集合**

#### **介绍**

集合是一种动态容器，长度可变，往里面加数据，它不够用了会自己扩容，有利于**增删改。**

普通数组比如 int[] arr = new int[3]，这个数组一旦创建，长度就固定是 3 了不能变，不利于增删改。

集合底层都是用 Object[] 数组来存数据，取出来时再强转成对应的类型，Object 只能存 引用数据类型（对象），不能存 基本数据类型，

所以必须用包装类（Integer、Double、Character）把基本类型包成对象。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660878996-4eb205cf-dab2-4c63-87e8-9f88afa63320.png)



#### **泛型**

##### **认识泛型**

作用：泛型提供了在**编译阶段约束类型**，并有自动进行检查的能力，消除强制类型转换，避免类型转换异常，同时做到代码复用。

- 没有泛型时，ArrayList里可以随便塞整数、字符串、对象，取出来的时候可能搞错类型（比如想拿整数，结果拿到字符串，报错）

没有泛型时，容器只知道存 “**Object**”，取出来必须手动转成具体类型

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660879060-386ce808-a3ed-4b29-889f-2e45d3453f30.png)

- 有了泛型，ArrayList就只能放整数；ArrayList只能放字符串，编译器会帮你检查，不会装错。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660879164-6f5918d6-a18c-4db8-a4b3-46ad2c930f30.png)



##### **泛型类**

定义：在**类名后面**声明类型参数，整个类内部都可以使用这个类型。

注意：类型变量建议用大写的英文字母，常用的有：E、T、K、V等，定义泛型时使用，充当类型占位符，使用时才指定真实类型。

ArrayList 底层用的是Object[] 数组，取出来时再强转成对应类型

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660879183-f68923e2-a8a5-49f8-9866-4f790e0ea0e5.png)



##### **泛型接口**

定义：在**接口名后面**声明类型参数，实现这个接口时，指定具体类型。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660879429-07c7c841-3fec-403c-b23c-9f19d9f00497.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660880235-5644818a-d937-43ad-9849-54b91f1b325a.png)

实现方式两种：

1. 实现类确定类型（推荐）

```java
public class UserRepository implements Repository<User> {
    @Override
    public User findById(Long id) { ... }
}
```

2. 实现类继续保留泛型，变成泛型类

```java
public class BaseRepository<T> implements Repository<T> {
    @Override
    public T findById(Long id) { ... }
}
```

##### **泛型方法**

定义：**方法自己单独声明泛型参数**，写在**方法返回值前面**，不属于类。

不用泛型方法，只能打印固定类型：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660880307-d40bcd64-638e-423d-8cf2-eeba23d97274.png)

用泛型方法，一个方法搞定所有类型：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660880355-8551a9ff-2b2a-447a-bc73-22f80c54cb36.png)

定义一个工具类：ListUtil，类中定义一个静态方法addAll，用来添加多个集合的元素。

```java
public class ListUtil {
    public static <E> boolean addAll(List<E> list, E...e) {
        for (E element:e){
            list.add(element);
        }
        return true;
    }
}
```

类上有泛型，方法再加  <T> 会怎样？

```java
class Box<T> {
    public <T> void test(T t){}
}
```

方法的 <T> **会覆盖类上的 T**，两个 T 不是同一个，属于不同作用域，容易混淆，不推荐这么写。



##### **通配符**

泛型不具备继承性

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660880859-fc03a324-e63c-43ab-9a27-05a384ad5d1a.png)



如用泛型方法，会有一个小弊端，它可以接受任意的数据类型，不管是否是子父类

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660880949-2ce5cf38-b562-4fcb-a257-84d907347cf8.png)



- 通配符的限制

**无界通配符 ?** 可以在“**使用泛型**”的时候代表一切类型；而 E、T、K、V 是在**定义泛型**的时候使用。

List 表示 “任意类型的 List”，但它只能**读取元素**（且**只能用****Object** 接收），

**不能添加元素**（因为不知道具体类型，添加任何类型都可能出错）。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660881440-2eec2958-b786-422e-945b-7fe9dd0e91db.png)



区别：

- <T>：**声明一个类型变量 T**，你给它起个名字，方法内可以复用这个 T（可以拿 T 当返回值、定义变量）
- ?：**通配符（wildcard），代表某个未知类型，没有名字，不能复用**，只能表示 “某种类型，但我不知道它具体是什么”

```java
private static <T> void run(ArrayList<T> list,T a) {
    list.add(a);
    System.out.println(list);
}
private static void run2(ArrayList<?> list,? a) { //报错
    list.add(a); //报错
    System.out.println(list);
}
```

使用 ? 场景：方法只需要遍历、读取集合，不需要新增元素，也不需要把元素作为返回值向外传递，不关心元素具体类型。

使用  <T> 泛型方法场景：比如把元素返回出去，或者取出元素之后再放回集合。



##### **上下限**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660881672-dc953a93-0881-4ac6-8984-2d817e5b43a0.png)

上限最多装 Ye，不能装范围更大的东西，比如Object

下限最少能装 Zi，也能装 Object 这些更大的类型

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660881773-6bfae4bc-95a0-495d-8d48-1778698d4738.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660882456-22b0d341-797c-4d76-a312-6b72072202c3.png)

##### **泛型支持的类型**

泛型不支持**基本数据类型**如int、double，只能支持**引用数据类型(对象类型)**如类、接口、数组。

###### **包装类**

包装类就是把**基本类型**的数据包装成**引用类型**（对象）。

使得它具有了对象的性质，并且为其添加了属性和方法，丰富了基本类型的操作。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660882695-11fd1150-7540-482e-813f-b8ca844aae89.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660882824-1ba19800-de2f-49d2-82c2-115890f835db.png)

- 包装类特点

封装性：所有的包装类都是 final 类，这意味着它们不能被继承。这种设计确保了包装类的行为和特性的一致性，从而避免了子类可能引入的不确定性。

不可变性：包装类的实例一旦被创建后，其中保存的基本数据类型数据就不能再被改变。这种不可变性使得包装类在多线程环境中更加安全，避免了因数据被意外修改而导致的错误。

提供方法：包装类封装了许多实用的方法，提供了丰富的功能。例如，它们支持数据类型转换、判断字符串的大小写、以及获取最大值和最小值等。

继承关系：除了 Character 和 Boolean 之外，其他所有的包装类都继承自 Number 类。这种继承关系使得这些包装类能够共享一些通用的功能和特性，例如数字的比较和转换，这为在不同数值类型之间的操作提供了一致的接口。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660882868-65e13f7c-3243-4c48-a997-84ebd1d0d8cb.png)

- 获取 Integer 对象（JDK5以前的方式）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660883774-fc9dfe8a-e088-48ca-8df4-adc543ed9811.png)

第一和第二都是手动装箱

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660884444-b7fb5e30-6628-46bb-81ea-db5ad49ae0d9.png)

- 这两种方法获取对象的区别

Java中的包装类缓存机制是为了优化性能和节省内存而设计的。

它为整型（Byte、Short、Integer、Long）、字符型（Character）和布尔型（Boolean）的包装类提供了缓存，确保在这些类型的小范围值之间可以复用对象。而对于浮点数类型的包装类（Float、Double），则没有这种缓存机制，意味着每次都需要创建新的对象。

Byte：-128 到 127

Short：-128 到 127

Character：0 到 127（即所有的ASCII字符）

Boolean：只有 true 和 false 两个值会被缓存。

Integer 对 -128~127 之间的整数做了缓存，提前**创建**了 **-128~127** 之间所有整数的 Integer 对象。当你用 Integer.valueOf(n) 或者自动装箱（Integer i = n）创建这个范围的整数时，不会新建对象，而是直接返回缓存池里的**同一个对象**。

```java
Integer i1 = 127;
Integer i2 = 127;
System.out.println(i1 == i2);//true
Integer i3 = 128;
Integer i4 = 128;
System.out.println(i3 == i4);//false
//注意：用 equals 比较的是数值本身，无法体现“是否是同一个对象”
System.out.println(i3.equals(i4));//true
```

- 包装类进行计算要拆箱，把得到的结果再装箱

手动装箱(不推荐)：

 Integer i1 = Integer.valueOf(100);

自动装箱（JDK5以后）：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660884068-3799d743-62b2-47be-b3ea-c05af14e2eff.png)



- 自动装箱和自动拆箱的底层实现

1. 自动装箱的底层实现

自动装箱是通过调用包装类的valueOf()方法来实现的。

我们可以通过调试来验证这一结论，在要发生自动装箱的地方打上断点，然后直接跳到这个断点处，然后强制进入（force step into），就会进入到 valueOf() 方法中。

2. 自动拆箱的底层实现

自动拆箱是通过调用包装类对象的xxxValue()方法来实现的。

我们可以通过调试来验证这一结论，在要发生自动拆箱的地方打上断点，然后直接跳到这个断点处，然后强制进入（force step into），就会进入到 xxxValue() 方法中。



Integer 类成员方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660884510-6b09baf9-3148-419d-a9d0-0e7f66eb916a.png)

8种包装类当中，除了 Character 都有对应的parseXxx的方法，进行类型转换。



- 包装类具备的其他功能

1. 把字符串数值转换成对应的基本数据类型(很有用)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660884410-b85273fa-71a1-4a8b-b9ad-07819a74ea68.png)

2. 把基本类型的数据转换成字符串。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660884976-4358e969-0d9e-436f-8db6-c1f8b9a67234.png)



- 注意

性能开销： 装箱和拆箱涉及对象创建和方法调用，有性能开销。在性能敏感的循环中，应尽量避免不必要的装箱拆箱。

内存占用： 如前所述，一个 Integer 对象比一个 int 占用多得多的内存（约 24 字节 vs 4 字节）。

空指针异常（NullPointerException）：

```java
Integer num = null;
int value = num; //  抛出 NullPointerException
```

== vs .equals()： 这是最大的陷阱！== 比较的是对象引用，.equals() 比较的是值。

 

#### **Collection集合**

##### **认识**

Collection代表**单列集合**，每个元素（数据）只包含一个值。

Map代表**双列集合**，每个元素包含两个值（键值对）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660886295-5d5da333-067c-4301-a93d-c5a453ae3863.png)

Collection集合特点：

- List系列集合：添加的元素是**有序、可重复、有索引**。（有序指的是，存和取的顺序是一样的）

ArrayList、LinekdList：有序、可重复、有索引。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660885446-b4703b4f-4c1e-440b-8d7f-176ed645606c.png)

- Set系列集合：添加的元素是**无序、不重复、无索引**。

HashSet：无序、不重复、无索引；

LinkedHashSet：有序、不重复、无索引。

TreeSet：按照大小默认升序排序、不重复、无索引。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660885656-e08296e7-0db2-4d52-99f0-a68055390ec0.png)

##### **Collection的常用功能**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660886346-fd52c6a8-d4cd-4f3f-b188-b7b976ee7560.png)

Collection 是一个接口，我们不能直接创建他的对象。

所以，现在我们学习他的方法时，只能创建他实现类的对象。

实现类：ArrayList

添加元素：

细节1：如果我们要往List系列集合中添加数据，那么方法永远返回 true，因为List系列的是允许元素重复的。

细节2：如果我们要往Set系列集合中添加数据，如果当前要添加元素不存在，方法返回 true，表示添加成功。

如果当前要添加的元素已经存在，方法返回 false，表示添加失败，因为Set系列的集合不允许重复。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660886370-dc76569c-c82c-4c50-a5bd-b264ef7aea18.png)

##### **三种遍历方式**

###### **迭代器遍历**

迭代器是 **所有集合通用**的遍历方式，不管是 List、Set，用迭代器都能遍历，迭代器不依赖索引。

如果不用迭代器，List 可以用 for 循环的索引遍历，但 Set 没有索引，没法用 for 循环。

遍历所有实现Iterable接口的集合，Map集合就不能遍历。

注意：

迭代器遍历完毕，**指针不会复位**；

循环中只能用一次 next() 方法，hasNext()方法和 next() 方法要一一对应。

```java
Set<Integer> set = new HashSet<>();
set.add(12);
set.add(22);
set.add(32);
set.add(39);
//获取迭代器
Iterator<Integer> it = set.iterator();
while (it.hasNext()){ //当前位置是否有元素
    System.out.println(it.next());
    //获取当前元素，并将迭代器对象移向下一位
}
```



迭代器遍历时，不能用集合的方法进行增加或者删除。

通过迭代器自己的remove()方法删除则不会报错

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);
list.add(4);
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
    Integer i = iterator.next();
    if (i.equals(1)){
        list.remove(1);
        //iterator.remove();通过迭代器自己的remove()方法删除则不会报错
    }
}
```

遍历Map集合，可以先把 Map 转成单列集合，再用迭代器

```java
Set<Map.Entry<String,Integer>> entrySet = map.entrySet();
Iterator<Map.Entry<String,Integer>> it = entrySet.iterator();
while(it.hasNext()){
    Map.Entry<String,Integer> entry = it.next();
    String k = entry.getKey();
    Integer v = entry.getValue();
}
```



###### **增强for循环**

for-each 是编译期语法糖，底层等价于 Iterator 迭代器遍历，为了简化迭代器的代码书写的。

增强for循环**不能在遍历中增删**某数据，不然会出现**并发修改异常**问题。

所有的单列集合(所有实现Iterable接口的集合) 和 数组 才能用增强for 进行遍历。

- 对于实现Iterable接口的集合

```java
for(String s : list){
    System.out.println(s);
}
```

编译后编译器自动翻译成等价代码：

```java
Iterator<String> it = list.iterator();
while(it.hasNext()){
    String s = it.next();
    System.out.println(s);
}
```

- 对于Map集合，可以先把 Map 转成单列集合，再用迭代器

```java
for (Map.Entry<String, Integer> entry : map.entrySet())
```

- 对于数组

```java
for(String s : list){
    System.out.println(s);
}
```

编译后编译器自动翻译成等价代码：

```java
Iterator<String> it = list.iterator();
while(it.hasNext()){
    String s = it.next();
    System.out.println(s);
}
```

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660886855-bed42a52-84cf-46e4-8787-770cd6abff17.png)

修改增强for中的变量，**不会改变集合中原本的数据**。

增强for会把每一个元素传给 i

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660886893-35300ceb-47f6-4989-925c-94303af70a90.png)

相比于 for 循环：代码更简洁，避免索引错误

```java
int[] arr = {1,2,3};
for(int i = 0; i < arr.length; i++){
    arr[i] = arr[i] * 2;   // 直接修改原数组
    // arr[i]代表数组真实位置，赋值直接改内存里的数组
}
```

- 对于LinkedList，for-each循环相比传统for循环的性能如何

传统 for 循环 = 每次找 “第 3 个、第 5 个”，每次都从队头开始数，O(n²)

```java
for (int i = 0; i < list.size(); i++) {
    String s = list.get(i);  // 每次都要从头开始数到第 i 个
}
```

for-each = 在书里夹一个书签，每次看完当前页，直接把书签移到下一页，永远从书签的位置继续，性能好 O (n)

每次调用 next()，它做两件事：返回当前指针指向的元素；把指针移动到下一个节点。

迭代器内部会维护一个“当前位置”的引用，所以迭代器不会从头开始遍历，因为迭代器指针不会复位

```java
for (String s : list) {
    // 直接用 s
}
```



###### **lambda表达式**

foreach 底层源码：自己遍历集合，依次得到每一个元素，然后把得到的每一个元素，传递给下面的 accept 方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660887538-ad9701f4-5a40-44c4-9140-d296dcdd5910.png)

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);
list.add(4);
//匿名内部类
list.forEach(new Consumer<Integer>() {
    @Override
    public void accept(Integer i) {
        System.out.println(i);
    }
});
//lambda
list.forEach(i -> System.out.println(i));
//方法引用
list.forEach(System.out::println);
```



###### **并发修改异常问题**

**遍历集合的同时又存在增删集合元素**的行为时可能出现业务异常，这种现象被称之为并发修改异常问题。

ArrayList 的源码：

modCount 集合现在变化的次数，只要对 ArrayList 做增 / 删 / 清空等操作，modCount 就会 +1；

expectedModCount 当调用 list.iterator() 创建迭代器对象时，会把当前的 modCount 赋值给它。

如果用迭代器的 remove() 内部会执行 expectedModCount = modCount，保证两者相等。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660887539-042aa096-8c73-4582-aac7-618a93d948bb.png)

**contains()**只有**String**类型能用

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660887733-84346576-f73e-428e-94af-0df41026a1b4.png)

- **解决方法一：**

在普通for循环后加i--，使游标后移一位，这样可以读到所有的数据

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660888226-048da18e-e76c-4537-855c-721dee2a1a49.png)

- **解决方法二：**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660888696-a8a1d58d-4867-4ef8-ae54-536239121bec.png)

- **解决方法三：**

使用迭代器自己的方法remove()方法，Iterator 没有 add() 方法。

ListIterator 有add()方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660888789-206bd6b2-94b5-4fdf-afd0-bbf26d374b77.png)

- **总结：**

①如果集合支持索引，可以使用for循环遍历，每删除数据后做i--或者可以倒着遍历。

②如果集合不支持索引，可以使用迭代器遍历，并用迭代器提供的删除方法删除数据。

注意：增强for循环/Lambda遍历均不能解决并发修改异常问题，因此它们只适合做数据的遍历，不适

合同时做增删操作。



#### **List集合**

##### **List 特点**

ArrayList、LinkedList：有序，可重复，有索引。

注意：链表是没有索引的，之所以有索引，是因为 LinkedList 实现了 List 接口，但只是假索引。

##### **ArrayList**

ArrayList是集合中最常用的一种，ArrayList是**泛型类**，可以**约束**存储的**数据类型**。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660889995-243839e1-dd4d-4bba-bb2b-3a202506392b.png)

remove() 方法注意事项：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660889492-6744eb1b-7b6d-41d2-8377-f68019fc9a64.png)

ArrayList底层是基于**数组**存储数据的。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660889826-f0023cac-94f7-450d-a516-bbd3aee9da3f.png)

它的本质是 **动态数组**（可以自动扩容的数组），特点为：

1. 查得快：有索引，一步就能定位，时间复杂度为O (1)。
2. 改得慢：如果在中间插入 / 删除元素，后面的 “位置” 都要移动。比如你在第 3 个位置插入一个元素，第 3、4、5... 位置都得往后移。

利用空参创建的集合，在底层创建一个默认长度为0的数组；

添加第一个元素时，底层会创建一个新的长度为10的数组；

存满时，会扩容成长度为15的数组。如果一次添加多个元素，1.5倍还放不下，则新创建数组的长度以实际为准。

##### **LinkedList**

LinkedList底层是基于**链表**存储数据的。

- 链表的特点：

链表中的数据是一个一个独立的结点组成的，结点在内存中是不连续的，每个结点包含**数据值**和**下一个结点的地址**。

最后一个节点的指针指向 null。

就像你有一串珠子，每个珠子上都穿了一根线，这根线只连接着下一个珠子 —— 这就是 LinkedList（**链表**）的基本样子。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660890239-6aa6c57e-b697-4f0f-8e44-e4c0928c339b.png)

- 与数组的区别：

1. 数组就像一排固定座位，每个位置有**索引**，即查询快，增删慢。
2. 而链表**没有索引**，元素也不知道自己在什么位置，无论查询哪个数据都要从头开始找，所以必须从**第一个元素**开始一个个往后找（较慢）。

但中间元素增删很快，只需要改一下前后的 “记住关系”。但对**首尾元素**进行**增删改查**的速度是极快的。

即增删快，查询慢

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660890568-1f448a99-0d64-4422-9547-6c9d5432509b.png)

LinkedList新增了：很多首尾操作的特有方法。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660891170-171ce1b9-f151-4496-adde-1a5ce2585d94.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660890900-dec66c96-55b8-429d-bab9-4584d18eb148.png)

##### **列表迭代器**

ListIterator，是专门给 List 集合用的迭代器

往前遍历基本用不到，因为迭代器对象默认指向零索引

| 特性     | Iterator | ListIterator     |
| -------- | -------- | ---------------- |
| 适用     | 所有集合 | 只有 List        |
| 遍历方向 | 只能向后 | 双向（前后都能） |
| 添加     | 不能     | 能               |
| 修改     | 不能     | 能               |
| 删除     | 能       | 能               |

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);
list.add(4);
ListIterator<Integer> lit = list.listIterator();
while (lit.hasNext()){
    Integer i = lit.next();
    if (i==2){
        lit.set(7);//[1, 7, 3, 4]
        lit.remove();//[1, 3, 4]
        lit.add(5);//[1, 5, 3, 4]
    }
}
System.out.println(list);
```



- 五种遍历方式

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660892265-4bba6d2f-24b4-4dfe-ba8b-2c44a4a2ac21.png)



##### **综合案例**

电影信息管理模块

需求：

开发一个电影信息管理模块，用户可以上架电影，查询电影，

下架某部电影，以及下架某个主演参演的全部电影。

分析：

1. 每部电影都是一个电影对象，设计电影类。
2. 需要定义一个电影操作类，其对象专门用于处理电影数据的业务。
3. 操作类中需要定义一个集合存放全部的电影对象

电影类

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660891982-d9a61e9b-47bf-432f-9845-0bcee2d00656.png)

主程序

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660891896-95d89c0e-90d8-4af2-881f-49c6179e9964.png)

电影操作类

修改：String command = sc.next();

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660892579-04263a8a-e718-43d2-93c2-a9dcd702c646.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660892794-dbc695a9-47ed-4ef6-ab42-27f6e717ddab.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660894115-bdf27c37-b406-4c8c-b0b3-359e2856e4fd.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660894141-9a5edfac-e710-4f9a-bb84-6bce86a53d90.png)



#### **Set集合**

##### **特点**

HashSet：无序、不重复、无索引。

无索引：没有带索引的方法，所以不能使用普通 for 循环遍历，也不能通过索引来获取元素。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660893874-9453ecff-75b6-415c-890c-45563b70732b.png)

LinkedHashSet：**有序**、不重复、无索引。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660894736-396e1ea3-2749-4f8e-919f-b11389220afd.png)

TreeSet：排序(默认按大小升序)、不重复、无索引。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660895204-d4bcb231-f132-42eb-889a-ddfcaea81c75.png)



##### **HashSet底层原理**

**HashSet**本质上**是一个 “包装” 了 **HashMap 的集合。

往 HashSet 中添加元素时，**本质上**是做了这样一件事：把这个元素作为 HashMap 的 key，再搭配一个**固定的、无意义**的 value（比如一个静态常量 PRESENT）存到 HashMap 中。

举个例子：hashSet.add("苹果") 等价于 hashMap.put("苹果", PRESENT)。

因为 HashMap 有个特性：key 不能重复（如果存相同的 key，会覆盖旧值），所以 HashSet 自然就实现了 “元素不重复” 的特性。



HashMap 的底层用**哈希表**存储数据

哈希表组成：

JDK8以前：数组 + 链表

JDK8以后：数组 + 链表 + 红黑树的混合结构

当添加元素时，对象会调用 Object 的 hashCode() 方法得到的一个随机值，即哈希值。

该方法定义在 Object 类中，所有对象都可以调用，默认使用地址值进行计算，

一般情况下，会重写 hashCode() 方法，利用对象内部的属性直计算哈希值

如果没有重写 hashCode 方法，不同对象计算出的哈希值是不同的；

如果已经重写 hashCode 方法，不同的对象只要属性值相同，计算出的哈希值就是一样的

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660895492-314d8992-405c-46f1-8ca2-81853c68f031.png)

创建一个默认长度 16 的数组，默认加载因子为 0.75，数组名 table[ ]，

再通过（ int index = (数组长度 - 1) & 哈希值; ）获得数组索引。

16*0.75 = 12，存数组的数据超过 12，就会扩容成原来的两倍。

存入数组时，如果索引对应的数组位置是空的，直接把元素（作为 key）存到这里。

如果索引位置已经有元素了（发生了 哈希冲突：不同元素算出了同一个索引），就需要进一步判断：

用 **equals()** 方法比较两个元素是否相同。

如果相同，说明是重复元素，不存；

如果不同，就把新元素挂到已有元素的后面，形成 链表 。

当同一个索引下的链表太长，当链表长度大于 8 而且数组长度大于等于 64，链表会自动转成 **红黑树**。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660895832-ce1d42a2-d7d6-4341-b4d5-18ad97f8ec4b.png)



- HashSet 无序的原因

是通过 HashCode() 计算的索引存入数组，而不是通过添加的先后顺序而存入数组，

而取出时，是通过数组索引依次取出（null 位置跳过不取），所以无序。

- 为什么 HashSet 没有索引

链表、红黑树会挂在同一个索引下

- HashSet 如何去重

重写 equals() 和 hashCode() 方法

- HashSet 有红黑树，为什么 HashSet 不需要重写 Comparable 接口来排序，而 TreeSet 需要

因为 TreeSet 按 key 的大小排序；

而 HashSet 按 key 的 hash 值 排序，谁的 hash 值大，谁就在右边。

- TreeMap 和 HashMap 谁的效率更高？

HashMap 可以用数组索引直接定位；

TreeMap 红黑树：必须不停比大小，必须一层层遍历节点，插入、删除还要旋转平衡。



###### **红黑树**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660896719-a7992d3f-2af8-4a98-8219-32d46ae9d0b5.png)

红黑树的每个节点是独立的对象，对象存储了当前的数据、父节点地址值、左子节点地址值、右子节点地址值。

没有父节点或子节点则记为 null。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660896698-c0c1123a-4b07-4910-840c-0cff820ec9cc.png)

红黑树是一种自平衡的二叉查找树，红黑树不是高度平衡的，它的平衡是通过"红黑规则"进行实现的。

平衡二叉树查找效率高。但弊端是添加节点时，旋转的次数多，会造成时间浪费。

而红黑树增删改查性能很高。

它是一种特殊的二叉查找树，红黑树的每一个节点上都有存储位表示节点的颜色。

红黑规则：

1. 每一个节点要么红，要么黑；
2. 且根节点必须是黑色；
3. 如果一个节点没有子节点或者父节点，则该节点相应的指针属性值为 Nil，这些 Nil 视为叶节点，每个叶节点是黑色的，
4. 如果某一个节点是红色，那么它的子节点必须是黑色 (不能出现两个红色节点相连的情况)
5. 对每一个节点，从该节点到其所有后代叶节点的简单路径上，均包含相同数目的黑色节点；

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660896983-56e91389-9620-4431-b052-d6119da00923.png)

默认颜色：添加节点默认是红色的 (效率高)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660897980-a8eff1f0-de92-47d7-bd3d-cf5235c78f2f.png)





###### **二叉树**

二叉树中，任意节点的度<=2；

左小右大的二叉树称为二叉查找树。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660899111-1addc824-6294-493f-9153-a41460faee5f.png)

- 二叉树遍历方式

1. 前序遍历：当前节点在前面获取

从根结点开始，然后按照当前结点，左子结点，右子结点的顺序遍历

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660898847-6b99bec3-e8c9-4cf0-aa77-9114fcddbc4f.png)

2. 中序遍历（常用）：当前节点在中间获取

从最左边的子节点开始，然后按照左子结点，当前结点，右子结点的顺序遍历

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660898858-9d567660-71de-497f-8b58-5d23dbea2580.png)

3. 后序遍历：当前节点在后面获取

从最左边的子节点开始，然后按照左子结点，右子结点，当前结点的顺序遍历

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660898853-2304e7fc-f420-49c5-8512-a31273e8b206.png)

4. 层序遍历

从根节点开始一层一层的遍历

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660900981-9de058e5-8cef-4e7a-850f-1d5cbd3ab759.png)

平衡二叉树

查找效率高。弊端是添加节点时，旋转的次数多，会造成时间浪费。

规则：**任意节点**左、右子树高度差不超过 1。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660900938-e48b1990-73d5-46ee-93b6-84a2c89438eb.png)

平衡二叉树如何保持平衡

当左右子树高度差超过1时，通过旋转保持平衡。

旋转机制：从添加的节点开始，遇到的第一个不平衡的点当作支点，进行旋转，再以支点为基准判断是左旋还说右旋。

左旋：

eg1：

以不平衡的点作为支点，把支点左旋降级，变成左子节点，晋升原来的右子节点。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660900855-975d1092-96ef-4fb5-bba1-65f4bc6a2c62.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660900820-a5a96b34-243f-464b-aaca-1553abea1e79.png)

eg2：

以不平衡的点作为支点，将根节点的右侧往左拉，原先的右子节点变成新的父节点，

并把多余的左子节点（本例为9）出让，给已经降级的根节点当右子节点。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660901026-ea745e01-401d-4658-9785-32e519077584.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660902213-f3a885b7-31d6-4f2e-9d60-dcde0ec1257d.png)

右旋：

eg1：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660902137-271853ce-7909-46f2-a7c7-ee0b6eeab018.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660902649-90d527ef-acad-4eca-bc86-0fc73f8a2478.png)

eg2：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660904006-6b33e98c-fe51-4f75-94fc-0368fae5e041.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660904056-f80fcc42-19e9-4236-ba98-8af522151182.png)

触发左旋、右旋的情况：

左左：当根节点左子树的左子树有节点插入，导致二叉树不平衡。

右旋一次就行

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660904135-366e7536-a992-4c53-bc5a-ad1d9a03b2f9.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660904097-008d3b44-abff-4811-9114-b6cc1d62b79f.png)

左右：当根节点左子树的右子树有节点插入，导致二叉树不平衡

先局部左旋，再整体右旋

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660904567-3aac39b5-3a0c-47f9-a1bf-af287e3b3649.png)

先以4为支点进行局部的左旋，变成左左情况

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660906159-b7af93f0-417c-404d-a394-1651a5d4647b.png)

再进行整体的右旋

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660906067-0723b031-5b23-4b07-9117-f397e91982d8.png)

右右：当根节点右子树的右子树有节点插入，导致二叉树不平衡

左旋一次就行

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660906046-e38946b5-bca5-4b0b-80aa-348e6b14c523.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660906072-a2da0880-0e45-4770-9a0e-e88e566d8ebd.png)

右左：当根节点右子树的左子树有节点插入，导致二叉树不平衡

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660908212-5188b934-4a34-4186-aed2-5d5f0f185a75.png)

先以 10 为支点进行局部的右旋，变成右右情况

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660908995-1d69b9d0-4fb1-417f-ac45-45ca196e8f0a.png)

再进行整体的左旋

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660909053-67a28c44-1868-4dc8-a901-cd5710244455.png)

##### **HashSet**去重操作

如果是自定义对象一定要重写方法；如果是 String 或 Integer 类型，底层已经重写好了，不需要自己重写。

需求：创建一个存储学生对象的集合，存储多个学生对象，

要求多个学生对象的成员变量值相同时，我们就认为是**同一个对象**，要求只保留一个。

HashSet 会按 “对象的**内存地址**” 算哈希值，两个**不同的对象的地址**不一样，所以不算重复

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660909100-6f7d8e94-0af9-40f2-aa0c-9740fb196f1a.png)

结论：如果希望Set集合认为 2 个内容一样的对象是重复的，必须重写对象的 hashCode() 和 equals() 方法

在学生类中右键直接生成方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660909477-7cd42d83-6089-47dc-989d-29b97501833d.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660909839-675d8930-a92b-418b-8797-eee3797e8891.png)



##### **LinkedHashSet底层原理**

有序、不重复、无索引。

这里的有序指的是保证存储和取出的元素顺序一致

原理：底层数据结构还是哈希表，只是每个元素又额外的多了一个双链表的机制，来记录存储的顺序。

所有元素通过这两个指针，串成一条 “有序队列”（比如添加顺序是 A，B，C，链表就串成 A↔B↔C）；

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660912223-0b9e1964-bf15-4772-82b0-40b347012cac.png)

与 HashSet 不同的是，遍历的不是数组，数组只是拿来存储，而是直接遍历这条链表，所以是有序的。

不过加了链表后效率会低，如果只是数据去重而不求有序，默认用 HashSet。



##### **TreeSet集合**

底层是基于**红黑树**实现的，增删改查性能好。

- 对于数值类型：Integer，Double，默认按照从小到大的顺序进行排序。
- 对于字符、字符串类型：按照字符 ASCII 码表中的数字升序进行排序。

TreeSet 集合存自定义对象要写比较规则，不然会报错。



###### **自定义对象的排序**

1. 默认排序：对象类实现一个 Comparable 接口，重写 compareTo() 方法，指定大小比较规则

compareTo 方法的返回值：

负数：当前对象 排在前面，即在红黑树中存在左边

正数：当前对象 排在后面，即在红黑树中存在右边

0 ：对象重复，不添加

当前对象 this = 你正要往集合里添加的新元素

传入对象 o = 集合里已经存在的老元素

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660912111-7b1dc0c0-e7c4-41d3-9191-65e6a8965ad2.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660912051-7878c5ba-5a62-40a2-8eb4-08a1ad6a1baa.png)

输出结果为：[Person(name=jack, age=20), Person(name=tom, age=20), Person(name=rose, age=90)]



2. 比较器排序：创建 TreeSet 对象时候，传递比较器 Comparator 指定规则

如果第一种无法满足当前需求，就使用第二种方法。

需求：请自行选择比较器排序和自然排序两种方式：

要求：存入四个字符串，“c”，“ab”，“df”，“qwer”，按照长度排序，如果一样长则按照首字母排序。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660912203-8fe1702b-6f6c-4c10-a70e-ac1fb20b4cae.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660914341-9518ea96-deea-4b8d-bc1b-99883a5be8ae.png)



TreeSet 的排序和去重完全依赖 Comparable/Comparator；

hashCode 和 equals 方法与哈希表有关；

TreeSet 底层为红黑树，所以不需要重写 hashCode 和 equals 方法。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660915580-6892331f-90b7-4dd5-958d-ea6dad6e793c.png)



#### **Map集合**

##### **概述**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660915110-c6bb20d1-01d9-455a-aa89-648ffb1dee72.png)

Map集合也被叫做“**键值对集合**”，格式：{ key1=value1,key2=value.2,key3=value3,..}

- 特点

1. Map集合的**所有键是不允许重复**的，但值可以重复。重复 key，会覆盖之前的 value。
2. 键和值是一一对应的，每一个键只能找到自己对应的值。

“键值对对象”，在 Java 中叫做“ Entry 对象”

需要存储一一对应的数据时，就可以考虑使用Map集合来做

注意：Map系列集合的**特点都是由键决定**的，值只是一个附属品，值是不做要求的

HashMap(由键决定特点)：无序、不重复、无索引；（用的最多）

LinkedHashMap（由键决定特点)：由键决定的特点：有序、不重复、无索引。

TreeMap(由键决定特点)：按照大小默认升序排序、不重复、无索引。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660915436-92a1eb42-a109-4c73-9905-d79094496466.png)



##### **常用方法**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660916128-2d7f75c9-5f6d-42fc-8585-1dab374ec16f.png)

- 添加元素注意事项

在添加数据的时候，如果键不存在，那么直接把键值对对象添加到map集合当中，方法返回 null；

在添加数据的时候，如果键是存在的，那么会把原有的键值对对象覆盖，会把被覆盖的值进行返回。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660916481-cb552177-6c98-45a1-a288-5f0f59a444ee.png)

###### **存自定义对象当 key**

如果用自定义对象当 key，要重写方法，不然 Map 认不出 “相同的 key”，会导致重复存储。

- HashMap / LinkedHashMap：必须重写 hashCode() 和 equals()
- TreeMap：Comparable 或 Comparator

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660917840-07909eb7-1a37-44fc-9910-df0f3a1e01f5.png)



##### **遍历方式**

1. 键找值

先获取 Map 集合全部的键，再通过遍历键来找值

遍历键可以用迭代器、增强 for、lambda 来遍历。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660917971-00d749dd-a4ae-4ce5-b29a-0932c466fee8.png)



2. 键值对

把“键值对“看成**一个整体**进行遍历

Map.Entry：可以理解为 “一个封装好的**键值对对象**”，直接遍历所有Entry对象，既能拿 key 也能拿 value，不用再通过 key 查 value

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660919369-c9f5f440-64c1-452a-9319-57357d03acfe.png)



3. Lambda

forEach 底层就是其实就是利用第二种方式进行遍历，依次得到每一个键和值，传给 accept 方法。

map.forEach((key, value) -> {

 // 这里写处理key和value的逻辑（比如打印、修改value等）

});

(key, value)：Lambda 的 “参数列表”，对应 Map 的一个键值对（key 是键，value 是值，类型和 Map 一致）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660919389-4a66070b-b867-48c3-8444-a8611300cfcc.png)



##### **实现类**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660920135-612f6ea2-9718-4dae-b764-9f6f1114265f.png)

Map 是键值对（key-value）的“**接口**”，而 HashMap、LinkedHashMap、TreeMap、Hashtable 这些是具体的 “**实现类**”。



###### **HashMap**

- 特点

1. HashMap是Map里面的一个实现类。
2. 没有额外需要学习的特有方法，直接使用 Map 里面的方法就可以了。
3. 特点都是由键决定的：无序、不重复、无索引
4. HashMap 跟 HashSet 底层原理是一模一样的，都是哈希表结构

其中哈希值是根据键来计算，与值无关；判断重复也只会判断键，不会判断值。



- 案例

需求：某个班级80名学生，现在需要组织秋游活动，班长提供了四个景点依次是（A、B、C、D)，

每个学生只能选择一个景点，请统计出最终哪个景点想去的人数最多。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660920289-0a9158ce-ae6b-445e-add0-04947f6c4d91.png)



###### **LinkedHashMap**

- 特点

1. 由键决定：有序、不重复、无索引。
2. 这里的有序指的是保证存储和取出的元素顺序一致
3. 原理：底层数据结构是哈希表，只是每个键值对元素又额外的多了一个双链表的机制记录存储的顺序。



###### **TreeMap**

- 特点

1. TreeMap 跟 TreeSet 底层原理一样，都是红黑树结构的。
2. 由键决定特性：不重复、无索引、可排序。

注意：默认按照键的从小到大进行排序，也可以自己规定键的排序规则

- 排序规则书写

1. 实现 Comparable 接口，指定比较规则。
2. 创建集合时传递 Comparator 比较器对象，指定比较规则。



###### **总结**

单线程、不用有序 / 排序：选 HashMap（万能首选）；

单线程、要存序 = 取序：选 LinkedHashMap；

单线程、要自动排序：选 TreeMap；

多线程、要安全高效：选 ConcurrentHashMap；

看到 Hashtable：知道是古老类，别主动用。



#### **Stream流**

##### **认识**

是 JDK8 开始新增的一套API (java.util.stream.*)，可以用于操作集合或者数组的数据。

优势：Stream流大量的结合了**Lambda**的语法风格来编程，功能强大，性能高效，代码简洁，可读性好。

##### **使用Stream流**

###### **获取流**

把数据源（集合、数组）变成 “流”；

双列集合要转成单列集合才能获取流。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660920288-1d40d2cd-7d8c-4833-b529-01efcae30b73.png)

```java
//List集合获取Stream流
ArrayList<Integer> list = new ArrayList<>();
Collections.addAll(list, 1, 2, 3, 4);
list.stream().filter(s -> s > 2).forEach(System.out::println);
//Map集合获取Stream流
HashMap<String, Integer> map = new HashMap<>();
map.put("aa", 1);
map.put("aab", 2);
map.put("aac", 3);
map.keySet().stream().filter(s -> s.length() > 2).forEach(System.out::println);
map.values().stream().filter(s -> s > 2).forEach(System.out::println);
map.entrySet().stream().filter(s -> s.getValue() < 2).forEach(System.out::println);
//数组获取Stream流
int[] arr = {1, 2, 3, 4};
Arrays.stream(arr).filter(s -> s > 3).forEach(System.out::println);
//零散数据获取Stream流
Stream.of("a", "bb", "ccc").filter(s -> s.length() > 2).forEach(System.out::println);
//也可以传递数组，但数组必须是引用类型
//基本数据类型会把整个数组当作一个元素
Stream.of(arr).forEach(System.out::println);//[I@433c675d
//引用数据类型会把数组的每个数据分别当作一个元素
Integer[] arr2 = new Integer[]{4,5,6,7};
Stream.of(arr2).forEach(System.out::println);//4567
```



###### **中间操作**

对数据做加工（筛选、转换、排序等），可以有多个步骤；

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660922672-26c2dba2-3a5e-4aa8-872f-6da103e6be6f.png)

- 筛选：filter（留下符合条件的数据）

语法：filter(数据 -> 条件)（条件为 true 则保留）

- 转换：map（把一种数据变成另一种）

语法：map(数据 -> 新数据)

- 排序：sorted（对数据排序）

自然排序（数字、字符串默认排序）：sorted()

自定义排序：sorted(Comparator.comparing(属性))

- 去重：distinct（去掉重复数据）

distinct () 去重 = 底层用 HashSet；HashSet 自定义对象去重 -> 必须重写 hashCode () + equals ()

```java
//简化版源码
Stream<T> distinct() {
    //内部用 HashSet 存，帮助去重
    return filter( new HashSet()::add );
}
```

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660923418-e8e17281-e2fe-46bf-91e1-68c3b31cf334.png)



同类型合并流：类型为原类型

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660923305-a48f041d-814d-469d-8d13-02985fa4cd83.png)



不同类型合并流：类型提升为父类

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660924432-d4a7990a-f3fc-4511-91bb-2bd790158930.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660924457-f3957820-7a90-42f5-85ee-1b4ce5f554be.png)



###### **终止操作**

得到最终结果（打印、收集到集合、统计等），只有执行**终止操作**，中间操作才会**真正运行**。

终结方法指的是调用完成后，不会返回新Stream了，没法继续使用流了。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660925976-3acfbe55-13ef-4de4-b328-0f4af9c133dd.png)

- 收集：toArray

```java
ArrayList<Integer> list = new ArrayList<>();
Collections.addAll(list,1,2,3,4,5);
//如果是空参，则返回Object类型数组
Object[] arr = list.stream().filter(s -> s > 2).toArray();
//想返回具体类型，必须用方法引用指定类型
//1.匿名内部类
Integer[] arr1 = list.stream().filter(s -> s > 2).toArray(new IntFunction<Integer[]>() {
    //value为数组长度
    //toArray作用就是创建数组，并把流中的数据放到数组中
    @Override
    public Integer[] apply(int value) {
        return new Integer[value];
    }
});
//2.lambda
Integer[] arr2 = list.stream().filter(s -> s > 2).toArray(value -> new Integer[value]);
//3.方法引用
Integer[] arr3 = list.stream().filter(s -> s > 2).toArray(Integer[]::new);
```

- 遍历：forEach（逐个处理数据，无返回值）
- 统计：count（统计数据个数）
- 查找：findFirst（获取第一个元素）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660925872-7162f93d-d04d-44e4-8b82-d83b4765f511.png)



- 收集：collect

```java
ArrayList<Person> list = new ArrayList<>();
list.add(new Person(1, "jack"));
list.add(new Person(2, "rose"));
list.add(new Person(3, "mike"));
//List集合
//1.
List<Person> list1 = list.stream().filter(person -> person.getId() > 1).collect(Collectors.toList());
//2.
List<Person> list2 = list.stream().filter(person -> person.getId() > 1).toList();
//Set集合
Set<Person> set = list.stream().filter(person -> person.getId() > 1).collect(Collectors.toSet());
//Map集合
//1.匿名内部类
Map<Integer, String> map1 = list.stream().collect(Collectors.toMap(
        //泛型一：表示流中每一个数据的类型
        //泛型二：表示Map集合中键的数据类型
        //方法apply形参：依次表示流里面的每一个数据
        //方法体：生成键的代码；返回值：已经生成的键
        new Function<Person, Integer>() {
            @Override
            public Integer apply(Person person) {
                return person.getId();
            }
        },
        new Function<Person, String>() {
            //泛型二：表示Map集合中值的数据类型
            //其他都一样
            @Override
            public String apply(Person person) {
                return person.getName();
            }
        }));
//2.lambda
Map<Integer, String> map2 = list.stream()
        .collect(Collectors.toMap(person -> person.getId(),person -> person.getName());
//3.方法引用
Map<Integer, String> map3 = list.stream()
        .collect(Collectors.toMap(Person::getId, Person::getName));
System.out.println(map2);
```



- 找最大/小：max/min

简单类型：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660926417-1654353f-9774-43fd-95f5-d9f6a7da74ae.png)

自定义类型：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660927854-2ea3c453-d868-4fb5-aac3-a51c5a2c1271.png)



#### **可变参数**

可变参数允许方法接收数量不固定的同类型参数，

它本质是**数组**的简化写法，让方法调用更简洁，无需手动创建数组传递参数。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660927729-45841bad-b874-45b2-81cd-0497d95cbad0.png)

一个方法只能有**一个**可变参数，且必须是方法的**最后一个参数**

因为可变参数会把参数全吃了

错误写法：

多个可变参数：也不知道哪个遍历是哪个可变参数的

public static void test(String... strs, int... nums) {}

可变参数不是最后一个

public static void test(int... nums, String name) {}

正确写法：

public static void printScore(String name, int... scores)



#### **不可变集合**

不可以被修改的集合，即不能修改、增加、删除集合中的内容，只能进行查询操作。

如果不想让别人改变集合的内容，就用不可变集合。

方法参数是可变参数

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660929182-0e9f1452-eaca-426f-8550-80d7365adfb9.png)

```java
List<Integer> list = List.of(1, 2, 3);
list.add(1);//会报错
Set<Integer> set = Set.of(4, 4, 5, 6);//Set集合的键不能重复，会报错
Map<String, Integer> map = Map.of("a",1,"b",2);//同样Map集合的键也不能重复
//且Map里面的of方法，参数是有上限的，最多只能传递20个参数，16个键值对
//因为Map方法有键和值两个参数，而一个方法中不能有两个可变参数，所以是有限的
//但如果把键值对写成一个参数，就可以了，比如Map.ofEntries()方法
HashMap<String, Integer> hashMap = new HashMap<>();
hashMap.put("q",1);
hashMap.put("w",2);
hashMap.put("e",3);
hashMap.put("r",4);
Set<Map.Entry<String, Integer>> entries = hashMap.entrySet();
//把entries变成数组，因为ofEntries方法的参数是数组
//如果集合的长度>数组的长度：数据在数组中放不下，此时会根据实际数据的个数，重新创建数组
//如果集合的长度<=数组的长度：数据在数组中放的下，此时不会创建新的数组，而是直接用
Map.Entry[] arr = new Map.Entry[0];
//把集合转成 Map.Entry 类型的数组，如果toArray是空参，则传Object类型
Map<Object, Object> map1 = Map.ofEntries(entries.toArray(arr));
//此时map1就是不可变集合
//简易写法
Map<String, Integer> stringIntegerMap = Map.copyOf(hashMap);
```



#### **Collections工具类**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660930383-ac3c2ae6-efc8-42de-b696-cdcea53985f1.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794660930155-ee2591e5-291c-4980-81e9-01de097995ce.png)



#### **综合实战**

##### **点名器**

###### **有概率**

要求：70%的概率随机到男生；30%的概率随机到女生。

```java
ArrayList<Integer> list = new ArrayList<>();
Collections.addAll(list, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0);
//如果抽到1，就抽男生集合；抽到0，就抽女生集合
int index = (int) (Math.random() * list.size());
ArrayList<String> nv = new ArrayList<>();
ArrayList<String> nan = new ArrayList<>();
Collections.addAll(nv, "a", "b", "c", "d");
Collections.addAll(nan, "e", "f", "g", "h");
if (list.get(index) == 1) {
    Collections.shuffle(nan);
    System.out.println(nan.getFirst());
} else {
    Collections.shuffle(nv);
    System.out.println(nv.getFirst());
}
```

###### **不重复**

要求：被点到的学生不会再被点到，但是如果班级中所有的学生都点完了，需要重新开启第二轮点名。

```java
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list,"a","s","d","f","g","h","j");
//循环次数不能写成list.size()，因为删除元素，集合长度会变
for (int i = 0; i < 10; i++) {
    Collections.shuffle(list);
    String name = list.getFirst();
    //如果抽取到某个名字，就把这个名字从集合中删去
    list.remove(name);
    if (list.isEmpty()){
        Collections.addAll(list,"a","s","d","f","g","h","j");
    }
    System.out.println(name);
}
```

###### **带权重**

txt 文件中事先准备好 80 个学生姓名，每个学生的名字独占一行。

要求1：每次被点到的学生，再次被点到的概率在原先的基础上降低一半。

举例：80个学生，点名5次，每次都点到小A，概率变化情况如下：

第一次每人概率：1.25%。

第二次小A概率：0.625%。		其他学生概率：1.2579%

第三次小A概率：0.3125%。		其他学生概率：1.261867%

第四次小A概率：0.15625%。		其他学生概率：1.2638449%

第五次小A概率：0.078125%。	其他学生概率：1.26483386%

要求2：作弊要求，第三次点名一定是张三。

答案在 IO 流的综合练习中

##### **斗地主**

分析业务需求：

总共有54张牌

点数："3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A", "2"

花色："♥", "♣", "♦", "♠"

大小王："🃏","🃏"

斗地主：发出51张牌，剩下3张做为底牌。

分析实现：

准备牌、洗牌、发牌、排序

```java
public class Game {
    static ArrayList<Integer> list = new ArrayList<>();
    static HashMap<Integer, String> card = new HashMap<>();
    static { //与类一起加载，只加载一遍，因为只需准备一副牌
        ArrayList<String> num = new ArrayList<>();
        Collections.addAll(num, "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A", "2");
        ArrayList<String> colour = new ArrayList<>();
        Collections.addAll(colour, "♥", "♣", "♦", "♠");
        int count = 1;
        //排序：如果原始数据的规律非常复杂，我们可以先手动排序让每一个数据跟唯一的序号产生对应关系
        //外循环必须要遍历数字而不是花色，不然下一个花色的3的序号会比上一个花色的4还大
        for (int i = 0; i < num.size(); i++) {
            for (int j = 0; j < colour.size(); j++) {
                card.put(count,num.get(i)+colour.get(j));
                list.add(count);
                count++;
            }
        }
        card.put(count,"大王");
        list.add(count++);
        card.put(count,"小王");
        list.add(count);
        System.out.println(card);
    }
    public Game(){
        //打乱
        Collections.shuffle(list);
        //发牌，并用TreeSet排序
        TreeSet<Integer> bottomCard = new TreeSet<>();
        TreeSet<Integer> player1 = new TreeSet<>();
        TreeSet<Integer> player2 = new TreeSet<>();
        TreeSet<Integer> player3 = new TreeSet<>();
        for (int i = 0; i < list.size(); i++) {
            if (i<3){
                bottomCard.add(list.get(i));
                continue;
            }
            if (i % 3 == 0) {
                player2.add(list.get(i));
            } else if (i % 3 == 1) {
                player3.add(list.get(i));
            } else {
                player1.add(list.get(i));
            }
        }
        System.out.print("玩家一：");
        for (Integer i : player1) {
            System.out.print(card.get(i)+" ");
        }
        System.out.println();
        System.out.print("玩家二：");
        for (Integer i : player2) {
            System.out.print(card.get(i)+" ");
        }
        System.out.println();
        System.out.print("玩家三：");
        for (Integer i : player3) {
            System.out.print(card.get(i)+" ");
        }
        System.out.println();
        System.out.print("底牌：");
        for (Integer i : bottomCard) {
            System.out.print(card.get(i)+" ");
        }
    }
}
```







### **异常**

#### **认识异常**

比如你想打开一个文件，但这个文件不存在；你想计算 5 ÷ 0，但除数不能为 0；

获取 ArrayList 里的第 10 个元素，但集合里其实只有 4 个元素……

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662172093-cc50c863-af1d-4471-8b89-524db01c2cfc.png)

- 编译时异常：没有继承 RuntimeExcpetion 的异常，编译阶段就会有错误提示。

比如文件不存在

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662171026-a173a2d4-885c-424e-a070-f5dcabe0359c.png)

- 运行时异常：继承自RuntimeException的异常或其子类，编译阶段不报错，运行时出现的。

比如索引越界：IndexOutOfBoundsException；空指针异常：NullPointerException；除以 0：ArithmeticException

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662171283-ecd6adad-8d23-48b4-aad8-6e59bc9dcf9b.png)

当程序出现异常时，自己没有处理（try-catch），JVM 会自动处理：

1. 把异常的名称，异常原因及异常出现的位置等信息输出在了控制台
2. 程序停止执行，异常下面的代码不会再执行了



#### **异常的作用**

- 作用1：

异常是用来定位程序 bug 的关键信息。

- 作用2：

可以作为方法内部的一种特殊返回值，以便通知上层调用者，方法有什么执行问题。

此作用必须用 **throw** 关键字手动抛出异常。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662171527-71396ed5-4aaf-46cb-8fde-bbc754eb1285.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662171436-5cb4934c-f3f0-4d03-bf85-f86f14241021.png)



#### **自定义异常**

自定义异常就是**自己定义的异常类**，用来描述程序中一些 “**特定场景的错误**”。Java无法为这个世界上全部的问题都提供异常类来代表，如果企业自己的某种问题，想通过异常来表示，以便用异常来管理该问题，那就需要自己来定义异常类了。



怎么定义一个自定义异常？

1. 新建一个类，让它继承 Java 自带的异常类（通常是 Exception 或 RuntimeException）。

继承 Exception：自定义的是编译时异常（必须强制处理）。

继承 RuntimeException：自定义的是运行时异常（不需要强制处理）。

1. 重写父类构造方法（通常是两个：一个无参构造器，一个带错误信息的构造器）。
2. 通过 throw new 异常类(xxx)创建异常对象并抛出。



- 编译时异常

因为是编译时异常，抛出时必须用 throws 声明，调用时必须用 try-catch 处理

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662172614-050282a2-c120-4372-9158-7555aa712254.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662172743-3b6a4f9c-aef7-4f96-96a6-fe74c7a0f8b6.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662172501-7f7280ac-4e94-40d3-bf02-e5b6980ad7eb.png)



- 运行时异常

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662172784-2b4521a9-b1ca-4378-9791-1b23cafd7c39.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662173634-df1a324d-5e12-4092-a012-6d710f730d9a.png)



#### **异常的处理方案**

- 方案一：能处理就捕获（try-catch）；不能处理就抛出（throws）

目的：当代码出现异常时，可以让程序继续往下执行。不然 JVM 默认执行会终止程序，不会执行后续代码。

用 try-catch 捕获异常，自己处理（比如提示 “文件没找到”）。

try 块：监控可能出异常的代码

catch 块：处理特定类型的异常



1. 用 try-catch

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662173762-9ac3f551-a912-468f-95dc-e995790e934b.png)

不用 try-catch

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662173705-a02f7e1d-def4-432e-a1a0-b1929932ca5d.png)



try 里面一旦出异常，直接跳到匹配的 catch，try 中下面的代码不会执行

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662174086-bfd016db-f76e-4a65-8152-3adc41c9e28d.png)



如果我们要捕获多个异常，这些异常中如果存在父子关系的话，那么父类一定要写在下面。

因为父类（比如 Exception） 直接全捕获了，下面的子类就无法捕获了。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662174138-31332a2a-05c9-4cc9-bedd-8e117d4cde96.png)



1. 用 throws 声明 “这个异常我不处理，交给调用者处理”。

目的：告诉调用者出错了

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662175279-a4ee953c-40e0-4dea-a6a7-52d66dd5f8a3.png)

```java
public static void main(String[] args) {
    int[] arr = null;
    try {
        method(arr);
    } catch (Exception e) {
        System.out.println("数组为null");
    }
    System.out.println("执行没");
}

private static void method(int[] arr) {
    if (arr == null) {
        throw new NullPointerException();
    }
    for (int i = 0; i < arr.length; i++) {
        System.out.println(arr[i]);
    }
}
```



![img](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662398891-9514ca38-1a28-4e0d-9ee6-f0ce7187b2ac.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662175225-3f478113-6533-449d-9e22-dfe90bc25723.png)

- 方案二：最外层捕获异常后，尝试重新修复

无论定义的是编译时异常还是运行时异常，main 方法必须用 **try-catch**，如果 main 方法不处理异常，异常会被抛给 JVM（Java 虚拟机），JVM 的默认处理方式是**终止程序**并打印异常信息，这会导致 while(true) 循环中断，无法实现 “重复输入” 的功能。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662175671-8ce193c7-bdfa-410d-a848-45db478681be.png)



#### **异常中的方法**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662175585-1b6fa380-12e5-4a3f-b7df-8f0d396b0c95.png)

printStackTrace()：在底层是利用 System.err.println 进行输出，把异常的错误信息以红色字体输出在控制台。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662176300-a39df3a0-1322-4cd7-8fe3-6dc878f4acd6.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662176725-816b5600-737d-4649-8608-ed97bcf9a5b9.png)





### **File**

File 对象就表示一个路径，可以是文件的路径、也可以是文件夹的路径。

这个路径可以是存在的，也允许是不存在的。

#### **构造方法**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663211129-648d6c2c-a3ad-4d9f-bf67-3660387c4a70.png)

```java
//根据路径变成File对象
File f1 = new File("D:\\ideaxiazai\\code_iade\\gdou\\TEAM\\src\\Javase\\Game1.java");
//父级路径
String parent = "D:\\ideaxiazai\\code_iade\\gdou\\TEAM\\src\\Javase";
//子级路径
String child = "Game2.java";
File f2 = new File(parent, child);
//File对象路径与String路径拼接
File parent2 = new File("D:\\ideaxiazai\\code_iade\\gdou\\TEAM\\src\\Javase");
String child2 = "Game3.java";
File f3 = new File(parent2, child2);
```

#### **成员方法**

##### **判断与获取**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663211439-eb354e1d-4686-4449-8fcf-3ac636a5539e.png)

- length()

细节1：这个方法只能获取文件的大小，单位是字节，如果单位想要 MB、GB，可以不断的除以 1024。

细节2：这个方法无法获取文件夹的大小，如果我们要获取一个文件夹的大小，需要把这个文件夹里面所有的文件大小都累加在一起。



##### **创建与删除**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663211214-662207ba-c874-478b-9d89-88904f498876.png)



- mkdirs()

既可以创建单级文件夹，也可以创建多级文件夹

- delete()

如果删除的是文件，则直接删除，不走回收站；

如果删除的是空文件夹，则直接删除，不走回收站；

如果删除的是有内容的文件夹，则删除失败。



##### **获取并遍历**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663211430-acb8bf73-f9b3-4630-835f-e35f95daf491.png)

- listFiles()

当调用者File表示的路径不存在时，返回 null；

当调用者File表示的路径是文件时，返回 null；

当调用者File表示的路径是一个空文件夹时，返回一个长度为 0 的数组；

当调用者File表示的路径是一个有内容的文件夹时，将里面所有文件和文件夹的路径放在File数组中返回（包含隐藏文件）；

当调用者File表示的路径是需要权限才能访问的文件夹时，返回 null。



#### **小练习**

- 找单级文件夹中以 .jpg 结尾的文件（不需要考虑子文件夹）

```java
File f1 = new File("C:\\Users\\ashih\\Documents\\B站网页\\B站网页\\素材\\封面");
for (File file : f1.listFiles()) {
    System.out.println(file.getName());
    System.out.println((file.getName().endsWith(".jpg")));
}
```

- 找多级文件夹中中以 .avif 结尾的文件（需要考虑子文件夹）

```java
public static void main(String[] args) throws Exception {
    File f1 = new File("C:\\Users\\ashih\\Documents");
    run(f1);
}

private static void run(File f1) {
    if (f1.isFile()){
        if (f1.getName().endsWith("avif")){
            System.out.println(f1.getName());
        }
        return;
    }
    File[] files = f1.listFiles();
    if (files != null) {
        for (File file : files) {
            run(file);
        }
    }
}
```

- 统计一个文件夹中每种文件的个数并打印。（考虑子文件夹）

法一：网课标准做法，不知道有多少种文件类型，用 Map 集合存储

```java
public static void main(String[] args) throws Exception {
    File f1 = new File("C:\\Users\\ashih\\Documents");
    System.out.println(run3(f1));
}

private static Map<String, Integer> run3(File f1) {
    //键：文件类型名 值：数量
    HashMap<String, Integer> map = new HashMap<>();
    File[] files = f1.listFiles();
    if (files != null) {
        for (File file : files) {
            if (file.isFile()) {
                String[] split = file.getName().split("\\.");
                if (split.length >= 2) {
                    String s = split[split.length - 1];
                    if (map.containsKey(s)) {
                        Integer val = map.get(s);
                        val++;
                        map.put(s, val);
                    } else {
                        map.put(s, 1);
                    }
                }
            } else if (file.isDirectory()) {
                Map<String, Integer> addMap = run3(file);
                for (Map.Entry<String, Integer> entry : addMap.entrySet()) {
                    String key = entry.getKey();
                    if (map.containsKey(key)) {
                        Integer val = map.get(key);
                        val += addMap.get(key);
                        map.put(key, val);
                    } else {
                        map.put(key, addMap.get(key));
                    }

                }
            }
        }
    }
    return map;
}
```



法二：如果固定 txt、png 等已知文件类型，直接用 endsWith()

```java
public static void main(String[] args) throws Exception {
    File f1 = new File("C:\\Users\\ashih\\Documents");
    int[] count = new int[3];
    run2(f1,count);
    System.out.println("jpg：" + count[0]);
    System.out.println("avif：" + count[1]);
    System.out.println("png：" + count[2]);
    /*
    易错：不能传基本数据类型。
    int jpgCount = 0;
    int avifCount = 0;
    int pngCount = 0;
    run2(f1,jpgCount,avifCount,pngCount);
    因为把0传给方法，方法里++，只改方法内部的副本，不会改外面的变量，
    方法结束，这个 1 就丢了！回到外面，变量还是为0。
    所以要传数组。
    */
}

private static void run2(File f1,int[] arr) {
    if (f1.isFile()){
        if (f1.getName().endsWith("avif")){
            arr[1]++;
            System.out.println(f1.getName());
        } else if (f1.getName().endsWith("jpg")) {
            arr[0]++;
            System.out.println(f1.getName());
        } else if (f1.getName().endsWith("png")) {
            arr[2]++;
            System.out.println(f1.getName());
        }
        return;
    }
    File[] files = f1.listFiles();
    if (files != null) {
        for (File file : files) {
            run2(file,arr);
        }
    }
}
```



法三：如果只统计一个数量，可以用基本数据类型

```java
public static void main(String[] args) throws Exception {
    File f1 = new File("C:\\Users\\ashih\\Documents");
    System.out.println(run2(f1));
}

private static int run2(File f1) {
    int avifCount = 0;
    File[] files = f1.listFiles();
    if (files != null) {
        for (File file : files) {
            if (file.isFile()) {
                if (file.getName().endsWith("avif")) {
                    avifCount++;
                    System.out.println(file.getName());
                }
            } else {
                avifCount += run2(file);
            }
        }
    }
    return avifCount;
}
```



### **IO流**

#### **概念**

存储和读取数据的解决方案

字节流：可以操作所有类型的文件

字符流：只能操作纯文本文件

纯文本文件：用 windows 系统自带的记事本打开并且能读懂的文件

输入流（InputStream / Reader）= 读数据；输出流（OutputStream / Writer）= 写数据

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663211457-19dbf3da-35a4-4359-9b8b-f27ea6e82a2e.png)

#### **字节流**

- 使用场景：拷贝任意类型的文件

##### **文件字节输入流**

把电脑里的文件（比如文本、图片、视频），以 **“字节”** 为最小单位，一点点 “读” 到程序里。

- 步骤

1. 创建字节输入流对象

如果文件不在，直接报错

2. 读数据

注意：read() 无参方法返回的是字节数据本身；

而 read(bytes) 有参方法返回的是读取字节的个数。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663213052-09991dd2-3c85-4682-bc99-4b9070cd33a8.png)

3. 释放资源

如果开了多个资源，先开的最后关闭。

- 单字节读取

```java
//创建字节输入流对象
FileInputStream fis = new FileInputStream("TEAM\\src\\data");//abcd
//单字节读取
System.out.println((char) fis.read());//a，需要强转成char，不然打印的是ASCII码值97
System.out.println((char) fis.read());//b
System.out.println((char) fis.read());//c
System.out.println((char) fis.read());//d
System.out.println(fis.read());//-1，表示没有数据可读了
//释放资源
fis.close();
```

- 循环读取（错误版）

判断的是第一次调用读取的字节（比如a），而打印的是第二次调用读取的字节（比如b），所以最后会打印：bd

```java
FileInputStream fis = new FileInputStream("TEAM\\src\\data");//abcd
while (fis.read()!=-1){  // 第1次调用read()：读了一个字节
    System.out.println((char) fis.read());  // 第2次调用read()：又读了一个字节
}
fis.close();
```

- 循环读取（正确版）

```java
FileInputStream fis = new FileInputStream("TEAM\\src\\data");//abcd
int len;//存储读取到的值，既用来判断，又用来打印
while ((len = fis.read()) != -1) {
    System.out.println((char) len);
}
fis.close();
```

中文在 UTF-8 编码 里占 3 个字节，在 GBK 编码 里占 2 个字节，一次读取一个字节一定会乱码

每次读取多个字节，性能会得到提升：因为每次从硬盘读取多个字节到程序（程序运行在内存中），

可以减少硬盘和内存的交互次数，从而提升性能。

但是读中文还会乱码

```java
FileInputStream fis = new FileInputStream("TEAM\\src\\data");//abcd
byte[] bytes = new byte[3];
int len;
while ((len = fis.read(bytes)) != -1) {
    /*注意：
    1. read() 无参方法返回的是字节数据本身；read(bytes)有参方法返回的是读取字节的个数，
    所以不能写成 System.out.println((char) len);
    2. 也不能写成：String s = new String(bytes);
    不然会打印成abc dbc这种脏数据
    第一次读取abc，len等于3，数组[ a, b, c ]
    第二次读取d，len等于1，数组被新数据覆盖[ d, b, c ]
    多出来的bc是脏数据，所以只取前一个有效数据
    */
    String s = new String(bytes,0,len);
    System.out.println(s);
}
fis.close();
```

使用字节流读取中文，如何保证输出不乱码，怎么解决？

- 定义一个与文件一样大的字节数组，一次性读取完文件的全部字节

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663212493-11f45369-1b60-4c90-b63f-d4a48cafa835.png)

如果文件过大，创建的字节数组也会过大，可能引起内存溢出。

读取文本应该用**字符流**；字节流适合做数据的转移，比如：**文件复制**



##### **文件字节输出流**

- 步骤

1. 创建字节输出流对象

参数是字符串表示的路径或者是 File 对象都是可以的；

如果文件不存在会创建一个新的文件，但是要保证父级路径是存在的；

如果文件已经存在，则会清空文件。

2. 写数据

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663212935-237bd94e-9c61-4413-bd30-da99c97e787c.png)

3. 释放资源

```java
//创建字节输出流对象
FileOutputStream fos = new FileOutputStream("TEAM\\src\\data");
//写一个字节
fos.write(97);//a
fos.write(98);//b
fos.write('c');//c

//写一个字节数组
byte[] bytes = {97, 98, 'c', 'd'};//abcd
fos.write(bytes);

//写一个字节数组的部分数据
//从第一个字节开始，读2个字节
fos.write(bytes,0,2);//ab

//换行
fos.write("\r\n".getBytes());

//字符串直接转 byte 数组
fos.write("abcd".getBytes());

//释放资源
fos.close();
```

- 在路径最后加个 true，数据不会被覆盖，而是追加

```java
FileOutputStream fos = new FileOutputStream("TEAM\\src\\data",true);
```



##### **文件复制**

```java
FileInputStream fis = new FileInputStream("TEAM\\src\\data");//abcd
FileOutputStream fos = new FileOutputStream("TEAM\\src\\data2");
byte[] bytes = new byte[3];
int len;
while ((len = fis.read(bytes)) != -1) {
    fos.write(bytes,0,len);
}
//先开的最后关
fos.close();
fis.close();
```



##### **资源释放问题**

仅了解

业务代码 不写 try-catch，用 Spring 的全局异常捕获器来统一抓

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663212774-a668ac05-33d7-4e36-9695-84bf6c37aeaa.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663213009-67a2688d-6dd1-4c63-82dc-4da4e4917cc0.png)



#### **字符集**

在计算机中，任意数据都是以二进制的形式来存储的

计算机中最小的存储单元是一个字节

ASCII 字符集中，一个英文占一个字节



- GBK 字符集完全兼容 ASCII 字符集

简体中文版 Windows，默认使用 GBK 字符集

一个英文占一个字节，二进制第一位是 0

一个中文占两个字节，二进制高位字节的第一位是1



- Unicode 字符集的 UTF-8 编码格式

一个英文占一个字节，二进制第一位是0

一个中文占三个字节，二进制第一位是1



- 中文乱码原因

原因1：读取数据时未读完整个汉字

原因2：编码和解码时的方式不统一



![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663214342-486a34f0-a5b9-49f9-8e5e-2e3dc4a78013.png)

```java
String s = "as我啊";
//使用默认方法编码
byte[] bytes1 = s.getBytes();
//使用指定方法编码
byte[] bytes2 = s.getBytes("GBK");

//使用默认方法解码
String s1 = new String(bytes1);
//使用指定方法解码
String s2 = new String(bytes2, "GBK");
```



#### **字符流**

##### **概念**

字符流的底层其实就是字节流

字符流 = 字节流 + 字符集

- 特点

输入流：一次读一个字节，遇到中文时，一次读多个字节（中文读取几个字节看编码方式）

输出流：底层会把数据按照指定的编码方式进行编码，变成字节再写到文件中

- 使用场景

对于纯文本文件进行读写操作



##### **文件字符输入流**

- 步骤

1. 创建字符输入流对象

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663214002-e2d489ad-fa9d-4285-b52a-cf09e2505a69.png)

2. 读取数据

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663214081-4f188288-595d-498b-8b7d-a2bbf5e72290.png)

3. 释放资源

- 示例

注意：字节流读取数据不需要强转，而字符流需要强转成 char 类型

字节流：read () 返回的是数据本身

字符流：read () 返回的是字符编码

```java
//一次读取单个字符
FileReader fr = new FileReader("TEAM\\src\\data");
int len;
while ((len = fr.read()) != -1) {
    System.out.print((char) len);
}
fr.close();
//一次读取四个字符
FileReader fr = new FileReader("TEAM\\src\\data");
int len;
char[] chars = new char[4];//注意是char数组，不是byte数组
while ((len = fr.read(chars)) != -1) {
    String s = new String(chars, 0, len);
    System.out.println(s);
}
fr.close();
```

##### **文件字符输出流**

1. 创建字符输出流对象

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663214595-080d2001-eeed-4dcd-8176-7b24b2438597.png)



2. 写数据

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663214778-59fafcc2-8823-48fc-92b0-92ddf63a5f0d.png)



字符输出流写出数据后，必须刷新流，或者关闭流，写出去的数据才能生效

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663215083-57751043-a505-4f3c-aaf8-04b5868d4396.png)



#### **小练习**

- 拷贝文件夹，需考虑子文件夹

```java
public static void main(String[] args) throws Exception {
    File f1 = new File("TEAM\\src\\a");
    File f2 = new File("TEAM\\src\\copy");
    run(f1, f2);
}

private static void run(File f1, File f2) throws Exception {
    f2.mkdirs();
    File[] files = f1.listFiles();
    if (files != null) {
        for (File file : files) {
            if (file.isFile()) {
                FileInputStream fis = new FileInputStream(file);
                FileOutputStream fos = new FileOutputStream(new File(f2,file.getName()));
                int len;
                while ((len = fis.read()) != -1) {
                    fos.write(len);
                }
                fos.close();
                fis.close();
            } else {
                f2.mkdirs();
                run(file, new File(f2,file.getName()));
            }
        }
    }
}
```

- 为了保证文件的安全性，就需要对原始文件进行加密存储，再使用的时候再对其进行解密处理。

加密原理：对原始文件中的每一个字节数据进行更改，然后将更改以后的数据存储到新的文件中。

解密原理：读取加密之后的文件，按照加密的规则反向操作，变成原始文件。

```java
//原始文件
FileInputStream fis = new FileInputStream("TEAM\\src\\a\\b.txt");
//加密文件
FileOutputStream fos = new FileOutputStream("TEAM\\src\\a\\ency.txt");
//加密
int len;
while ((len = fis.read()) != -1) {
    //一个数字异或两次就会变回本身
    //所以加密就异或一次；解密就异或两次
    fos.write(len ^ 10);
}
fos.close();
fis.close();
//加密文件
FileInputStream fis = new FileInputStream("TEAM\\src\\a\\ency.txt");
//解密文件
FileOutputStream fos = new FileOutputStream("TEAM\\src\\a\\redu.txt");
//解密
int len;
while ((len = fis.read()) != -1) {
    //一个数字异或两次就会变回本身
    //所以加密就异或一次；解密就异或两次
    fos.write(len ^ 10);
}
fos.close();
fis.close();
```

- 修改文件数据

文本文件中有以下的数据：2-1-9-4-7-8

将文件中的数据进行排序，变成以下的数据：1-2-4-7-8-9

法一：

```java
FileReader fr = new FileReader("TEAM\\src\\a\\b.txt");
StringBuilder sb = new StringBuilder();
int len;
while ((len = fr.read()) != -1) {
    sb.append((char) len);
}
String str = sb.toString();
String[] split = str.split("-");
ArrayList<Integer> list = new ArrayList<>();
for (String s : split) {
    int num = Integer.parseInt(s);
    list.add(num);
}
Collections.sort(list);
FileWriter fw = new FileWriter("TEAM\\src\\a\\b.txt");
for (int i = 0; i < list.size(); i++) {
    if (i == list.size()-1){
        //在后面加空字符串
        //不然就会类似97对应a，这样输出
        fw.write(list.get(i)+"");
    }else {
        fw.write(list.get(i)+"-");
    }
}
fw.close();
fr.close();
```

法二：简洁

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663215738-d833f2dc-fa8b-4fcd-9d4c-888da1f49017.png)



#### **缓冲流**

##### **缓冲字节流**

作用：增加了缓冲区，可以提高字节输入流读取数据的性能

原理：缓冲字节输入流自带了 8KB（8192字节） 缓冲池；缓冲字节输出流也自带了 8KB 缓冲池。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663216252-19bd40c5-0215-4322-bba3-db3f46247b71.png)

- 原理

把数据从硬盘放到缓冲区，一次最多可放 8192 个字节数据，中间的变量 b 再从输入流的缓冲区，

把数据放到输出流的缓冲区，因为它们都是在内存中，所以运行速度更快，输出流的缓冲区放满 8192 个字节了，

再把数据放到目的地的硬盘中。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663216360-6854b2d2-6013-4246-a144-7c1fcb43591b.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663216306-9bb24ded-7a7e-46d2-b368-580810c2b1f6.png)



##### **缓冲字符流**

- 构造方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663216355-24bcbaec-ce16-4d88-a0f6-40641b7a2f6c.png)

- 成员方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663217257-65846364-0961-4d29-928f-21b3d5dc5378.png)

不同平台的换行符：Windows：\r\n		Linux：\n		Mac：\r

关流时只需关缓冲流，因为缓冲流 close() 方法底层会关把基本流关闭

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663217798-780b0c92-b849-4cfa-9076-dbde7142c379.png)

按照行读取

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663217520-27bcdbfa-5556-4f50-baa9-f3e1fdc96602.png)



##### **小练习**

- 给出师表排序

```java
3.侍中、侍郎郭攸之、费祎、董允等，此皆良实，志虑忠纯，是以先帝简拔以遗陛下。愚以为宫中之事，事无大小，悉以咨之，然后施行，必得裨补阙漏，有所广益。
8.愿陛下托臣以讨贼兴复之效，不效，则治臣之罪，以告先帝之灵。若无兴德之言，则责攸之、祎、允等之慢，以彰其咎；陛下亦宜自谋，以咨诹善道，察纳雅言，深追先帝遗诏，臣不胜受恩感激。
4.将军向宠，性行淑均，晓畅军事，试用之于昔日，先帝称之曰能，是以众议举宠为督。愚以为营中之事，悉以咨之，必能使行阵和睦，优劣得所。
2.宫中府中，俱为一体，陟罚臧否，不宜异同。若有作奸犯科及为忠善者，宜付有司论其刑赏，以昭陛下平明之理，不宜偏私，使内外异法也。
1.先帝创业未半而中道崩殂，今天下三分，益州疲弊，此诚危急存亡之秋也。然侍卫之臣不懈于内，忠志之士忘身于外者，盖追先帝之殊遇，欲报之于陛下也。诚宜开张圣听，以光先帝遗德，恢弘志士之气，不宜妄自菲薄，引喻失义，以塞忠谏之路也。
9.今当远离，临表涕零，不知所言。
6.臣本布衣，躬耕于南阳，苟全性命于乱世，不求闻达于诸侯。先帝不以臣卑鄙，猥自枉屈，三顾臣于草庐之中，咨臣以当世之事，由是感激，遂许先帝以驱驰。后值倾覆，受任于败军之际，奉命于危难之间，尔来二十有一年矣。
7.先帝知臣谨慎，故临崩寄臣以大事也。受命以来，夙夜忧叹，恐付托不效，以伤先帝之明，故五月渡泸，深入不毛。今南方已定，兵甲已足，当奖率三军，北定中原，庶竭驽钝，攘除奸凶，兴复汉室，还于旧都。此臣所以报先帝而忠陛下之职分也。至于斟酌损益，进尽忠言，则攸之、祎、允之任也。
5.亲贤臣，远小人，此先汉所以兴隆也；亲小人，远贤臣，此后汉所以倾颓也。先帝在时，每与臣论此事，未尝不叹息痛恨于桓、灵也。侍中、尚书、长史、参军，此悉贞良死节之臣，愿陛下亲之信之，则汉室之隆，可计日而待也。
```

法一：

```java
BufferedReader br = new BufferedReader(new FileReader("TEAM/src/a/b.txt"));
BufferedWriter bw = new BufferedWriter(new FileWriter("TEAM/src/a/c.txt"));
String line;
ArrayList<String> list = new ArrayList<>();
while ((line = br.readLine()) != null) {
    list.add(line);
}
//排序
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        String[] arr1 = o1.split("\\.");
        String[] arr2 = o2.split("\\.");
        return Integer.parseInt(arr1[0]) - Integer.parseInt(arr2[0]);
    }
});
for (String s : list) {
    bw.write(s);
    bw.newLine();
}
bw.close();
br.close();
```

法二：

```java
BufferedReader br = new BufferedReader(new FileReader("TEAM/src/a/b.txt"));
BufferedWriter bw = new BufferedWriter(new FileWriter("TEAM/src/a/c.txt"));
String line;
TreeMap<Integer, String> map = new TreeMap<>();
while ((line = br.readLine()) != null) {
    String[] arr = line.split("\\.");
    map.put(Integer.parseInt(arr[0]),line);
}
for (Map.Entry<Integer, String> entry : map.entrySet()) {
    String value = entry.getValue();
    bw.write(value);
    bw.newLine();
}
//排序
bw.close();
br.close();
```

- 实现一个验证程序运行次数的小程序

程序运行演示如下：

第一次运行控制台输出：欢迎使用本软件，第1次使用免费~

第二次运行控制台输出：欢迎使用本软件，第2次使用免费~

第三次运行控制台输出：欢迎使用本软件，第3次使用免费~

第四次及之后运行控制台输出：本软件只能免费使用3次，欢迎您注册会员后继续使用~

```java
BufferedReader br = new BufferedReader(new FileReader("TEAM/src/a/b.txt"));
String line = br.readLine();
int num = Integer.parseInt(line);
num++;
if (num <= 3) {
    System.out.println("欢迎使用本软件，第" + num + "次使用免费");
} else {
    System.out.println("本软件只能免费使用3次，欢迎您注册会员后继续使用");
}
BufferedWriter bw = new BufferedWriter(new FileWriter("TEAM/src/a/b.txt"));
//FileWriter 一打开同一个文件，文件就会被清空，所以不能写在上面
//什么时候用，什么时候创建；不用即关闭。
bw.write(num + "");
bw.close();
```



#### **其他流**

##### **转换流**

转换流是是字符流和字节流之间的桥梁。

创建转换输入流对象，需要包装一个字节流，包装后就变成字符流。

输出流是把字符流转化为字节流

应用：字节流想使用字符流的方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663217816-80e5a4b8-451b-4e73-a865-38b302658721.png)

```java
//指定字符集编码（JDK11淘汰）
InputStreamReader isr = new InputStreamReader(new FileInputStream("TEAM/src/gbkfile.txt"),"GBK");
int len;
while ((len = isr.read()) != -1) {
    System.out.print((char) len);
}
isr.close();
//JDK11后的替代方案
FileReader fr = new FileReader("TEAM/src/gbkfile.txt", Charset.forName("GBK"));
int len;
while ((len = fr.read()) != -1) {
    System.out.print((char) len);
}
fr.close();
```



###### **小练习**

- 将 GBK 文件转为 UTF-8

```java
//JDK11后的替代方案
FileReader fr = new FileReader("TEAM/src/gbkfile.txt", Charset.forName("GBK"));
int len;
while ((len = fr.read()) != -1) {
    System.out.print((char) len);
}
fr.close();
```

- 利用字节流读取文件中的数据，每次读一整行，而且不能出现乱码

```java
//1.字节流在读取中文的时候，是会出现乱码的，但是字符流可以搞定
InputStreamReader isr = new InputStreamReader(new FileInputStream("TEAM/src/a/c.txt"));
//2,字节流里面是没有读一整行的方法的，只有字符缓冲流才能搞定
BufferedReader br = new BufferedReader(isr);
String line;
while ((line = br.readLine()) != null) {
    System.out.println(line);
}
br.close();
```



##### **序列化流**

把内存里的对象 → 变成一串可保存、可传输的字节 / 字符串，这个过程就叫序列化。

反过来：把字节 / 字符串 → 变回内存里的对象 → 叫反序列化。

条件：需要让 JavaBean 类实现 Serializable 接口。

Serializable 接口里面是没有抽象方法，是标记型接口。一旦实现了这个接口，那么就表示当前的类可以被序列化。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663218214-5115583d-d5b6-4b88-a378-6f16c405d794.png)

- 反序列化流

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663218925-d9d71fb7-d8fe-4324-84dc-f0a030394b1a.png)

```java
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("TEAM/src/a/b.txt"));
Person p1 = new Person("jack", 22);
oos.writeObject(p1);
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("TEAM/src/a/b.txt"));
System.out.println(ois.readObject());
ois.close();
oos.close();
```



实现 Serializable 接口，Java 底层会根据类的数据进行计算，计算出一个 long 类型的序列号。

当创建对象时，对象包含了所属类的序列号，写进的文件也会带序列号。

如果此时修改了 JavaBean 的代码，就会重新计算一个序列号。

文件中的版本号，跟 JavaBean 的版本号不匹配，就会报错。

为了不报错，就要手动定义版本号，Java 底层就不会再计算了。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663219772-2df093b7-26ec-40b1-9153-78340a1a3631.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663219599-8db275f1-8ed1-45ff-a666-d9e98f82fe87.png)

序列化多个对象，会把对象放到集合中，然后直接序列化集合。

```java
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("TEAM/src/a/b.txt"));
Person p1 = new Person("jack", 22);
Person p2 = new Person("rose", 99);
ArrayList<Person> people = new ArrayList<>();
people.add(p1);
people.add(p2);
oos.writeObject(people);
oos.close();
//读取就不用循环读了
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("TEAM/src/a/b.txt"));
ArrayList<Person> list = (ArrayList<Person>) ois.readObject();
System.out.println(list);
ois.close();
```



##### **打印流**

打印流只有输出流，只能写不能读

PrintStream（字节打印流）	PrintWriter（字符打印流）

作用：打印流可以实现更方便、更高效的打印数据出去，能实现打印啥出去就是啥出去。

- 字节打印流

构造器

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663219789-4e6cb5b9-e2da-4992-af37-16aa839137bc.png)

成员方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663219828-a55bb764-8d6e-4252-b16f-1dc84817d19c.png)

```java
PrintStream ps = new PrintStream("TEAM/src/a/b.txt");
ps.println(97);
ps.print(true);
ps.printf("%s发顺丰","商家");
ps.close();
System.out.println(666);
PrintStream out = System.out;
out.println(666);
//public static final PrintStream out = null;
//out是System类的静态变量，属于字节打印流
//特殊的打印流，默认指向控制台，且不能关闭
```

- 字符打印流

字符流底层有缓冲区，想要自动刷新需要开启。

构造器

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663220874-d91718e4-2d70-4219-b891-6d1851b85824.png)

成员方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663220942-94af0c28-f7ea-43b2-8b2d-32950e8a8265.png)

```java
PrintWriter pw = new PrintWriter("TEAM/src/a/b.txt");
pw.write("是否dasd");
pw.flush();//强制把缓冲区数据刷到文件，不然文件为空
pw.close();//关闭会自动刷新
```



##### **压缩流**

###### **解压缩流**

解压本质：压缩包的每一个文件或文件夹都是 ZipEntry 对象，把每一个 ZipEntry 按照层级拷贝到本地另一个文件夹中。

```java
public static void main(String[] args) throws Exception {
    //压缩包路径
    File source = new File("D:\\BaiduNetdiskDownload\\阿里云.zip");
    //目的地
    File dest = new File("D:\\BaiduNetdiskDownload");
    unZip(source, dest);
}

private static void unZip(File source, File dest) throws Exception {
    //创建一个解压缩流用来读取压缩包中的数据
    ZipInputStream zis = new ZipInputStream(new FileInputStream(source));
    //获取压缩包的zipEntry对象
    ZipEntry entry;
    while ((entry = zis.getNextEntry()) != null) {
        System.out.println(entry);
        if (entry.isDirectory()) {
            File file = new File(dest, entry.getName());
            file.mkdirs();
        }else {
            FileOutputStream fos = new FileOutputStream(new File(dest, entry.getName()));
            int len;
            while ((len = zis.read()) != -1) {
                fos.write(len);
            }
            fos.close();
            //表示压缩包的一个文件处理完毕
            zis.closeEntry();
        }
    }
    zis.close();
}
```



###### **压缩流**

压缩本质：把每一个（文件/文件夹）看成 ZipEntry 对象放到压缩包中

- 压缩单文件

```java
public static void main(String[] args) throws Exception {
    //要压缩的文件
    File source = new File("D:\\BaiduNetdiskDownload\\阿里云.txt");
    //压缩后的目的地
    File dest = new File("D:\\BaiduNetdiskDownload");
    toZip(source,dest);
}

private static void toZip(File source, File dest) throws Exception {
    //压缩流
    ZipOutputStream zos = new ZipOutputStream(new FileOutputStream(new File(dest,source.getName().split("\\.")[0]+".zip")));
    ZipEntry entry = new ZipEntry(source.getName());//参数只能是文件名或文件夹名，不能是绝对路径
    zos.putNextEntry(entry);//打开一个文件
    //把文件写进压缩包
    FileInputStream fis = new FileInputStream(source);
    int len;
    while ((len = fis.read()) != -1) {
        zos.write(len);//往entry里写入数据
    }
    zos.closeEntry();
    zos.close();
}
```



- 压缩文件夹

```java
public static void main(String[] args) throws Exception {
    //要压缩的文件夹
    File source = new File("D:\\BaiduNetdiskDownload\\测试压缩");
    //压缩后的目的地
    File dest = new File(source.getParent(), source.getName() + ".zip");
    //压缩流
    ZipOutputStream zos = new ZipOutputStream(new FileOutputStream(dest));
    //获取source里每一个文件，变成ZipEntry对象，放入压缩包中
    toZip(source, zos, source.getName());
    zos.close();
}

private static void toZip(File source, ZipOutputStream zos, String name) throws Exception {
    File[] files = source.listFiles();
    if (files != null) {
        for (File file : files) {
            if (file.isDirectory()) {
                //此时父路径要变化
                toZip(file, zos, name + "\\" + file.getName());
            } else {
                //name是个文件夹名
                //文件夹不能写入数据，文件才行
                //所以应该传的是文件名，而不是文件夹名
                //但又需要在压缩包中创建子文件夹，就要传name进去
                ZipEntry entry = new ZipEntry(name + "\\" + file.getName());
                zos.putNextEntry(entry);
                FileInputStream fis = new FileInputStream(file);
                int len;
                while ((len = fis.read()) != -1) {
                    zos.write(len);//往entry里写入数据
                }
                fis.close();
                zos.closeEntry();
            }
        }
    }
}
```



#### **Commons-IO**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663222246-1d9d65a8-f588-4cb2-a4e3-80a4c360ecfe.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663221663-2bfab5c4-dffc-403d-a834-6482a17fc1c6.png)



#### **HuTool**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663222304-6776b292-70e7-4128-8000-9177a03a21dd.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663222580-ffe3bd4d-ebb0-496f-a969-31bf9889e6a0.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663222631-214fdaf8-636c-4a0c-847f-0f79d843440a.png)



- 项目实战

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663223741-e88954a6-bc68-4587-a9c6-c8b1ba42b6a9.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663224008-579e8efe-3e7a-41c0-b65e-05d6a3e13646.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663224044-2c262006-1644-4ff8-9cec-bfbde0f75e94.png)



#### **综合练习**

##### **网络爬取**

获取姓氏：[https://hanyu.baidu.com/shici/detail?pid=0b2f26d4c0ddb3ee693fdb1137ee1b0d&from=kg0](https://hanyu.baidu.com/shici/detai1?pid=0b2f26d4c0ddb3ee693fdb1137ee1b0d&from=kg0)

获取男生名字：http://www.haoming8.cn/baobao/10881.html

获取女生名字：http://www.haoming8.cn/baobao/7641.html

```java
public static void main(String[] args) throws Exception {
    String familyNameNet = "https://hanyu.baidu.com/shici/detail?pid=0b2f26d4c0ddb3ee693fdb1137ee1b0d&from=kg0";
    String boyNameNet = "http://www.haoming8.cn/baobao/10881.html";
    String girlNameNet = "http://www.haoming8.cn/baobao/7641.html";
    //爬取数据，把网址上所有的数据拼接成一个字符串
    String familyStr = webCrawler(familyNameNet);
    String boyStr = webCrawler(boyNameNet);
    String girlStr = webCrawler(girlNameNet);
    //通过正则表达式，把其中符合要求的数据获取出来
    //[\u4E00-\u9FA5]是中文的范围
    ArrayList<String> familyList = getData(familyStr, "([\\u4E00-\\u9FA5]{4})(，|。)", 1);
    ArrayList<String> boyList = getData(boyStr, "([\\u4E00-\\u9FA5]{2})(、|。)", 1);
    ArrayList<String> girlList = getData(girlStr, "(.. ){4}..", 0);
    //处理数据
    //将姓氏拆开加入新集合，赵钱孙李
    ArrayList<String> familyName = new ArrayList<>();
    for (String s : familyList) {
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            familyName.add(String.valueOf(c));
        }
    }
    //男生名字有重复，要去重
    ArrayList<String> boyName = new ArrayList<>();
    for (String s : boyList) {
        if (!boyName.contains(s)) {
            boyName.add(s);
        }
    }
    //女生名字
    ArrayList<String> girlName = new ArrayList<>();
    for (String s : girlList) {
        String[] split = s.split(" ");
        for (int i = 0; i < split.length; i++) {
            girlName.add(split[i]);
        }
    }
    //获取男生和女生的信息，姓名、性别、年龄
    //第四、五个参数是男生的人数和女生的人数
    ArrayList<String> list = getInfo(familyName, boyName, girlName, 10, 10);
    System.out.println(list);
    //把数据写进文件中
    BufferedWriter bw = new BufferedWriter(new FileWriter("TEAM/src/a/b.txt"));
    for (String s : list) {
        bw.write(s);
        bw.newLine();
    }
    bw.close();
}

private static ArrayList<String> getInfo(ArrayList<String> familyName, ArrayList<String> boyName, ArrayList<String> girlName, int boyCount, int girlCount) {
    //生成不重复的名字
    HashSet<String> boySet = new HashSet<>();
    while (true) {
        if (boySet.size() == boyCount) {
            break;
        }
        Collections.shuffle(familyName);
        Collections.shuffle(boyName);
        boySet.add(familyName.get(0) + boyName.get(0));
    }
    //女生
    HashSet<String> girlSet = new HashSet<>();
    while (true) {
        if (girlSet.size() == girlCount) {
            break;
        }
        Collections.shuffle(familyName);
        Collections.shuffle(girlName);
        girlSet.add(familyName.get(0) + girlName.get(0));
    }
    //把姓名、性别、年龄合并在一起
    ArrayList<String> list = new ArrayList<>();
    for (String s : boySet) {
        //15-29
        int age = (int) (Math.random() * 15 + 15);
        list.add(s + "-男-" + age);
    }
    for (String s : girlSet) {
        //15-29
        int age = (int) (Math.random() * 15 + 15);
        list.add(s + "-女-" + age);
    }
    return list;
}


private static ArrayList<String> getData(String str, String regex, int index) {
    ArrayList<String> list = new ArrayList<>();
    Pattern compile = Pattern.compile(regex);
    Matcher matcher = compile.matcher(str);
    while (matcher.find()) {
        list.add(matcher.group(index));
    }
    return list;
}

private static String webCrawler(String net) throws Exception {
    StringBuilder sb = new StringBuilder();
    URL url = new URL(net);
    //连接上网址
    URLConnection conn = url.openConnection();
    //读取数据
    //因为conn.getInputStream()是字节流，但网址可能会有中文，所以要转化成字符流读取
    InputStreamReader isr = new InputStreamReader(conn.getInputStream());
    int len;
    while ((len = isr.read()) != -1) {
        sb.append((char) len);
    }
    isr.close();
    return sb.toString();
}
```



##### **用 HuTool 生成假数据**

```java
public static void main(String[] args) throws Exception {
    String familyNameNet = "https://hanyu.baidu.com/shici/detail?pid=0b2f26d4c0ddb3ee693fdb1137ee1b0d&from=kg0";
    String boyNameNet = "http://www.haoming8.cn/baobao/10881.html";
    String girlNameNet = "http://www.haoming8.cn/baobao/7641.html";
    //爬取数据，把网址上所有的数据拼接成一个字符串
    String familyStr = HttpUtil.get(familyNameNet);
    String boyStr = HttpUtil.get(boyNameNet);
    String girlStr = HttpUtil.get(girlNameNet);
    //通过正则表达式，把其中符合要求的数据获取出来
    List<String> familyList = ReUtil.findAll("([\\u4E00-\\u9FA5]{4})(，|。)", familyStr, 1);
    List<String> boyList = ReUtil.findAll("([\\u4E00-\\u9FA5]{2})(、|。)", boyStr, 1);
    List<String> girlList = ReUtil.findAll("(.. ){4}..", girlStr, 0);
    //处理数据自己写
}
```



##### **带权重点名器**

txt 文件中事先准备好 80 个学生姓名，每个学生的名字独占一行。

要求1：每次被点到的学生，再次被点到的概率在原先的基础上降低一半。

举例：80个学生，点名5次，每次都点到小A，概率变化情况如下：

第一次每人概率：1.25%。

第二次小A概率：0.625%。		其他学生概率：1.2579%

第三次小A概率：0.3125%。		其他学生概率：1.261867%

第四次小A概率：0.15625%。		其他学生概率：1.2638449%

第五次小A概率：0.078125%。	其他学生概率：1.26483386%

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663224568-4b166577-e545-40d8-8e83-b9bc1d108e85.png)

```java
//把文件中的数据读取到内存中
ArrayList<Person> list = new ArrayList<>();
BufferedReader br = new BufferedReader(new FileReader("TEAM/src/a/b.txt"));
String len;
while ((len = br.readLine()) != null) {
    //马宛涓-女-26-1
    String[] arr = len.split("-");
    Person person = new Person(arr[0], arr[1], Integer.parseInt(arr[2]), Double.parseDouble(arr[3]));
    list.add(person);
}
br.close();
//计算权重总和
double weight = 0.0;
for (Person person : list) {
    weight += person.getWeight();
}
//计算每个人的占比
double[] arr = new double[list.size()];
int index = 0;
for (Person person : list) {
    arr[index] = person.getWeight() / weight;
    index++;
}
//计算每个人的权重占比范围
//拿自己的数值与前面的数值相加
for (int i = 1; i < arr.length; i++) {
    arr[i] += arr[i - 1];
}
//随机抽取，获取一个0.0~1.0之间的随机数
double random = Math.random();
//判断random在arr的位置
//二分查找：返回值-插入点-1
int i = -Arrays.binarySearch(arr, random) - 1;
Person p = list.get(i);
//修改被点到的学生的权重
p.setWeight(p.getWeight() / 2.0);
//把集合写入文件
BufferedWriter bw = new BufferedWriter(new FileWriter("TEAM/src/a/b.txt"));
for (Person person : list) {
    bw.write(person.toString());
    bw.newLine();
}
bw.close();
```





### **多线程**

#### **概念**

- 进程：一个软件运行，就是一个进程

进程是资源分配的基本单位，每个进程有独立的内存空间（堆、栈、方法区等）

进程之间相互隔离，一个进程崩溃不会影响其他进程

- 线程：软件中互相独立，又能同时运行的功能

线程是CPU调度的基本单位

同一个进程的多个线程共享内存空间（堆、方法区），每个线程有独立的栈空间（存储局部变量）

多线程就是让程序 “同时” 干多个活

多线程利用程序的等待时间，让 CPU 在多个程序进行切换，提高程序运行效率



#### **创建线程**

##### **继承Thread类**

继承 Thread 类之后，重写**run()**方法（run()里写线程要干的活），然后创建子类对象，调用 **start()** 启动线程。

Thread.currentThread () 的意思：获取 “当前正在执行这段代码的线程对象”。

启动线程必须是调用 start 方法，不是调用 run 方法。因为直接调用 run 方法会当成普通方法执行，此时相当于还是单线程执行，只有调用 start 方法才是启动一个新的线程执行。

不要把主线程任务放在启动子线程之前，不然主线程会先跑完，而不是同时进行。

```java
public class Test {
    public static void main(String[] args) throws Exception {
        MyThread t1 = new MyThread();
        t1.setName("线程一");
        MyThread t2 = new MyThread();
        t2.setName("线程二");
        t1.start();
        t2.start();
        for (int i = 0; i < 100; i++) {
            //主线程任务放在启动子线程之后
            System.out.println("主线程执行");
        }
    }

}
class MyThread extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + "执行任务");
        }
    }
}
```

##### **实现Runnable接口**

1. 定义一个线程任务类 MyRunnable：实现 Runnable 接口，重写 run() 方法；
2. 创建 MyRunnable 任务对象 task；
3. 把 MyRunnable 任务对象交给 Thread 处理；
4. 调用线程对象的 start() 方法启动线程。

```java
public class Test {

    public static void main(String[] args) throws Exception {
        MyRunnable task = new MyRunnable();
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);
        t1.start();
        t2.start();
        for (int i = 0; i < 100; i++) {
            //主线程任务放在启动子线程之后
            System.out.println("主线程执行");
        }
    }

}

class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + "执行任务");
        }
    }
}
```

- 匿名内部类写法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662562215-ad4a4de4-3f56-4289-89b6-13288dc48844.png)



##### **实现Callable接口**

1. 创建任务对象，定义一个类实现 Callable 接口，重写 call 方法，封装要做的事情和要返回的数据，把 Callable 类型的对象封装成FutureTask(线程任务对象)。
2. 把线程任务对象交给 Thread 对象。
3. 调用 Thread 对象的 start 方法启动线程。
4. 线程执行完毕后、通过 FutureTask 对象的的**get方法**去**获取线程任务执行的结果**。分开 try-catch，有一个出异常不会影响其他线程

```java
public class Test {

    public static void main(String[] args) throws Exception {
        MyRunnable task = new MyRunnable();
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);
        t1.start();
        t2.start();
        for (int i = 0; i < 100; i++) {
            //主线程任务放在启动子线程之后
            System.out.println("主线程执行");
        }
    }

}

class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + "执行任务");
        }
    }
}
```

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662562478-37e8a6f9-ac85-40c8-b60d-374dc935a65b.png)

#### **常用成员方法**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662563144-acf0f32a-32a9-4a11-8032-432c4a5b550e.png)

- yield()方法

在线程的 run() 方法内部调用

```java
class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 50; i++) {
            System.out.println(Thread.currentThread().getName() + "执行任务" + i);
            Thread.yield();
        }
    }
}
```

两个线程更均匀地交替执行

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662562494-02455d93-e694-4c68-9bea-d2f9d1b8f319.png)



- setDaemon()方法

当其他的非守护线程执行完毕之后，守护线程会陆续结束，即使方法还没执行完

- join()方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662562463-aadce0dd-761f-43c4-b287-83c1fcc51f46.png)



- sleep()方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662563482-f9ff4b54-54f6-4389-bfcb-c051955e7a4c.png)



- getPriority() / setPriority(int priority)方法

注意：设置优先级方法应该在 start() 方法之前

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662563600-28717754-acea-409c-bd24-129306e931b0.png)



- getState()方法：获取线程当前状态

1. NEW：新建（未启动）；
2. RUNNABLE：就绪 / 运行中；
3. BLOCKED：阻塞（等待锁）；
4. WAITING：等待（无超时，比如wait()）；
5. TIMED_WAITING：计时等待（比如sleep(1000)）；
6. TERMINATED：死亡（执行完）。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662563812-e0a26191-0cdb-4ea6-a8c2-3571f3d3fbff.png)



- isAlive()方法：判断线程是否存活

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662563965-bf8c0e3c-5ce8-4376-8609-01a0736964a2.png)



有参构造器：

如果我们要给线程设置名字，可以用set方法进行设置，也可以构造方法设置

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662564614-62b63e35-0333-44b4-8ec9-a9f0637f8d84.png)



#### **线程安全**

多个线程在同时执行，同时访问一个共享资源，存在同时修改该共享资源的安全问题

```java
public class Test {
    public static void main(String[] args) throws Exception {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        t1.setName("窗口一");
        t2.setName("窗口二");
        t1.start();
        t2.start();
    }

}

class MyThread extends Thread {
    //如果实现Runnable接口，就不需要加static修饰
    static int ticket = 0;
    @Override
    public void run() {
        while (true) {
            if (ticket < 100) {
                ticket++;
                System.out.println(Thread.currentThread().getName() + "在卖第" + ticket + "张票");
            } else {
                break;
            }
        }
    }
}
```

会卖同一张票、或者超出100张票，会有线程安全问题

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662564413-66f59f83-abff-4758-86a7-bfa912a1bc8e.png)



#### **线程同步**

##### **认识**

让多个线程先后依次访问共享资源，这样就可以避免出现线程安全问题。

**线程同步的常见方案：**

- 加锁：锁默认打开，一个线程进去后会加锁，访问完毕后自动解锁，然后其他线程才能再进来加锁。

##### **同步代码块**

左键自动生成

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662564485-95391081-812d-428b-b3ba-a1db8d42e3c4.png)

synchronized 修饰代码块，把**访问共享资源**的核心代码给上锁，执行完**自动释放锁**

锁的对象要唯一：

1. 对于实例方法建议使用 this 作为锁对象。

注意：多个线程共用同一个任务对象，比如 Runnable 类，多个线程共用同一个任务对象

继承 Thread → 多个对象 → this 锁不住

实现 Runnable → 一个对象 → this 锁得住

如下面代码就不能用 this，此时 this 指的是 t1 或 t2 这两个对象

```java
MyThread t1 = new MyThread();
MyThread t2 = new MyThread();
```

2. 对于静态方法建议使用字节码（类名.class）对象作为锁对象。

```java
public class Test {
    public static void main(String[] args) throws Exception {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        t1.setName("窗口一");
        t2.setName("窗口二");
        t1.start();
        t2.start();
    }
}
class MyThread extends Thread {
    //如果实现Runnable接口，就不需要加static修饰
    static int ticket = 0;//0~99
    static Object obj = new Object();

    @Override
    public void run() {
        while (true) {
            synchronized (obj) {
                if (ticket < 100) {
                    ticket++;
                    System.out.println(Thread.currentThread().getName() + "在卖第" + ticket + "张票");
                } else {
                    break;
                }
            }
        }
    }
}
```

- 注意

synchronized() 不能写在循环的外面，（假设线程一先抢到CPU）不然线程一会把循环执行完才会解锁，

等线程二进来已经没代码可执行了。



##### **同步方法**

synchronized 修饰方法，给方法里的所有代码加锁，线程要执行这个方法，

必须先拿到锁，执行完方法**自动释放锁**，没拿到锁的线程排队。

锁对象不能自己指定，锁的对象：

1. 如果是非静态同步方法：锁是 this
2. 如果是静态同步方法（加 static）：锁是 类名.class（类对象，所有实例共享这把锁）

把同步代码块里被 synchronized 包围的代码写成方法，然后方法被 synchronized 修饰。

```java
public class Test {

    public static void main(String[] args) throws Exception {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        t1.setName("窗口一");
        t2.setName("窗口二");
        t1.start();
        t2.start();
    }

}
class MyThread extends Thread {
    static int ticket = 0;//0~99
    @Override
    public void run() {
        while (true) {
            //抽出来一个方法
            if (method()) break;
        }
    }

    private static synchronized boolean method() {
        if (ticket < 100) {
            ticket++;
            System.out.println(Thread.currentThread().getName() + "在卖第" + ticket + "张票");
        } else {
            return true;
        }
        return false;
    }
}
```

- StringBuffer和StringBuilder的区别

StringBuffer 是线程安全的（有锁），但要耗费性能，速度慢；

StringBuilder 是非线程安全的（无锁），速度快，一般都用这个。



##### **Lock锁**

在之前的实现方式，锁都是自动开关的，而 Lock 锁是手动开关锁。

Lock 锁是 JDK5 开始提供的一个新的锁定操作，通过它可以创建出锁对象进行加锁和解锁，更灵活、更方便、更强大。

Lock 是**接口**，不能直接实例化。可以采用它的实现类 ReentrantLock，来构建 Lock 锁对象。

```java
public class Test {

    public static void main(String[] args) throws Exception {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        t1.setName("窗口一");
        t2.setName("窗口二");
        t1.start();
        t2.start();
    }

}

class MyThread extends Thread {
    //如果实现Runnable接口，就不需要加static修饰
    static int ticket = 0;//0~99
    //建议用final修饰，防止被别人篡改
    final static Lock lk = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            lk.lock();
            try {
                if (ticket < 100) {
                    ticket++;
                    System.out.println(Thread.currentThread().getName() + "在卖第" + ticket + "张票");
                } else {
                    break;
                }
            } finally {
                //将解锁放在finally代码块中，确保一定会解锁
                lk.unlock();
            }
        }
    }
}
```



##### **死锁**

不要把两个锁嵌套，避免死锁，即程序卡死

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662565058-c1984e0f-d9d5-46dc-b31c-838b83cbf3ca.png)



##### **等待唤醒机制**

A线程不够条件执行了，就先等B线程执行，条件满足后，B线程来唤醒A，让A继续干活。

然后轮到B等着，等A执行完唤醒B，以此循环。

生产者：做东西

消费者：拿东西

仓库满了 → 生产者必须等

仓库空了 → 消费者必须等

一方做完 / 拿完 → 叫醒对方

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662565427-15abfe1b-e1fd-4b3d-8a73-661975a570f3.png)

```java
//仓库
public class Warehouse {
    /**
     * 控制生产者和消费者的执行
     */
    //仓库状态：0=空，1=有商品
    public static int productFlag = 0;
    //总任务数：需要生产和消费10次
    public static int count = 10;
    //锁对象
    public static Object lock = new Object();
}
//生产者
public class Producer extends Thread {
    @Override
    public void run() {
        while (true) {
            synchronized (Warehouse.lock) {
                if (Warehouse.count == 0) { //检查是否完成任务
                    break;
                } else {
                    if (Warehouse.productFlag == 0) {
                        //做东西
                        System.out.println("生产者做东西，执行次数：" + Warehouse.count);
                        //修改仓库状态
                        Warehouse.productFlag = 1;
                        //唤醒消费者
                        Warehouse.lock.notifyAll();
                    } else {
                        try {
                            Warehouse.lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }
    }
}
//消费者
public class Consumer extends Thread {
    @Override
    public void run() {
        while (true) {
            synchronized (Warehouse.lock) {
                if (Warehouse.count == 0) { //检查是否完成任务
                    break;
                } else {
                    //判断仓库有没有东西
                    if (Warehouse.productFlag == 0) {
                        //没有就等待
                        try {
                            //让当前线程与锁绑定
                            Warehouse.lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    } else {
                        //有就把东西拿走，即数量减一
                        Warehouse.count--;
                        System.out.println("消费者拿走东西");
                        //再修改仓库的状态，从有东西修改到无东西
                        Warehouse.productFlag = 0;
                        //再唤醒生产者（唤醒与这把锁绑定的所有线程）
                        Warehouse.lock.notifyAll();
                    }
                }
            }
        }
    }
}
//主程序
public class Test {
    public static void main(String[] args) throws Exception {
        Producer producer = new Producer();
        Consumer consumer = new Consumer();
        producer.setName("生产者");
        consumer.setName("消费者");
        producer.start();
        consumer.start();
    }
}
```





- 阻塞队列实现

自动等待，自动唤醒

```java
public class Producer extends Thread {
    ArrayBlockingQueue<String> queue;
    public Producer(ArrayBlockingQueue<String> queue) {
        this.queue = queue;
    }
    @Override
    public void run() {
        while (true) {
            try {
                //如队列有空位，立即放入元素
                //如队列已满，阻塞等待，直到消费者取走元素
                queue.put("货物");
                System.out.println("生产者做了一个货物");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
public class Consumer extends Thread {
    ArrayBlockingQueue<String> queue;
    public Consumer(ArrayBlockingQueue<String> queue) {
        this.queue = queue;
    }
    @Override
    public void run() {
        while (true) {
            try {
                //队列有元素，立即取出并返回
                //队列为空，阻塞等待，直到生产者放入元素
                String goods = queue.take();
                System.out.println("消费者拿了一个" + goods);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
public class Test {
    public static void main(String[] args) throws Exception {
        //创建容量为1的阻塞队列
        ArrayBlockingQueue<String> queue = new ArrayBlockingQueue<>(1);
        Producer p = new Producer(queue);
        Consumer c = new Consumer(queue);
        p.start();
        c.start();
    }
}
```



#### **综合练习**

##### **抢红包**

一共一百块，分成三个红包，五个人抢。（主程序代码不展示）

```java
class MyThread extends Thread {
    private static int num = 3;
    private static double money = 100.0;
    private static final double MIN = 0.01;

    @Override
    public void run() { //抢到红包的线程不能再抢红包，所以把while循环去掉
        synchronized (MyThread.class) {
            if (num == 0) {
                System.out.println(getName() + "来晚了，红包已被抢完");
            } else {
                double bag;
                if (num == 1) {
                    //还剩最后一个红包时，所剩金额就是红包数
                    bag = money;
                } else {
                    //保证每个人至少0.01，比如第一个红包最多抢到99.98
                    double max = money - (num - 1) * MIN;
                    //保证最少有0.01元
                    bag = MIN + Math.random() * (max - MIN);
                    money -= bag;
                }
                num--;
                System.out.printf("%s抢到了%.2f元", Thread.currentThread().getName(), bag);
                System.out.println();
            }
        }
    }
}
```

##### **抽奖**

1. 有一个抽奖池，该抽奖池中存放了奖励的金额，该抽奖池中的奖项为：{10,5,20,50,100,200,500,800,2,80,300,700}

创建两个抽奖箱（线程）设置线程名称分别为：“抽奖箱1”，“抽奖箱2”。

随机从抽奖池中获取奖项元素并打印在控制台上，格式如下：

抽奖箱1又产生了一个10元大奖

抽奖箱2又产生了一个100元大奖

```java
class MyThread extends Thread {
    private static final List<Integer> MONEY_POOL = List.of(10, 5, 20, 50, 100, 200, 500, 800, 2, 80, 300, 700);
    private static final List<Integer> prize = new ArrayList<>(MONEY_POOL);
    @Override
    public void run() {
        while (true){
            synchronized (MyThread.class){
                if (prize.isEmpty()) {
                    break;
                }else {
                    int index = (int) (Math.random() * prize.size());
                    System.out.println(Thread.currentThread().getName() + "又产生了一个" + prize.get(index) + "元大奖！");
                    prize.remove(index);
                }
            }
            //用sleep方法使几个线程抢到CPU的执行权机会平均一点
            //sleep要在锁的外面，不然不会解锁，外面的线程进不来
            try {
                Thread.sleep(30);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

2. 每次抽的过程中，不打印，抽完时一次性打印（随机）

在此次抽奖过程中，抽奖箱1总共产生了6个奖项。

分别为：10,20,100,500,2,300，最高奖项为300元，总计额为932元。

在此次抽奖过程中，抽奖箱2总共产生了6个奖项。

分别为：5,50,200,800,80,700，最高奖项为800元，总计额为1835元。

```java
class MyThread extends Thread {
    private static final List<Integer> MONEY_POOL = List.of(10, 5, 20, 50, 100, 200, 500, 800, 2, 80, 300, 700);
    private static final List<Integer> prize = new ArrayList<>(MONEY_POOL);

    @Override
    public void run() {
        //不用在成员位置创建两个静态集合来装两个线程抽到的奖
        //只在方法里创建一个局部变量，不然有多少个线程就要创建多少个集合，很繁琐
        //线程一创建的集合，会进入线程一的栈空间；线程二创建的集合，会进入线程二的栈空间
        List<Integer> box = new ArrayList<>();
        while (true) {
            synchronized (MyThread.class) {
                if (prize.isEmpty()) {
                    break;
                } else {
                    Collections.shuffle(prize);
                    box.add(prize.getFirst());
                    prize.removeFirst();
                }
            }
        }
        int sum = 0;
        int max = box.getFirst();
        for (Integer i : box) {
            if (i > max) max = i;
            sum += i;
        }
        System.out.println(getName() + "产生了" + box.size() + "个奖项，分别为：" + box + "，最高奖项为" + max + "，总金额为" + sum);
    }
}
```



#### **线程池**

##### **认识线程池**

如果没有线程池：快递店每次来包裹（任务），都要临时找个临时工（创建线程）帮忙送货，送完就辞退（销毁线程），

找临时工、办入职离职要花时间（创建 / 销毁线程是 “重量级操作”，浪费系统资源）。

而线程池就像 “固定员工制”：提前招 5 个全职员工（核心线程），平时有包裹就分给他们；忙不过来时，先把包裹放货架（任务队列）排队；如果货架满了，再临时招几个兼职（非核心线程）；等不忙了，兼职自动离职，核心员工留下待命。

**举个例子：**

核心线程 5 个，最大线程 8 个，任务队列容量 10，空闲存活时间 30 秒。

前 5 个任务：直接分给 5 个核心线程处理；

第 6-15 个任务：核心线程忙，放到队列排队；

第 16-18 个任务：**队列满了**，招 3 个兼职（总人数 8）处理；

第 19 个任务：队列满、总人数也满了，**拒绝接收**；

所有任务处理完后，3 个兼职等 30 秒没活干，自动离职，只剩 5 个核心线程待命。



##### **创建线程池**

如何创建线程池对象？

- 方式一：使用 ExecutorServicel 的实现类 ThreadPoolExecutor 自创建一个线程池对象。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662567022-d16779ab-7ab2-4621-b7e7-acb879d123f7.png)

其中参数一至五是必选参数，参数六至七是可选参数。

**拒绝策略：**

| AbortPolicy（默认） | 直接告诉客户 “接不了”，抛异常（RejectedExecutionException） |      |
| ------------------- | ----------------------------------------------------------- | ---- |
| CallerRunsPolicy    | 由提交任务的线程执行，即主线程                              |      |
| DiscardPolicy       | 悄悄扔掉最新的任务，不抛异常也不提示(不推荐)                |      |
| DiscardOldestPolicy | 扔掉货架上最久的未处理任务，给新任务腾位置                  |      |

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662565593-385de51a-578b-438b-bfc0-357fcab36e8c.png)

- 方式二：使用 Executors (线程池的工具类)，调用方法返回不同特点的线程池对象

特点：

1. 固定 n 个 “全职员工”（核心线程数 = 最大线程数 = n），没有兼职员工。
2. 任务队列是 “无限容量货架”（LinkedBlockingQueue，默认无界）。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662566461-642826ea-bfe1-4db2-bba4-15e61f49ec25.png)

newSingleThreadExecutor()：只有 1 个工作线程

- execute() 方法

1. 把任务交给线程池调度，不用手动创建、管理 Thread；
2. 任务异步执行，即由线程池内部工作线程后台执行，不阻塞当前调用线程；

- submit() 方法

可以拿到结果

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662566494-d0797957-5b70-4aae-8f0c-27f295641001.png)



##### **处理Runnable任务**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662566739-1c476ae5-504e-43c6-a110-b6a39069a50d.png)



##### **处理Callable任务**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662567111-51cfd03c-518c-417e-86ae-4266ade87c6c.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662567615-83a1152a-0b15-47d9-8e74-d1ba4897dc17.png)



##### **线程池合适大小**

- 最大并行数 ≈ CPU 逻辑核心数

（多了反而变慢，CPU 会来回切换）

物理核心：CPU 真实硬件核心（比如 8 核）

逻辑核心：超线程后（比如 8 核 16 线程）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662568411-e30b1e57-fb44-4703-93c2-051074a8c344.png)



#### **并发并行**

- 并发：多个任务 “交替执行”，看起来像同时进行（实际同一时间只有一个任务在跑）；

并发（CPU 单核场景）

前提：CPU 只有 1 个核心

原理：CPU 通过 “时间分片”（比如每个任务执行 10 毫秒，然后切换到下一个），让多个线程 / 任务交替执行；

因为 CPU 切换速度极快（毫秒级），人类感官上觉得 “这些任务在同时跑”，但实际同一时间 CPU 只处理一个任务。

- 并行：多个任务 “同时执行”，真正意义上的同步推进（同一时间有多个任务在跑）。

前提：CPU 有多个核心（比如 4 核、8 核，相当于 “多个人做饭”）；

原理：多个核心同时各自处理一个任务，真正的 “同步执行”；

特点：同一时间有多个任务在执行，不需要切换，效率更高（前提是任务可以拆分）

核心目的：利用多核 CPU 的资源，让多个任务真正同时推进，缩短总耗时。





### **网络编程**

#### **概念**

在网络通信协议下，不同计算机之间通过网络进行数据传输。

应用场景：即时通信、网游对战、金融证券、国际贸易、邮件、等等。

- 软件架构

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662864163-3fc161d7-3bd9-4c1c-b1f8-a5be513155e3.png)

BS优缺点：

1. 不需要开发客户端，只需要页面+服务端
2. 用户不需要下载，打开浏览器就能使用
3. 如果应用过大，用户体验受到影响，因为浏览器显示的画面，在本地是没有的，所有东西都需要通过网络给传递过来



CS优缺点：

1. 画面可以做的非常精美，用户体验好
2. 需要开发客户端，也需要开发服务端
3. 用户需要下载和更新的时候太麻烦



#### **三要素**

##### **IP**

设备在网络中的地址，是唯一的标识。

公网地址（万维网使用）和私有地址（局域网使用）。

192.168.开头的就是私有地址，范围即为192.168.0.0-192.168.255.255，专门为组织机构内部使用，以此节省IP。

特殊IP：127.0.0.1，也可以是 localhost: 是回送地址也称本地回环地址，也称本机IP，永远只会寻找当前所在本机。



ipconfig：查看本机IP地址

ping：检查网络是否连通

1. IPv4

全称：Internet Protocol version4,互联网通信协议第四版。

采用32位地址长度，分成4组。

256*256*256*256 = 42亿多的IP地址

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662863167-856b5dc4-1d8d-4d44-bc26-2d26da2ffb53.png)



2. IPv6

全称：Internet Protocol version6,互联网通信协议第六版。

由于互联网的蓬勃发展，IP地址的需求量愈来愈大，而IPV4的模式下IP的总数是有限的。

采用128位地址长度，分成8组。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662864117-36e6e1c1-4dd1-4132-8d07-dd7356f70c61.png)

```java
//获取IP对象
InetAddress address = InetAddress.getByName("busychen");
InetAddress address1 = InetAddress.getByName("192.168.43.149");
//获取电脑IP
String hostAddress = address.getHostAddress();
//获取主机名
String hostName = address.getHostName();
```



##### **端口号**

应用程序在设备中唯一的标识。

端口号：由两个字节表示的整数，取值范围：0~65535

其中0~1023之间的端口号用于一些知名的网络服务或者应用。

我们自己使用1024以上的端口号就可以了。

注意：一个端口号只能被一个应用程序使用。



##### **协议**

数据在网络中传输的规则，常见的协议有UDP、TCP、http、https、ftp。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662864089-29a4ab1b-5f8e-4aa4-a42e-05dac50d0087.png)



###### **UDP协议**

用户数据报协议(User Datagram Protocol)

UDP是面向无连接通信协议。

速度快，有大小限制一次最多发送64K，数据不安全，易丢失数据。

- 发送数据

1. 创建发送端的 DatagramSocket 对象
2. 数据打包(DatagramPacket)
3. 发送数据
4. 释放资源

```java
//参数用来绑定端口，以后我们就是通过这个端口往外发送
//空参：所有可用的端口中随机一个进行使用
//有参：指定端口号进行绑定
DatagramSocket ds = new DatagramSocket();
String s = "hello";
byte[] bytes = s.getBytes();
InetAddress address = InetAddress.getByName("busychen");
int port = 10086;
DatagramPacket dp = new DatagramPacket(bytes,bytes.length,address,port);
ds.send(dp);
ds.close();
```

- 接收数据

1. 创建接收端的 DatagramSocket 对象
2. 接收打包好的数据
3. 解析数据包
4. 释放资源

```java
//在接收的时候，一定要绑定端口
//而且绑定的端口一定要跟发送的端保持一致
DatagramSocket ds = new DatagramSocket(10086);
//接收数据包
byte[] bytes = new byte[1024];
DatagramPacket dp = new DatagramPacket(bytes, bytes.length);
ds.receive(dp);
//解析数据包
byte[] data = dp.getData();
int length = dp.getLength();
InetAddress address = dp.getAddress();
int port = dp.getPort();
System.out.println("接收到数据" + new String(data, 0, length));
System.out.println("该数据是从" + address + "这台电脑中的" + port + "这个端口发出的");
ds.close();
```

注意：要先运行接收端，再运行发送端。

- UDP的三种通信方式

单播：1 个发送者 → 1 个指定接收者

组播：1 个发送者 → 一组加入群的设备

```java
//发送端
DatagramSocket ds = new DatagramSocket();
//把上面代码换成下面代码就行
MulticastSocket ms = new MulticastSocket();
//还有指定IP要指定组播地址
InetAddress address = InetAddress.getByName(组播IP);
MulticastSocket ms = new MulticastSocket(指定端口);
InetAddress address = InetAddress.getByName(组播IP);
ms.joinGroup(address);
```

广播：1 个发送者 → 同一局域网内 所有设备

```java
InetAddress address = InetAddress.getByName("255.255.255.255");
```



###### **TCP协议**

传输控制协议TCP(Transmission Control Protocol)

TCP协议是面向连接的通信协议。

速度慢，没有大小限制，数据安全。

TCP通信协议是一种可靠的网络协议，它在通信的两端各建立一个Socket对象

通信之前要保证连接已经建立

通过 Socket 产生IO流来进行网络通信

- 客户端

1. 创建客户端的Socket对象(Socket)与指定服务端连接
2. 获取输出流，写数据
3. 释放资源

```java
//在创建对象的同时会连接服务端
//如果连接不上，代码会报错
Socket socket = new Socket("127.0.0.1",10001);
OutputStream os = socket.getOutputStream();
os.write("dasd".getBytes());
socket.close();//底层会把流也关了
```

- 服务端

1. 创建服务器端的Socket对象(ServerSocket)
2. 监听客户端连接，返回一个Socket对象
3. 获取输入流，读数据，并把数据显示在控制台
4. 释放资源

```java
ServerSocket serverSocket = new ServerSocket(10001);
Socket socket = serverSocket.accept();
InputStream is = socket.getInputStream();
//如果要读中文，就要转化成字符流
InputStreamReader isr = new InputStreamReader(is);
int len;
while ((len = isr.read()) != -1) {
    System.out.println((char) len);
}
socket.close();//底层会把流也关了
serverSocket.close();//关服务器
```

- 三次握手和四次挥手

 TCP 建立连接（三次握手）

目的：确保双方都能正常收发消息，才开始传输数据。



断开连接（四次挥手）

目的：确保双方数据都传完了，再安全断开。



#### **综合练习**

1. 客户端：发送一条数据，接收服务端反馈的消息并打印

服务器：接收数据并打印，再给客户端反馈消息

- 服务器

```java
ServerSocket serverSocket = new ServerSocket(10001);
Socket socket = serverSocket.accept();
InputStream is = socket.getInputStream();
BufferedReader br = new BufferedReader(new InputStreamReader(is));
int len;
while ((len = br.read()) != -1) {
    System.out.print((char) len);
}

//回写
OutputStream os = socket.getOutputStream();
os.write("收到".getBytes());
socket.close();
serverSocket.close();
```



- 客户端

```java
Socket socket = new Socket("127.0.0.1", 10001);
OutputStream os = socket.getOutputStream();
Scanner sc = new Scanner(System.in);
String s = sc.next();
os.write(s.getBytes());
//写一个结束标记，服务端的read()方法读到这个信号，就会返回-1
//之前客户端发完直接关闭 socket → 连接断了 → 服务端自动读到 -1
//但是现在要等待服务端回写，就用此方法只关闭发送流，不关闭整个连接
socket.shutdownOutput();

//接收回写
InputStream is = socket.getInputStream();
InputStreamReader isr = new InputStreamReader(is);
int b;
while ((b = isr.read()) != -1) {
    System.out.print((char) b);
}
socket.close();
```



2. 客户端：将本地文件上传到服务器。接收服务器的反馈。

服务器：接收客户端上传的文件，上传完毕之后给出反馈。

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662863762-cc82f86d-7965-4d17-a8d5-82d1c7a44a39.png)

- 服务端

```java
ServerSocket serverSocket = new ServerSocket(10001);
Socket socket = serverSocket.accept();
InputStream is = socket.getInputStream();
//防止文件名重复
String fileName = UUID.randomUUID().toString().replace("-", "");
FileOutputStream fos = new FileOutputStream("TEAM\\serverdir\\" + fileName + ".txt");
int len;
while ((len = is.read()) != -1) {
    fos.write((char) len);
}
//回写
OutputStream os = socket.getOutputStream();
os.write("收到".getBytes());
socket.close();
serverSocket.close();
```

- 客户端

```java
Socket socket = new Socket("127.0.0.1", 10001);
OutputStream os = socket.getOutputStream();
FileInputStream fis = new FileInputStream("TEAM\\src\\a\\b.txt");
int len;
while ((len = fis.read()) != -1) {
    os.write(len);
}
socket.shutdownOutput();

//接收回写
InputStream is = socket.getInputStream();
InputStreamReader isr = new InputStreamReader(is);
int b;
while ((b = isr.read()) != -1) {
    System.out.print((char) b);
}
socket.close();
```



3. 上传文件（多线程）

- 只需改服务端，客户端同上

```java
public class Test {

    public static void main(String[] args) throws Exception {
        ServerSocket serverSocket = new ServerSocket(10001);
        while (true) {
            Socket socket = serverSocket.accept();
            new MyThread(socket).start();
        }
    }
}

class MyThread extends Thread {
    Socket socket;

    public MyThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            InputStream is = socket.getInputStream();
            //防止文件名重复
            String fileName = UUID.randomUUID().toString().replace("-", "");
            FileOutputStream fos = new FileOutputStream("TEAM\\serverdir\\" + fileName + ".txt");
            int len;
            while ((len = is.read()) != -1) {
                fos.write((char) len);
            }
            //回写
            OutputStream os = socket.getOutputStream();
            os.write("收到".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (socket != null) {
                try {
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

4. 线程池优化

```java
//只需改main方法里的代码，其他不变
public static void main(String[] args) throws Exception {
    ThreadPoolExecutor pool = new ThreadPoolExecutor(3,5,
            30,TimeUnit.SECONDS,new ArrayBlockingQueue<>(10),Executors.defaultThreadFactory());
    ServerSocket serverSocket = new ServerSocket(10001);
    while (true) {
        Socket socket = serverSocket.accept();
        pool.submit(new MyThread(socket));
    }
}
```



### **单元测试**

就是给代码里的 “最小功能块”（比如一个函数、一个小方法）写专门的 “测试代码”，自动检查它能不能正常工作。

类名以Test结尾，测试方法以test开头（这样 IDE 能自动识别）

为需要测试的业务类，定义对应的测试类，并为每个业务方法，编写对应的测试方法（必须：**公共、无参、无返回值**）

测试方法上必须声明@Test注解，然后在测试方法中，编写代码调用被测试的业务方法进行测试。

加入JUnit后需重启Idea，才能执行

开始测试：选中测试方法，右键选择“JUnit运行”，如果测试通过则是绿色；如果测试失败，则是红色

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662864396-ae51d1d9-223e-46d6-9ac8-37ea481fd0c0.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662865312-77bcbfcd-8bd5-478d-a293-3aa502845f75.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662865851-1d8d3d09-18b7-4007-a5e9-0435003384be.png)

断言：用来帮我们确定被测试的方法是否按照预期的效果正常工作

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662866702-3242df0c-997f-4df4-9604-5ff4e424781d.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662866907-8460a35a-1d9a-43a6-99b1-1ced17cc4eb8.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662867014-bdd33432-fd9b-4de0-9b0c-a3eba42ff213.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662867012-fc416442-953f-43c0-b79a-d55a9cc84da7.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662868190-f665169f-bfd7-44b1-8620-9f43067ef909.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662868518-7b5de967-7026-4fc5-97f7-702ac2778728.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662870426-c1d16e7a-1220-4628-b6ad-76615d41fecd.png)

看覆盖率：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662870483-93909703-c08b-4a65-ae76-517483c8e3a6.png)

只想测一个类里面的方法：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662870370-5a45ef52-701d-472c-88fa-551c8941c7a3.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662870358-692b4d8b-76b0-435a-97d7-eb8bf106fda2.png)

通过AI：

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794662870799-0cb1704b-8fca-4e57-b5c3-3de329420d4b.png)



### **反射**

#### **认识反射**

反射允许对封装类的字段，方法和构造函数的信息进行编程访问。

可以把类中的成员变量、成员方法、构造方法获取出来，并进行操作。

例如：Idea会展示这个对象的变量、方法

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663058589-812d27d4-95a9-465f-99fa-f5be932c056e.png)

#### **获取类**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663058804-fc1ea522-63f9-4e1e-a557-f80b596f6563.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663058930-89070d16-2635-4f1c-8315-bc689bfd7aad.png)



#### **获取类中的成分**

##### **获取构造器**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663059704-3b6bbd5f-af7b-4961-956c-b8c2ff446ef1.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663059335-5dcede40-6075-4a88-9b37-1194993d1d8d.png)



##### **获取变量**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663060391-5439c55d-3a37-40a6-9f19-bd757e137d3c.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663060463-b36fdf0a-4ce1-4492-95e4-b85528ef9d10.png)



##### **获取方法**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663061228-5fa48609-1d4f-4351-87e0-b4757f89827d.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663060843-2b64cb1b-0e93-4d2a-ade0-768f5b465c09.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663061337-37a77952-ff60-4abb-b01e-48b9e593326d.png)



#### **作用、应用场景**

##### **获取构造器作用**

作用：动态创建对象（不用new关键字）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663061850-0db82162-683b-4b5a-b3c2-5706f3b045fb.png)



##### **获取变量作用**

作用：动态修改私有属性（不用setter方法）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663061868-a644f79b-5758-4747-a611-aa62a75cbaf7.png)



##### **获取方法作用**

作用：动态调用私有方法（不用公开接口）

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663062307-46ef4d1d-3368-4ab3-98d2-a8f4d802c56e.png)

总结：

- 基本作用：可以得到一个类的全部成分然后操作。
- 可以破坏封装性——暴力反射
- 可以绕过泛型的约束

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663062590-7626a2e9-e83d-4ac4-b56e-38b80558a1a2.png)





### **注解**

#### **概述、自定义注解**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663103076-9a13b0d2-5afd-4b70-9829-1db98f454c2d.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663102434-039fea1a-ef7e-4d6d-a2db-50ea94a66f10.png)

注解是 “给代码加的**说明 / 标记**”，让程序或工具知道该怎么处理这段代码。

例如：

- 给一个方法加 @Override → 告诉编译器 “这个方法是重写父类的，帮我检查有没有写错”
- 给一个类加 @Controller（Spring 框架） → 告诉 Spring“这是个控制器，负责接收网页请求”
- 给一个字段加 @NotNull → 告诉工具 “这个值不能为 null，帮我校验”

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663102069-e62bb879-e47e-4064-95ab-561ac1388a9a.png)

Java 设计注解时，把value定为 “默认快捷属性名”

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663101964-44cdb970-96c3-4a22-a687-65488eabc126.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663101993-b0d8174d-014c-4fa3-ab3c-a054a1e9ec90.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663102836-36428a24-b5f2-4f97-88c4-b6ef5359b970.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663102949-ad2a49e3-9648-4108-8d4f-47b1a3b4af78.png)

#### **元注解**

元注解，顾名思义就是「修饰注解的注解」—— 就像给 “便利贴（普通注解）” 加 “使用说明”：告诉编译器 / 程序，这个普通注解能贴在哪（比如只能贴在方法上）、能存活多久（比如运行时还能读取）、能不能被继承等。

- @Retention：指定注解的 “生命周期”

例如：@Retention(RetentionPolicy.RUNTIME)

| 取值                  | 生命周期阶段                                      | 通俗理解                                     | 典型场景                          |
| --------------------- | ------------------------------------------------- | -------------------------------------------- | --------------------------------- |
| SOURCE（源代码阶段）  | 只在 .java 文件中存在，编译成 .class 后消失       | 注解只给编译器看，编译后就没用了             | @Override、@SuppressWarnings      |
| CLASS（类文件阶段）   | 编译后存在于 .class 文件中，但运行时（JVM）会丢弃 | 给字节码工具看（比如类加载器），运行时读不到 | 默认值（很少手动指定）            |
| RUNTIME（运行时阶段） | 编译后在 .class 中，运行时 JVM 也能读取           | 程序运行时能通过反射获取注解信息             | Spring 的 @Controller、自定义注解 |

- @Target：指定注解能 “贴在哪”

例如：@Target({ElementType.METHOD,ElementType.TYPE})

| 取值           | 可贴的代码元素               | 典型场景                         |
| -------------- | ---------------------------- | -------------------------------- |
| TYPE           | 类、接口、枚举               | @Controller、@Service            |
| METHOD         | 方法                         | @Override、@Test                 |
| FIELD          | 成员变量（字段）             | @NotNull、@Value（Spring）       |
| PARAMETER      | 方法参数                     | @RequestParam（Spring）          |
| CONSTRUCTOR    | 构造方法                     | 自定义校验注解                   |
| LOCAL_VARIABLE | 局部变量（比如方法里的变量） | 很少用（比如 @SuppressWarnings） |

#### **注解的解析**

注解本身是 “死标签”，解析就是让程序 “活读标签、按标签做事” 的过程。

程序运行时（或编译时），读取注解的信息（比如注解的属性值），并根据这些信息做对应处理

前提：

1. 注解的生命周期是「RUNTIME」：必须用元注解 @Retention(RetentionPolicy.RUNTIME) 修饰，

之前讲过，只有运行时注解能被 JVM 读取，解析才有意义。

1. 知道注解贴在哪个 “载体” 上：注解只能贴在类、方法、字段、参数等 “代码载体” 上，解析时要先找到这个载体，比如要解析方法上的注解，先找到这个方法。

##### **解析类上的注解**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663103054-444311bc-573c-4f6f-9062-36351d38f426.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663103944-d3a5d0ad-6788-45e2-93e6-3af959ef5ef4.png)

##### **解析方法上的注解**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663103801-f340f7a8-3bdb-46d4-a055-554e17a5b1a7.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663104094-32451866-db62-482d-b99a-49b41144a945.png)

##### **解析变量上的注解**

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663104275-614604d4-a1b2-47f7-a14c-14c12c7445d3.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663104743-fcee255f-339e-4f73-888a-a376e9bb8e48.png)

### **动态代理**

- 场景

1. 当你想在不修改原有业务代码的前提下，给方法统一增加额外功能时，就用动态代理。
2. 多个类 / 方法需要相同的通用功能（日志、权限、缓存…）

核心对象会实现一个接口写一个方法。

代理对象（中间人）：程序运行时自动创建的对象，它会：

伪装成 “目标对象”（实现同一个接口，所以你用的时候感觉和原对象一样）；

拦截目标对象的方法调用（比如你调用方法，其实是代理先收到请求）；

执行额外操作（打印日志），再调用目标对象的原方法，把原方法的结果返回给你。

代理要实现 InvocationHandler 接口

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663105046-b445e33f-f1b2-4e47-a14c-f5dfd93ac7ce.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663105461-1d10b709-bee0-4ac7-b989-a307fbd091f6.png)

![0](https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1794663105730-6c35b9c4-b374-4591-8245-1dbafce26898.png)

