# 变量

- 标识符：golang中对各种变量、方法和函数等命名时使用的字符序列.
  - golang中严格区分大小写.
  - 标识符中不能含有空格.
  - `_`在golang中是一个特殊的标识符，即空标识符. 它可以代表任何其他标识符，但它对应的值会被忽略，故仅作为占位符使用.
  - 不能以系统保留关键字作为标识符.
- 声明变量：`变量名 值 数据类型`
  - 支持多变量声明.
- 常量
- 全局变量
  - 函数外部定义的变量.
  - 只定义不使用，不会报错.
  - 一次性声明.
- 局部变量

```go
package main
// 全局变量
var a1 = 1
var a2
var ( // 一次性声明
	a3 = 'frank'
)
func main() {
  b1, b2 := 'frank', 7 // 多变量声明
}
```

## 数据类型

### 基本数据类型

- 数值型

  - 整数类型 `int, int8, int16, int32, int64` `uint8 uint16 uint32 uint64` `rune` `byte`
  - 浮点类型 `float32` `float64` 
  - 复数类型 `complex64` `complex128`

- 字符型

- 布尔型 `bool`

- 字符串 `string`


```go
/* 整数类型
- golang中，int、uint的大小和系统有关
- golang的整型默认声明为int型，int默认8个字节，默认值为0
- golang程序中整型变量使用时，遵守保小不保大的原则
*/


/* 浮点数类型
- 浮点数=符号位+指数位+尾数位
- golang浮点类型有固定的范围和字段长度，不受具体操作系统的影响
- golang的浮点型默认声明为float64类型，默认值为0
*/
var a float64 = .1234
var b float64 = 3.14159e-2 // 3.14159/(10^2)


/* 字符类型
- golang中没有专门的字符类型，存储单个字符(字母)，一般使用byte保存(ASCII表)
- golang中的编码统一成utf-8. 一个ASCII字符占1个字节，一个汉字占3个字节
- 字符存储：字符 -> 对应码值 -> 二进制 -> 存储
- 字符读取：二进制 -> 码值 -> 对应字符 -> 读取
- 字符常量是用单引号'括起来的单个字符
*/
var a byte = 'a'
var b byte = '王' // overflow
var c int = '王'
fmt.Println("a=", a) // 97
fmt.Println('a=%c', a) // 'a'
fmt.Println('c=%c') // '王'


/* 字符串类型
- 计算机中，字符串是一串固定长度的字符连接起来的字符序列. golang中，字符串由单个字节拼接而来
- golang中，字符串的字符使用UTF-8编码标识Unicode文本
- 字符串一旦被赋值，字符串就不能修改，即Go中字符串是不可变的（原子性）
- 双引号形式：会识别转义字符
- 反引号形式：以字符串的原生形式输出，包括换行和特殊字符
- 默认值为""
*/
var hello string = "my name is \nfrank"
fmt.Println(hello) // 会换行
name[0] = 'a' // error
str1 := `
my name is \n frank
wong
`
fmt.Println(str1) // 原样输出
var str2 = "hello "+"world"+ // 字符串可拼接，+号必须在上一行尾部
", "
str2 += "frank"


/* 布尔型
- 仅占1个字节，只允许取值为true或flase
- golang中，bool型不可以使用0或非0替代true或false
- 默认值为false
*/
```

### 复杂数据类型

- 指针 `pointer`
- 数组 `array`
- 结构体 `struct`
- 管道 `channel`
- 函数 `func`
- 切片 `slice`
- 接口 `interface`
- 映射 `map`

```go
/* 指针
- 对于基本数据类型来说，变量存的就是值. 用&获取变量的地址
- 指针变量存的是一个地址，这个地址指向的空间存的才是值
- 值类型：变量直接存储值，内存通常在栈中分配. eg: int,float,string,数组,struct
- 引用类型：变量存储一个地址，该地址对应的空间才是真正存储值的地方，内存通常在堆中分配，没有任何变量引用的地址对应的空间会由GC回收. eg: 指针,slice,map,管道,interface
*/
var a int = 10
fmt.Println("a的地址=", &a)
fmt.Println("a的值=", a)
var b *int = &a
fmg.Println("b的值=%v", b) // a的地址
fmg.Println("b的地址=%v", &b)
fmg.Println("b指向的值=%v", *b)
*b = 6
fmt.Println("a的值=", a) // 6
```

## 数据类型转换

- golang中数据类型不能自动转换，故不同类型的变量之间赋值时需要显示转换.
- 高精度转换到低精度在编译时不会报错，转换的结果按一处处理，实际结果和期待结果不一定一样.
- https://studygolang.com/articles/13139

```go
var a int32 = 100
// int -> float
var b float32 = float32(a) // 100
// high -> low
var c int8 = int8(a) // 100
// low -> high
var d int64 = int64(a) // 100
// float -> int
var e float32 = 3.14
var f int32 = int32(e) // 3

// . -> string
var n1 int = 99
var n2 float64 = 3.14
var b bool = true
var str string
str = fmt.Sprintf("%d", n1)
fmt.Printf("str type %T str=%q\n", str, str) // string "99"
str = strconv.FormatInt(int64(n1), 10)
fmt.Printf("str type %T str=%q\n") // string "99"

//string -> .
var str1 string = "hello"
var str2 string = "123"
n1, _ = strconv.ParseInt(str1, 10, 64) // 0
n2, _ = strconv.ParseInt(str2, 10, 64) // 123
```

## 运算

- 运算符：https://studygolang.com/articles/12463
  - 一种特殊的符号，用以表示数据的运算、赋值和比较等.
    - 算术运算符
    - 关系运算符
    - 逻辑运算符
    - 位运算符、移位运算符
    - 赋值运算符
    - 其他运算符
  - 运算符优先级
  - 二元运算符的运算方向均是从左至右.
  - golang明确不支持三元运算符.
- 算术运算
- 关系运算
- 逻辑运算
- 赋值运算
- 位运算

```go
/* 算术运算
- 算术运算符是对数值类型的变量进行运算
- 自增\自减：只有i++和i--且只能独立使用，没有++i和--i
- 取余：a%b <=> a-a/b*b
*/

/* 关系运算
- 关系运算符又称为比较运算符，其运算结果都是bool型
- 关系运算组成的表达式，称为关系表达式：a>b
*/

/* 逻辑运算
- 逻辑运算用于连接多个条件，通常是关系表达式，最终结果是一个bool值
*/

/* 赋值运算
- <变量> <赋值运算符> [变量|表达式|常量]
- 运算顺序从右往左
*/
```

## 进制转换

- 二进制：golang中，不能直接使用二进制来表示一个整数，沿用了C语言的特点.
- 八进制：以0开头表示.
- 十六进制：以0x或0X开头表示，a-f不区分大小写.

# 控制流

- 顺序控制
- 分支控制
  - 单分支控制
  - 双分支控制
  - 多分支控制
  - 嵌套分支
  - switch分支控制
- 循环控制
  - for循环
  - for-range循环
  - while循环
  - do-while循环
  - 多重循环控制
- 跳转控制

```go
/* 顺序控制
- 变量先声明后使用，即前向引用
*/

/* 分支控制
- 单分支控制：if语句
- 双分支控制：if-else语句
- 多分支控制：if-else if-else语句
- 嵌套分支：if语句内含有if语句
- switch分支控制：switch语句.	
	- 1.执行switch的表达式得到值s; 2.用值s与case的表达式进行比较，看是否相等
	- switch与if的区别比较
*/
var age int = 18
if age >19 { // 哪怕代码块里只有一行代码，也必须使用{}
  fmt.Println("age>18")
}
if n:20; n>5 {
  fmt.Println("n>5")
} else if n>0 {
  fmt.Println("0<n<=5")
} else { // else不能换行
  fmt.Println("n<0")
}
// switch eg1
switch age { // 从上到下逐一测试，直至匹配为止;
  case 6, 0, 1: // case后的表达式可以有多个，表达式间是或运算
  	fmt.Println("age=6")
  case 18:
  	fmt.Println("age=18") // golang中，匹配项后面不需要加break！
  case 30:
  	fmt.Println("age=30")
  default: // default语句不是必须的
  	fmt.Println("no matching")
} // switch和case后跟的是表达式，即常量值、变量、有返回值的函数
// switch eg2
switch { // swich后不跟表达式，类似于if-else
  case age == 6:
  	fmt.Println("age=6")
  case age == 18:
  	fmt.Println("age=18")
  	fallthrough // 会继续执行下一个case，默认只能穿透一层
  case age >18 && age<30:
  	fmt.Println("age>18") // eg2中会执行，且到此为止
  default:
  	fmt.Println("no matching") // eg2中不会执行
}

/* 循环控制
- for循环控制：for 循环变量初始化;循环条件;循环变量迭代{循环操作语句}
- for-range循环：range关键字用于for中迭代数组、切片、通道、集合的元素
*/
//eg1
for i:=1; i<=10; i++ {
  fmt.Println("i=%d", i)
}
//eg2
n := 1
for n<9 { // 等价于一个while循环
  fmt.Println("n=%d", i)
  n++
}
//eg3
for { // 等价于一个无限循环
  // 通常需要配合break语句
}
//eg4
nums := []int{1,2,3}
for index,num := range nums {
  fmt.Println("index=%d, value=%d", index, num)
}

/* 跳转控制
- break: 1.默认会跳出最近的for循环; 2.break后跟指定标签，会跳出标签对应的for循环
- continue: 用于结束本次循环，然后继续执行下一次循环
- goto: goto <label>
	- lang中的goto语句可以无条件转移到程序中指定的行
- return: 在方法或函数中使用，表示跳出所在方法或函数
*/
//eg1
label1:
for i := 0; i < 4; i++ {
  //label2:
  for j := 0; j < 10; j++ {
    if j ==2 {
      //默认跳出最近的循环 这里尝试跳出外层循环
      break label1
    }
    fmt.Println("j =", j, "i =", i)
  }
} 
```

# 函数

在golang中，代码组织成包`package`，包组织成模块`module`.

`go.mod`: 该文件所在目录下的代码属于一个模块，该模块可被其他代码引入`import`.

在一个包里，只有大写字母开头的函数才能被其他包内的函数调用.

文件名与包名、模块名并无关系.

# 模块

- `$GOPATH`
- `go.mod`：golang1.11版本新引入的官方包管理工具.
  - 标记了每个依赖包的版本，在构建过程中用go命令下载go.mod中的依赖包，下载的依赖包会缓存在本地，以便下次构建.
  - Module是相关go包的集合，是源代码交换和版本控制的单元.
  - 只需要记录直接依赖的依赖包版本. 只在依赖包版本包含go.mod文件时，才会记录间接依赖包版本.
- `go.sum`：`<module> <version>[/go.mod] <hash>`
  - 考虑到下载和缓存在本地的依赖包都有被篡改的可能，只靠go.mod无法保证一致性构建，于是引入go.sum来解决Go Modules的这一安全隐患.
  - Go Module机制下，需要同时使用依赖包的名称和版本才能准确描述一个依赖.
  - 记录所有(直接+间接)依赖包版本及其go.mod的hash值.

```go
// go.mod
package pkg1
go 1.16

require (
	github.com/labstack/echo v4.0.0
)

// go.sum
github.com/labstack/echo v4.0.0 h1:b4Gk+7WdP/d3HZH8EJsZpvV7EtDOgaZLtnaNGIu1adA=
github.com/labstack/echo v4.0.0/go.mod h1:TIyPZe4MgqvfeYDBFedMoGGpEw/LqOeaOT+nhxU+yHo=
```

## 包

`package`

- golang中最基本的代码组织单元.

- 对于包内的变量名、函数名、常量名，首字母大写是公开的，首字母小写是私有的.

# 参考

- https://docs.studygolang.com/