
# 目录
+ [环境变量](#环境变量)
+ [安装目录清单](#安装目录清单)
+ [init调用顺序](#initOrder)
+ [执行顺序](#startOrder)
+ [基本类型](#baseType)
+ [值类型和引用类型](#valueType)
+ [导出，Public](#public)
+ [函数](#func)
+ [注释](#comment)
+ [交换变量](#swap)
+ [其它](#other)
+ [格式化说明符](#format)
+ [位运算符](#byte)
+ [字符串](#string)
+ [常用包](#package)


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

### <h3 id="initOrder">init调用顺序</h3>
--------------------------
+ 每个package中应该是每个init都会被调用，且顺序固定
+ 对同一个go文件的init()调用顺序是从上到下的
+ 对同一个package中不同文件是按文件名字符串比较“从小到大”顺序调用各文件中的init()函数
+ 对于不同的package，如果不相互依赖的话，按照main包中"先import的后调用"的顺序调用其包中的init()
+ 如果package存在依赖，则先调用最早被依赖的package中的init()

### <h3 id="startOrder">Go 程序的执行（程序启动）顺序如下：</h3>
---------------------------------
1. 按顺序导入所有被 main 包引用的其它包，然后在每个包中执行如下流程：
2. 如果该包又导入了其它的包，则从第一步开始递归执行，但是每个包只会被导入一次。
3. 然后以相反的顺序在每个包中初始化常量和变量，如果该包含有 init 函数的话，则调用该函数。
4. 在完成这一切之后，main 也执行同样的过程，最后调用 main 函数开始执行程序。

### <h3 id="baseType">基本类型</h3>
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

### <h3 id="valueType">值类型和引用类型</h3>
-----------------------
+ 所有像 int、float、bool 和 string 这些基本类型都属于值类型,
+ 像数组（第 7 章）和结构（第 10 章）这些复合类型也是值类型
+ 在 Go 语言中，指针（第 4.9 节）属于引用类型，
+ 其它的引用类型还包括 slices（第 7 章），maps（第 8 章）和 channel（第 13 章）。
+ 被引用的变量会存储在堆中，以便进行垃圾回收，且比栈拥有更大的内存空间。

### <h3 id="public">导出，Public</h3>
----------------------------
+ 当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；
+ 标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 private ）。


### <h3 id="func">function</h3>
----------------------------------------
```
func functionName(parameter_list) (return_value_list) {
  // parameter_list 的形式为 (param1 type1, param2 type2, …)
  //return_value_list 的形式为 (ret1 type1, ret2 type2, …)
}
```
只有当某个函数需要被外部包调用的时候才使用大写字母开头，并遵循 Pascal 命名法；否则就遵循骆命名法，即第一个单词的首字母小写，其余单词的首字母大写。
+ 函数也可以是一个确定的类型，就是以函数作为返回类型。
+ 这种类型的声明要写在函数名和可选的参数列表之后。
+ 一个函数可以拥有多返回值，返回类型之间需要使用逗号分割，并使用小括号 () 将它们括起来
+ 使用 type 关键字可以定义你自己的类型，type IZ int。type Employee struct{company string}
+ 返回某个对象的函数或方法的名称一般都是使用名词，没有Get... 之类的字符，
+ 如果是用于修改某个对象，则使用 SetName。有必须要的话可以使用大小写混合的方式，如 MixedCaps 或 mixedCaps，而不是使用下划线来分割多个名称。

### <h3 id="comment">注释</h3>
--------------------------
+ 在多段注释之间应以空行分隔加以区分。
+ 几乎所有全局作用域的类型、常量、变量、函数和被导出的对象都应该有一个合理的注释。
+ 如果这种注释（称为文档注释）出现在函数前面，例如函数 Abcd，则要以 "Abcd..." 作为开头。

### <h3 id="swap">交换变量</h3>
-------------------------------------
+  如果你想要交换两个变量的值，则可以简单地使用 a, b = b, a。

### <h3 id="other">其它</h3>
------------------------
#### import别名
import fm "fmt"// alias3
#### 空白标识符_
被用于抛弃值，如值 5 在：_, b = 5, 7 中被抛弃。
#### 使用 := 赋值操作符
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

### <h3 id="format">格式化说明符</h3>
在格式化字符串里，%d 用于格式化整数（%x 和 %X 用于格式化 16 进制表示的数字<br/>
%g 用于格式化浮点型（%f 输出浮点数，%e 输出科学计数表示法）<br/>
%0d 用于规定输出定长的整数，其中开头的数字 0 是必须的。<br/>
%n.mg 用于表示数字 n 并精确到小数点后 m 位，<br/>
除了使用 g 之外，还可以使用 e 或者 f，<br/>
例如：使用格式化字符串 %5.2e 来输出 3.4 的结果为 3.40e+00。<br/>
%b 是用于表示位的格式化标识符

### <h3 id="byte">位运算符</h3>
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
    TB
    PB
    EB
    ZB
    YB
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

### <h3 id="string">字符串</h3>
Go 支持以下 2 种形式的字面值
+ 解释字符串 使用双引号括起来，其中的相关的转义字符将被替换，这些转义字符包括：
 + \u 或 \U：Unicode 字符
 + \\：反斜杠自身
+ 非解释字符串：
 + 该类字符串使用反引号括起来，支持换行，\n\` 会被原样输出。<br/>

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
- trings.NewReader(str) 用于生成一个 Reader 并读取字符串中的内容,Read() 从 []byte 中读取内容,ReadByte() 和 ReadRune() 从字符串中读取下一个 byte 或者 rune
- 与字符串相关的类型转换都是通过 strconv 包实现的
- strconv.IntSize 获取程序运行的操作系统平台下 int 类型所占的位数
```
strconv.Atoi(s string) (i int, err error)
strconv.ParseFloat(s string, bitSize int) (f float64, err error)
strconv.Itoa(i int) string
strconv.FormatFloat(f float64, fmt byte, prec int, bitSize int) string
```



























###  <h3 id="package">常用包</br>
```
import "math/rand" 
math.rand   
rand.Int() 
rand.Intn(8)
math.MaxUnit8 math.MinInt32 math.MaxInt32
unicode IsLetter(ch) IsDigit(ch) IsSpace(ch)
