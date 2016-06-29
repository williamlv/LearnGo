<!-- MarkdownTOC -->

- [环境变量](#环境变量)
- [安装目录清单](#安装目录清单)
- [调试器](#调试器)
- [格式化代码 gofmt](#格式化代码-gofmt)
- [生成代码文档](#生成代码文档)
- [init调用顺序](#init调用顺序)
- [Go 程序的执行（程序启动）顺序如下：](#go-程序的执行（程序启动）顺序如下：)
- [基本类型](#基本类型)
- [值类型和引用类型](#值类型和引用类型)
- [出，Public](#出，public)
- [function](#function)
    - [命名返回值](#命名返回值)
    - [变长参数](#变长参数)
    - [回调函数](#回调函数)
    - [应用闭包](#应用闭包)
    - [闭包调试](#闭包调试)
- [注释](#注释)
- [交换变量](#交换变量)
- [包引用](#包引用)
- [空白标识符_](#空白标识符_)
- [使用 := 赋值操作符](#使用--赋值操作符)
- [格式化说明符](#格式化说明符)
- [位运算符](#位运算符)
- [字符](#字符)
- [字符串](#字符串)
- [常用包](#常用包)
- [指针](#指针)
- [控制结构](#控制结构)
- [error](#error)
- [defer和追踪](#defer和追踪)
- [内置函数](#内置函数)
- [数组](#数组)
- [切片](#切片)
- [bytes包](#bytes包)
- [map](#map)
- [锁](#锁)

<!-- /MarkdownTOC -->


# 环境变量
+ $GOROOT 安装目录
+ $GOBIN $GOROOT/bin
+ $GOPATH 项目路径,必须包含三个目录
 - src
 - pkg
 - bin

# 安装目录清单
你的 Go 安装目录（`$GOROOT`）的文件夹结构应该如下所示：

README.md, AUTHORS, CONTRIBUTORS, LICENSE

- `/bin`：包含可执行文件，如：编译器，Go 工具
- `/doc`：包含示例程序，代码工具，本地文档等
- `/lib`：包含文档模版
- `/misc`：包含与支持 Go 编辑器有关的配置文件以及 cgo 的示例
- `/os_arch`：包含标准库的包的对象文件（`.a`）
- `/src`：包含源代码构建脚本和标准库的包的完整源代码（Go 是一门开源语言）
- `/src/cmd`：包含 Go 和 C 的编译器和命令行脚本

# 调试器
- 在合适的位置使用打印语句输出相关变量的值（print/println 和 fmt.Print/fmt.Println/fmt.Printf）。

-在 fmt.Printf 中使用下面的说明符来打印有关变量的相关信息：

 - %+v 打印包括字段在内的实例的完整信息
 - %#v 打印包括字段和限定类型名称在内的实例的完整信息
 - %T 打印某个类型的完整说明

# 格式化代码 gofmt
[参考](https://github.com/Unknwon/the-way-to-go_ZH_CN/blob/master/eBook/03.5.md)

# 生成代码文档
[参考](https://github.com/Unknwon/the-way-to-go_ZH_CN/blob/master/eBook/03.6.md)
```
go doc package
```

# init调用顺序 
+ 每个package中应该是每个init都会被调用，且顺序固定
+ 对同一个go文件的init()调用顺序是从上到下的
+ 对同一个package中不同文件是按文件名字符串比较“从小到大”顺序调用各文件中的init()函数
+ 对于不同的package，如果不相互依赖的话，按照main包中"先import的后调用"的顺序调用其包中的init()
+ 如果package存在依赖，则先调用最早被依赖的package中的init()

# Go 程序的执行（程序启动）顺序如下：
1. 按顺序导入所有被 main 包引用的其它包，然后在每个包中执行如下流程：
2. 如果该包又导入了其它的包，则从第一步开始递归执行，但是每个包只会被导入一次。
3. 然后以相反的顺序在每个包中初始化常量和变量，如果该包含有 init 函数的话，则调用该函数。
4. 在完成这一切之后，main 也执行同样的过程，最后调用 main 函数开始执行程序。

# 基本类型 
```
int uint unitptr
int8 int16 int32 int64 uint8 uin16 uint32 uint64
float32 //小数点后7位
float64 、、15
m = int32(n)
complex64 //32位实数和虚数
complex128 //64位实数和虚数
```
类型之前需要显式转换。

# 值类型和引用类型
+ 所有像 int、float、bool 和 string 这些基本类型都属于值类型,
+ 像数组（第 7 章）和结构（第 10 章）这些复合类型也是值类型
+ 在 Go 语言中，指针（第 4.9 节）属于引用类型，
+ 其它的引用类型还包括 slices（第 7 章），maps（第 8 章）和 channel（第 13 章）, 接口(interface)。
+ 被引用的变量会存储在堆中，以便进行垃圾回收，且比栈拥有更大的内存空间。

#导出，Public
+ 当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；
+ 标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 private ）。


# function
```
func functionName(parameter_list) (return_value_list) {
  // parameter_list 的形式为 (param1 type1, param2 type2, …)
  //return_value_list 的形式为 (ret1 type1, ret2 type2, …)
}
```
只有当某个函数需要被外部包调用的时候才使用大写字母开头，并遵循 Pascal 命名法；否则就遵循骆命名法，即第一个单词的首字母小写，其余单词的首字母大写。
+ 函数也可以是一个确定的类型，就是以函数作为返回类型。
+ 这种类型的声明要写在函数名和可选的参数列表之后。"{"在第一行
+ 一个函数可以拥有多返回值，返回类型之间需要使用逗号分割，并使用小括号 () 将它们括起来
+ 使用 type 关键字可以定义你自己的类型，type IZ int。type Employee struct{company string}
+ 返回某个对象的函数或方法的名称一般都是使用名词，没有Get... 之类的字符，
+ 如果是用于修改某个对象，则使用 SetName。有必须要的话可以使用大小写混合的方式，如 MixedCaps 或 mixedCaps，而不是使用下划线来分割多个名称。
+ 不支持函数重载，函数重载需要进行多余的类型匹配影响性能
+ 匿名函数或者lambda函数
+ 按值传递，按引用传递(指针实现)
## 命名返回值
```
func getX2(input int)(x2 int, x3 int){ //x2,x3默认初始化为0 命名返回值即使只有一个也需要使用()括起来
    x2 = 2 * input
    x3 = 3 * input
    return         //return x2, x3
}
```

## 变长参数
如果函数的最后一个参数是采用 ...type 的形式，那么这个函数就可以处理一个变长的参数，这个长度可以为 0
```
func Greeting(prefix string, who ...string)
Greeting("hello:", "Joe", "Anna", "Eileen")
```
在 Greeting 函数中，变量 who 的值为 []string{"Joe", "Anna", "Eileen"}。
如果参数被存储在一个数组 arr 中，则可以通过 arr... 的形式来传递参数调用变参函数。

## 回调函数
```
callback(1, Add)
func Add(a, b int){
    fmt.Printf(a+b)
}
func callback(y int, f func(int, int)){
    f(y, 2)
}
```

## 应用闭包
```
func main() {
    var f = Adder()
    fmt.Print(f(1), " - ")
    fmt.Print(f(20), " - ")
    fmt.Print(f(300))
} 
func Adder() func(int) int {
    var x int
    return func(delta int) int {
        x += delta
        return x
    }
}
```
1 - 21 -321    
在多次调用中，变量 x 的值是被保留的

## 闭包调试
```
where := func() {
    _, file, line, _ := runtime.Caller(1)
    log.Printf("%s:%d", file, line)
}
//log包的flag
log.SetFlags(log.Llongfile)
log.Print("")
//或
var where1 = log.Print
where1()

```

# 注释
+ 在多段注释之间应以空行分隔加以区分。
+ 几乎所有全局作用域的类型、常量、变量、函数和被导出的对象都应该有一个合理的注释。
+ 如果这种注释（称为文档注释）出现在函数前面，例如函数 Abcd，则要以 "Abcd..." 作为开头。

# 交换变量
+  如果你想要交换两个变量的值，则可以简单地使用 a, b = b, a。

# 包引用
import fm "fmt"// alias3
import . "./pack1" //可以不通过包名来使用其中的项目
import _ "./pack1/pack1" //导入副作用，只执行init和初始化全局变量
go install codesite.ext/author/goExample/goex //导入外部安装包，使用 import goex即可引用
import goex "codesite.ext/author/goExample/goex" //导入代码中

# 空白标识符_
被用于抛弃值，如值 5 在：_, b = 5, 7 中被抛弃。

# 使用 := 赋值操作符
变量定义采用后置类型
```
var identifier type;
var aaa, bbb int32;
a := 1;
```
变量的类型可由系统自动推断
只能被用在函数体内，而不可以用于全局变量的声明与赋值
不可以再次对于相同名称的变量使用初始化声明
使用 a := uint64(0) 来同时完成类型转换和赋值操作

# 格式化说明符
在格式化字符串里，%d 用于格式化整数（%x 和 %X 用于格式化 16 进制表示的数字<br/>
%g 用于格式化浮点型（%f 输出浮点数，%e 输出科学计数表示法）<br/>
%0d 用于规定输出定长的整数，其中开头的数字 0 是必须的。<br/>
%n.mg 用于表示数字 n 并精确到小数点后 m 位，<br/>
除了使用 g 之外，还可以使用 e 或者 f，<br/>
例如：使用格式化字符串 %5.2e 来输出 3.4 的结果为 3.40e+00。<br/>
%b 是用于表示位的格式化标识符

# 位运算符
+ 位清除 &^：将指定位置上的值设置为 0<br/>
+ 按位异或 ^
+ 一元运算符 按位补足 ^
该运算符与异或运算符一同使用，即 m^x，对于无符号 x 使用“全部位设置为 1”，对于有符号 x 时使用 m=-1。例如：^2 = ^10 = -01 ^ 10 = -11
+ 位左移常见实现存储单位的用例    <br/>
使用位左移与 iota 计数配合可优雅地实现存储单位的常量枚举：
```
type ByteSize float64
const (
    _ = iota // 通过赋值给空白标识符来忽略值
    KB ByteSize = 1<<(10*iota)
    MB
)
```
<br/>
在通讯中使用位左移表示标识的用例<br/>
```
type BitFlag int //类型别名
const (
    Active BitFlag = 1 << iota // 1 << 0 == 1
    Send // 1 << 1 == 2
    Receive // 1 << 2 == 4
)
flag := Active | Send // == 3
```

# 字符
字符只是整数的特殊用例。byte 类型是 uint8 的别名<br/>
字符同样称为 Unicode 代码点或者 runes，并在内存中使用 int 来表示
一般使用格式 U+hhhh 来表示，其中 h 表示一个 16 进制数。其实 rune 也是 Go 当中的一个类型，并且是 int32 的别名。
在书写 Unicode 字符时，需要在 16 进制数之前加上前缀 \u 或者 \U。

# 字符串
Go 支持以下 2 种形式的字面值
+ 解释字符串 使用双引号括起来，其中的相关的转义字符将被替换，这些转义字符包括：
 + \u 或 \U：Unicode 字符
 + \\：反斜杠自身
+ 非解释字符串：
 + 该类字符串使用反引号括起来，支持换行，`raw string \n` 会被原样输出。<br/>
和 C/C++不一样，Go 中的字符串是根据长度限定，而非特殊字符\0。<br/>
注意事项 获取字符串中某个字节的地址的行为是非法的，例如：&str[i]。<br/>
在循环中使用加号 + 拼接字符串并不是最高效的做法，更好的办法是使用函数 strings.Join()（第 4.7.10 节），有没有更好地办法了？有！使用字节缓冲（bytes.Buffer）拼接更加给力（第 7.2.6 节）！
- HasPrefix 判断字符串 s 是否以 prefix 开头
- HasSuffix 判断字符串 s 是否以 suffix 结尾
- Contains 判断字符串 s 是否包含 substr
- Index 返回字符串 str 在字符串 s 中的索引
- LastIndex 返回字符串 str 在字符串 s 中最后出现位置的索引
- 如果 ch 是非 ASCII 编码的字符 strings.IndexRune(s string, r rune) int定位
- Replace 用于将字符串 str 中的前 n 个字符串 old 替换为字符串 new
- Count 用于计算字符串 str 在字符串 s 中出现的非重叠次数
- Repeat 用于重复 count 次字符串 s 并返回一个新的字符串
- ToLower 将字符串中的 Unicode 字符全部转换为相应的小写字符
- ToUpper 将字符串中的 Unicode 字符全部转换为相应的大写字符
- strings.TrimSpace(s) 来剔除字符串开头和结尾的空白符号
- strings.Trim(s, "cut") 来将开头和结尾的 cut 去除掉,TrimLeft 或者 TrimRight
- strings.Fields(s) 将会利用 1 个或多个空白符号来作为动态长度的分隔符将字符串分割成若干小块，并返回一个 slice，如果字符串只包含空白符号，则返回一个长度为 0 的 slice。
- strings.Split(s, sep) 用于自定义分割符号来对指定字符串进行分割，同样返回 slice
- Join 用于将元素类型为 string 的 slice 使用分割符号来拼接组成一个字符
- Strings.NewReader(str) 用于生成一个 Reader 并读取字符串中的内容,Read() 从 []byte 中读取内容,ReadByte() 和 ReadRune() 从字符串中读取下一个 byte 或者 rune
- 与字符串相关的类型转换都是通过 strconv 包实现的
- strconv.IntSize 获取程序运行的操作系统平台下 int 类型所占的位数
```
strconv.Atoi(s string) (i int, err error)
strconv.ParseFloat(s string, bitSize int) (f float64, err error)
strconv.Itoa(i int) string
strconv.FormatFloat(f float64, fmt byte, prec int, bitSize int) string
```

# 常用包
```
import "math/rand" 
import "time"
time.Now(), time.Now().UTC()
math.rand   
rand.Int() 
rand.Intn(8)
math.MaxUnit8 math.MinInt32 math.MaxInt32
unicode IsLetter(ch) IsDigit(ch) IsSpace(ch)
archive/tar 和 /zip-compress：压缩(解压缩)文件功能。
fmt-io-bufio-path/filepath-flag:
fmt: 提供了格式化输入输出功能。
io: 提供了基本输入输出功能，大多数是围绕系统功能的封装。
bufio: 缓冲输入输出功能的封装。
path/filepath: 用来操作在当前系统中的目标文件名路径。
flag: 对命令行参数的操作。　　
strings-strconv-unicode-regexp-bytes:
strings: 提供对字符串的操作。
strconv: 提供将字符串转换为基础类型的功能。
unicode: 为 unicode 型的字符串提供特殊的功能。
regexp: 正则表达式功能。
bytes: 提供对字符型分片的操作。
index/suffixarray: 子字符串快速查询。
math-math/cmath-math/big-math/rand-sort:
math: 基本的数学函数。
math/cmath: 对复数的操作。
math/rand: 伪随机数生成。
sort: 为数组排序和自定义集合。
math/big: 大数的实现和计算。 　　
container-/list-ring-heap: 实现对集合的操作。
list: 双链表。
unsafe: 包含了一些打破 Go 语言“类型安全”的命令，一般的程序中不会被使用，可用在 C/C++ 程序的调用中。
syscall-os-os/exec:
os: 提供给我们一个平台无关性的操作系统功能接口，采用类Unix设计，隐藏了不同操作系统间差异，让不同的文件系统和操作系统对象表现一致。
os/exec: 提供我们运行外部操作系统命令和程序的方式。
syscall: 底层的外部包，提供了操作系统底层调用的基本接口
time-log:
time: 日期和时间的基本操作。
log: 记录程序运行时产生的日志,我们将在后面的章节使用它。
encoding/json-encoding/xml-text/template:
encoding/json: 读取并解码和写入并编码 JSON 数据。
encoding/xml:简单的 XML1.0 解析器,有关 JSON 和 XML 的实例请查阅第 12.9/10 章节。
text/template:生成像 HTML 一样的数据与文本混合的数据驱动模板（参见第 15.7 节）。
net-net/http-html:（参见第 15 章）
net: 网络数据的基本操作。
http: 提供了一个可扩展的 HTTP 服务器和基础客户端，解析 HTTP 请求和回复。
html: HTML5 解析器。
runtime: Go 程序运行时的交互操作，例如垃圾回收和协程创建。
reflect: 实现通过程序运行时反射，让程序操作任意类型的变量。
regexp: 正则表达式，需要Compile
pat := "[0-9]+.[0-9]+" 
re, _ := regexp.Compile(pat)
```

# 指针
取地址符&,取内容*
format使用%p
var ptrInt *int  //nil
不能获取文字或常量的地址
不能进行指针运算，如*p++
常用于传递一个变量的引用，如函数参数

# 控制结构
条件语句两侧的括号可以省略
if-else
switch //case val1, val2, val3。每一个 case 分支都是唯一的，从上至下逐一测试，直到匹配为止。
一旦成功地匹配到每个分支，在执行完相应代码后就会退出整个 switch 代码块，也就是说您不需要特别使用 break 语句来表示结束。
程序也不会自动地去执行下一个分支的代码。如果在执行完每个分支的代码后，还希望继续执行后续分支的代码，可以使用 fallthrough 关键字来达到目的。
switch 语句还可以被用于 type-switch（详见第 11.4 节）来判断某个 interface 变量中实际存储的变量类型。

select,用于channel的选择
for 初始化语句; 条件语句; 修饰语句 {}
for 条件语句 {}
for true {}
for range{} //for ix, val := range coll { }。
要注意的是，val 始终为集合中对应索引的值拷贝，因此它一般只具有只读性质，对它所做的任何修改都不会影响到集合中原有的值（译者注：如果 val 为指针，则会产生指针的拷贝，依旧可以修改集合中的原值）。一个字符串是 Unicode 编码的字符（或称之为 rune）集合，因此您也可以用它迭代字符串
break, continue, return, goto

# error
```
//var err error
an, err := strconv.Atoi(orig)
if err != nil {
    fmt.Printf("err")
    os.Exit(1) //return err
}
if value, ok := readData(); ok {
    //习惯用法
}
n, _ strconv.Atoi(s) //忽悠第二的返回值
```

# defer和追踪
关键字 defer 允许我们推迟到函数返回之前（或任意位置执行 return 语句之后）一刻才执行某个语句或函数
关键字 defer 的用法类似于面向对象编程语言 Java 和 C# 的 finally 语句块，它一般用于释放某些已分配的资源。
当有多个 defer 行为被注册时，它们会以逆序执行（类似栈，即后进先出）
+ 关闭文件流
    defer file.Close()
+ 解锁一个资源
    defer mu.Unlock()
+ 打印最终报告
    defer printFooter()
+ 关闭数据库连接
    defer disconnectFromDB()
+ 追踪进入和离开函数
```
func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}
func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}
///记录函数参数与返回值
func func1(s string) (n int, err error) {
    defer func() {
        log.Printf("func1(%q) = %d, %v", s, n, err)
    }()
    return 7, io.EOF
}
```

# 内置函数

|名称 | 说明 |
|-----|------|
|close |  用于管道通信|
|len、cap len| 用于返回某个类型的长度或数量（字符串、数组、切片、map 和管道）；cap 是容量的意思，用于返回某个类型的最大容量（只能用于切片和 map）|
|new、make  |  new 和 make 均是用于分配内存：new 用于值类型和用户定义的类型，如自定义结构，make 用户内置引用类型（切片、map 和管道）。它们的用法就像是函数，但是将类型作为参数：new(type)、make(type)。new(T) 分配类型 T 的零值并返回其地址，也就是指向类型 T 的指针（详见第 10.1 节）。它也可以被用于基本类型：v := new(int)。make(T) 返回类型 T 的初始化之后的值，因此它比 new 进行更多的工作（详见第 7.2.3/4 节、第 8.1.1 节和第 14.2.1 节）new() 是一个函数，不要忘记它的括号|
|copy、append |用于复制和连接切片|
|panic、recover |  两者均用于错误处理机制|
|print、println  | 底层打印函数（详见第 4.2 节），在部署环境中建议使用 fmt 包|
|complex、real imag  | 用于创建和操作复数（详见第 4.5.2.2 节）|

# 数组
```
var identifier [len]type    
var arrKeyValue = [5]string{3: "Chris", 4: "Ron"}
func Sum(a *[3]float64) (sum float64) {
    for _, v := range a { // derefencing *a to get back to the array is not necessary!
        sum += v
    }
    return
}
```

# 切片
- 声明切片的格式是： var identifier []type（不需要说明长度）。   
- 一个切片在未初始化之前默认为 nil，长度为 0。   
- 切片的初始化格式是：var slice1 []type = arr1[start:end]。   
- 终止索引标识的项不包括在切片内, start到end-1f索引构成的子集       
- var slice1 []type = arr1[:] 那么 slice1 就等于完整的 arr1 数组（所以这种表示方式是 arr1[0:len(arr1)] 的一种缩写）。另外一种表述方式是：slice1 = &arr1。   
- var slice1 []type = make([]type, len)。   //slice1 := make([]type, len),slice1 := make([]type, len, cap)。
- len() cap() copy() append() sort.Ints(arri) IntsAreSorted(a []int) bool SearchInts()
x = append(x, y...) //切片y扩展成列表追加到x
- 切片重组
sl = sl[0:len(sl)+1] //扩展1位
- 字符串
```
c := []bytes(s)
copy(dst []byte, src string)
```

# bytes包
- 类型 Buffer, 提供Read 和 Write 方法
```
var buffer bytes.Buffer
var r *bytes.Buffer = new(bytes.Buffer)
func NewBuffer(buf []byte) *Buffer
```

# map
```
var map1 map[keytype]valuetype
var map1 map[string]int
val1, isPresent = map1[key1] //key1存在，isPresent为true
```
- 未初始化的 map 的值是 nil。
- 数组、切片和结构体不能作为 key，但是指针和接口类型可以
- 如果要用结构体作为 key 可以提供 Key() 和 Hash() 方法
- value 可以是任意类型的；通过使用空接口类型（详见第 11.9 节），我们可以存储任意值，但是使用这种类型作为值时需要先做一次类型断言（详见第 11.3 节）。
- 不要使用 new，永远用 make 来构造 map
- 可以选择标明 map 的初始容量 capacity，就像这样：make(map[keytype]valuetype, cap)
- delete(map1, key1) //key1不存在不会产生错误
- 获取一个 map 类型的切片，我们必须使用两次 make() 函数，第一次分配切片，第二次分配 切片中每个 map 元素
```
items := make([]map[int]int, 5)
    for i:= range items {
        items[i] = make(map[int]int, 1)
        items[i][1] = 2
    }
//value是切片
mp1 := make(map[int][]int)
mp2 := make(map[int]*[]int)
//map拷贝到切片
keys := make([]string, len(barVal))
i := 0
for k, _ := range barVal {
    keys[i] = k
    i++
}
```

# 锁
sync 包中 Mutex 来实现的
```
import "sync"

type Info struct {
    mu sync.Mutex    
}

info.mu.Lock()
info.mu.Unlock()
```
