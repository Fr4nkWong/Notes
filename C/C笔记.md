# 前言

《编译原理》

词法、语法。

# 基本概念

## 变量

## 作用域

- 函数作用域
- 代码块作用域
- 文件作用域
- 原型作用域

## 运算符

- 参考 http://c.biancheng.net/view/161.html

## 标识符

# 数据类型

## 基本类型
- 定点数
  - `char`
  - `short`
  - `int`
  - `unsigned`
  - `long`
- 浮点数
  - `float`
  - `double`
- 枚举型`enum`

```C++
/*
数据运算：
- 取余操作 %
	- 取余只能针对整数.
	- 余数可正可负，由%左边决定.
*/


/*
字符char
字符串与字符数组，参考数组部分.
*/

char str[] = "10";	// 编译器会在末尾自动添加'\0'
int str_len = sizeof(str)/sizeof(char); // 3 
```

## 复合类型

### 数组

- 定义 `[]`:
  - 定点数、浮点数下的数据类型是基础类型，而数组、枚举、共用体这样的数据类型属于内置的构造类型，由基础类型之上进一步封装而来。
  - `sizeof`只能通过数组名求得数组长度，不能通过数组指针求得数组长度。
  - <u>数组和指针不等价，数组是另外一种数据类型</u>。
- **字符数组与字符串**
  - 字符串需要有结束标志`'\0'`，即ASII码表中的第0个字符，引文称为`NUL`。
  - `""`包围的字符串会自动在末尾添加`'\0'`。
  - 字符数组不会自动在末尾添加`'\0'`，需要自行预留位置。
- 静态数组
- 动态数组

```c++
// 数组的声明与定义
int arr[5] = {1,2,3,4,5};
int arr1[] = {1,2,3,4,5};

/*
字符串
字符数组，可读写，以'\0'为结束标志
sizeof不仅计算字符和空格还要计算'\0'，strlen只计算字符和空格
*/
#include <string.h>

char str[] = {'h','e','l','l','o'}; // 长度为5，不会自动添加'\0'，很容易出现数组越界
char str1[] = "hello"; // 预留一位，结尾自动添加'\0'，sizeof为6
char str2[20]; // sizeof为20，strlen为0
char str3[20] = {'w'}; // sizeof为20，strlen为1
sizeof(str); // 5
int len = strlen(str1); // 5
char *p = str;
sizeof(p); // p存的是地址，地址位数与电脑机器位有关，32位=>4*8位=>4
char *str4 = "hello"; // strlen为10，sizeof(str4)为地址位数，sizeof(*str4)为1
char str5[] = ""; // sizeof为1，strlen为0	sizeof也仅能取到str5[0]
```

### 指针

- 常指针 `const *p`

```C++
int *p1 = (int *)malloc(sizeof(int)*100);
int *p2 = new int[100];
long A::*func(int) {} // 返回值为长整型指针

// 字符串常量，只读，以'\0'为结束标志
char *str = "hello";
int a = 1;
int *p = &a;
int b = *p;
print("%d", *p); // 1

/*
C++引用与常引用
*/
// eg1
int a = 99;
int &r = a;
r = 18;
cout<<&r<<endl;
// eg2
int a = 99;
int *r = &a;
*r = 18;
cout<<r<<endl;

// eg3 引用作为函数形参
bool isOdd1(int &n){  //实参只能是变量，不可绑定临时数据
    if(n/2 == 0){
        return false;
    }else{
        return true;
    }
}
bool isOdd2(const int &n){  //常引用，可以绑定临时数据
    if(n/2 == 0){
        return false;
    }else{
        return true;
    }
}

int a = 100;

isOdd1(a);  //正确
isOdd1(a + 9);  //错误
isOdd1(27);  //错误
isOdd1(23 + 55);  //错误

isOdd2(a);  //正确
isOdd2(a + 9);  //正确
isOdd2(27);  //正确
isOdd2(23 + 55);  //正确

// eg4 函数返回引用
int & func1() {
  int a = 1;
  return a; // int &t = a; return t;
}

int *func2() {
  int a = 1;
  return &a; // int *t = &a; return t;
}

int main() {
  int p1 = func1();
  int *p2 = func2();
  return 0;
}

// eg5
char c = '@';
char *p3 = &c;  //正确
int *p4 = &c;  //错误
char &r3 = c;  //正确
int &r4 = c;  //错误 

int n = 100;
int &r1 = n;  //正确
const float &r2 = n;  //正确

char *str = "http://c.biancheng.net";
const int &r = str;	//报错，char和int*没有关系，不能自动转换

// eg6 const与指针
const int *p1; // 指针指向的值不可修改
int const *p2; // 指针指向的值不可修改
int * const p3; // 指针本身存的地址不可修改
```

### 结构体

`struct`

```c++
struct student {
	char *name;
	int age;
  unsigned sex:1;
} stu;

int main() {
  struct student stu1;
  printf("%d", sizeof(stu1)); // 16
  return 0;
}
```

### 共用体

`union`

- 结构体和共用体的区别：
  - 结构体的各个成员会占用不同的内存，互相之间没有影响；
  - 而共用体的所有成员占用同一段内存，修改一个成员会影响其余所有成员，共用体占用的内存等于最长的成员占用的内存。
- 共用体使用了**内存覆盖**技术，同一时刻只能保存一个成员的值，如果对新的成员赋值，就会把原来成员的值覆盖掉。

## 静态变量

`static`

- 隐藏：程序有多个模块时，将全局变量或函数的作用范围限制在当前模块，对其他模块隐藏。
- 保持变量内容的持久化：将局部变量存储到全局数据区，使它不会随着函数调用结束而被销毁。

## 常量

`const`

- C中的 const 变量在多文件编程时的表现和普通变量一样，除了不能修改，没有其他区别。

- C++中全局 const 变量的作用域仍然是当前文件，但是它在其他文件中是不可见的，这和添加了`static`关键字的效果类似。

## 类型转换

- 类型转换都只是为了本次运算而进行的**临时性转换**，转换结果会保存到临时的内存空间，不会改变数据本来的类型或值。
- <u>可自动转换的类型一定能够强制转换，但需要强制转换的类型不一定能够自动转换</u>。
  - 自动类型转换：编译器根据代码的上下文环境自行判断的结果，但数据的精度可能会降低(精度损失)。
    - 一种类型的变量对另一种类型的变量进行<u>赋值时</u>发生。
    - 不同类型的<u>混合运算中</u>发生，参与运算的所有数据会先转换成同一类型再进行运算。
      - 转换按数据长度增加的方向进行，以保证精度不降低。
      - **所有浮点运算都是以双精度进行的**。即使只有float，也必须转换为double再进行运算。
      - char和short参与运算时，<u>必须先转换成int类型</u>。
  - 强制类型转换 `(type_name) expression`
  - 向上转型：C++类的继承与派生部分有涉及。
  - 向下转型

| 优先级(递减)                | 编译器执行(实参=>形参)                                       |
| --------------------------- | ------------------------------------------------------------ |
| 精确匹配                    | 直接匹配                                                     |
| -                           | 可忽略的转换：1.数组名=>数组指针; 2.函数名=>指向函数的指针; 3.非const =>const |
| 1.类型提升; 2.匹配;         | 整型提升：1. bool, char, short => int;                       |
| - 类型提升不会损失精度      | 小数提升：float => double                                    |
| 1.使用自动类型转换; 2.匹配; | 整型转换：1. char => long; 2. short => long; 3. int => short; 4. long => char; |
| - 不能保证数据正确性        | 小数转换：double => float                                    |
| - 不能保证数据应有的精度    | 整数和小数转换：1. short => float; 2.int => double; 3. float => int; 4. double => long; |
| -                           | 指针转换：int * => void *                                    |

```C++
/*
自动类型转换：
short ->
char  -> int -> unsigned int -> long -> double <- float
*/

/*
强制类型转换
*/
// eg1
int main(){
    int sum = 103;  //总数
    int count = 7;  //数目
    double average;  //平均数
    average = (double) sum / count; // sum/count得到的结果是int；注意！此处不同于 (double)(sum/count)
    printf("Average is %lf!\n", average);

    return 0;
}  

// eg2 函数重载决议时，同一优先级内有多个匹配的函数，编译器无法抉择。
void func(char ch){
    cout<<"#1"<<endl;
}

void func(long m){
    cout<<"#3"<<endl;
}

void func(double f){
    cout<<"#4"<<endl;
}

int main(){
    short s = 99;
    float f = 84.6;
  
    func('a');
    func(s); // 报错，编译器自动转化后匹配不到最优解func(int)，剩下的重载函数无法抉择。
    func(49); // 报错，编译器匹配不到最优解func(int)，剩下的重载函数无法抉择。
    func(f);
    return 0;
}
```

# 流程控制

# 函数

```c++
/*
C++引用作为函数参数、返回值
*/
int &function(int &a) {
  a += 10;
	return 0;
}

/*
内联函数
*/ 
inline function1(int a) {
  a += 1;
  return a;
}
```

## 内联函数

- 用`inline`关键字修饰的函数，叫做内联函数。
- 编译器会把内联函数体替换调用内联函数的地方，节省函数调用、参数传递、控制转移的开销。
- 内联函数体内不能有循环语句和`switch`语句。
- 内联函数的定义必须出现在第一次被调用之前。
- 对内联函数不能进行异常接口声明。

## 静态函数

`static void function() {}`

## 系统函数

# 预处理命令

- `#define` 与 `typeof`的区别
  - 宏定义只是简单的字符串替换，由预处理器处理；`typeof`是在编译阶段由编译器处理，并不是简单的字符串替换，而是给原有的数据类型起一个新的名字，将其作为一种新的数据类型。

| 指令     | 说明                                                        |
| -------- | ----------------------------------------------------------- |
| #        | 空指令，无任何效果。                                        |
| #include | 包含一个源代码文件。                                        |
| #define  | 定义宏。                                                    |
| #undef   | 取消已定义的宏。                                            |
| #if      | 如果给定条件为真，则编译下面代码。                          |
| #ifdef   | 如果宏已经定义，则编译下面代码。                            |
| #ifndef  | 如果宏没有定义，则编译下面代码。                            |
| #elif    | 如果前面的#if给定条件不为真，当前条件为真，则编译下面代码。 |
| #endif   | 结束一个#if……#else条件编译块。                              |

```c++
/*
宏定义
本质上为字符串替换，不替换被引号包围的宏名。
不需要带分号，如加上分号会将分号一起替换。
宏定义必须写在函数之外，其作用域为宏定义命令起到源程序结束。如要终止其作用域可使用#undef命令。
宏定义允许嵌套。
带参宏定义中，形参之间可以出现空格，但是宏名和形参列表之间不能有空格出现。
*/
#include <stdio.h> // c

#define PI 3.1415926    // 定义常数
#define S (PI*y*y) // 定义表达式
#define MAX(a,b) (a>b) ? a : b // 带参数宏定义，区别于函数
#define UINT unsigned int // 定义数据类型
#define SQ(y) ((y)*(y))
#define PIN1 int *
typedef int *PIN2;  //也可以写作typedef int (*PIN2);

int main() {
  PIN1 a, b; // int *a; int b;
  PIN2 a,b; // int *a; int *b;
  int x = 1;
  int y = 2;
  int max = MAX(x,y);
  while(x<=5){
  	printf("%d^2 = %d\n", x, SQ(x++)); // 3^2 = 1 5^2 = 9 7^2 = 25 区别带参宏定义与函数
  }
  return 0;
}
```

# 多文件编程

```c++
/*
# 编译器执行流程：
- 预处理：进行宏替换，条件编译，头文件展开，去掉注释。
- 编译：首先进行语法语义检错，无误后要将写好的C文件编译成汇编文件。
- 汇编：将汇编文件转换成可执行的机器指令。
- 链接：把所有的目标文件以及所依赖的库文件链接到一起生成可执行程序。

源代码.c + 头代码.h =(预处理)=> .i =(编译)=> 汇编代码.s =(汇编)=> 目标文件.o\.obj
目标文件.o\.obj + 静态库.a\.lib =(链接)=> 可执行文件.out\.exe

# 声明、定义、初始化的区别
- 变量
	- 声明：向编译器声明用到的变量，与内存分配无关。
	- 定义：给变量分配了内存空间。
	- 初始化：变量的内存空间里一开始存什么值，由初始化决定。之后的赋值不叫初始化。
- 函数
	- 声明：告诉编译器要使用这个函数，现在没有找到它的定义不要紧，请不要报错，稍后会把定义补上。
	- 定义：函数到底要执行什么具体操作。

# 头文件.h
- 可以声明函数，但不可以定义函数。
- 可以声明变量，但不可以定义变量。
- 可以定义宏，包括带参的宏和不带参的宏。
- 结构体的定义、自定义数据类型一般也放在头文件中。
*/

// eg1 全局变量与静态变量
int func(){
    // 也可以不赋初值 0，静态数据区的变量默认初始化为 0
    static int n = 0;
    n++;
    printf("Function is called %d times.\n", n);
    return n;
}
int main(){
    int i, n = 0;
    for(i = 1; i<=5; i++){
        func();
    }
    printf("n = %d\n", n);
    return 0;
}

// eg2 变量声明
datatype name = value; // 变量定义的形式，同时进行了初始化
datatype name; // 变量定义的形式
extern dataype name; // 变量的声明只有一种形式
extern dataytpe name = value; // 不推荐！变量也可以在声明的同时初始化
```

- 作用域
  - 函数原型作用域：`void func(int n);`
  - 局部作用域：`{}`
  - 类作用域：`class {};`
  - 命名空间作用域：`namespace {};`
- 变量
  - 全局变量：存储在全局数据区。
    - 全局数据区的数据在程序启动时就被初始化，一直到程序运行结束才会被操作系统回收内存。
    - **全局数据区的变量只能被初始化(定义)一次**，以后只能改变它的值，不能再被初始化，即使有这样的语句，也无效。
    - 全局变量和函数的作用域默认是整个程序，即所有的源文件。
  - 局部变量：存储在栈区。
  - 静态变量 `static`：存储在全局数据区。
    - 既可以修饰全局变量，还可以修饰局部变量。
    - 数据持久化：有利于将局部变量存储到全局数据区，使它不会随着函数调用结束而被销毁，但其作用域仅限于该函数。
    - 隐藏效果：通常将不需要被其他模块调用的全局变量或函数用 `static`关键字来修饰，static 能够将全局变量和函数的作用域限制在当前文件中，在其他文件中无效。
  - 外来变量 `extern`：适用于声明变量和函数。
    - 对于声明函数而言，extern非必须。
    - 对于声明变量而言，extern必须。有extern才是声明，没有extern就是定义。
- 模板的多文件

# 标准库

- `<stdio.h>`：http://c.biancheng.net/cpp/u/stdio_h/
  - 输入\输出设备
    - `void scanf("[flag][width][.precision]type", address)`：
      - 从键盘输入的数据并没有直接交给 scanf()，而是放入了缓冲区中，直到我们按下回车键，scanf() 才到缓冲区中读取数据。如果缓冲区中的数据符合 scanf() 的要求，那么就读取结束；如果不符合要求，那么就继续等待用户输入，或者干脆读取失败。
      - 可以一次性读取多份类型相同或者不同的数据。
      - 没有回显。
    - `void gets()`：输入一行字符串(**空格有效**)。
      - scanf() 读取字符串时以空格为分隔，遇到空格就认为当前字符串结束了，所以无法读取含有空格的字符串。
      - gets() 认为空格也是字符串的一部分，只有遇到回车键时才认为字符串输入结束。不管输入了多少个空格，只要不按下回车键，对 gets() 来说就是一个完整的字符串。
      - 有回显。
    - `char getchar()`：输入单个字符，缓冲区机制和回显方面与scanf()不同。
    - `char getche()`：输入单个字符，缓冲区机制和回显方面与scanf()不同。
    - `char getch()`：输入单个字符，缓冲区机制和回显方面与scanf()不同。
    - `void printf("[flag][width][.precision]type", varble)`
    - `void puts()`：输出字符串，并且输出结束后自动换行。
    - `void putchar(char)`：输出单个字符。
  - 文件流
    - `char fgetc(FILE *fp)`：从文件流中读取单个字符。
    - `int fputc(int ch, FILE *fp)`：单个字符写入文件流。
    - `char *fgets(char *str, int n, FILE *fp)`：从文件流中读取一行字符串，并保存到数组中。
    - `int fputs(char *str, FILE *fp)`：字符串写入文件流。
    - `size_t fread(void *ptr, size_t size, size_t count, FILE *fp)`：**文件流中读取数据(二进制形式)**。
    - `size_t fwrite(void *ptr, size_t size, size_t count, FILE *fp)`：向文件流写入数据(二进制形式)。
    - `int fscanf(FILE *fp, char format, ...)`：**文件流中的数据格式化输入**，仅比scanf()多了文件指针，即**文件读出操作**。
    - `int fprintf(FILE *fp, char format, ...)`：格式化数据输出到文件流，即文件写入操作。
    - `rewind(FILE *fp)`：将位置指针重定位到文件开头。
    - `int fseek(FILE *fp, long offset, int origin)`：将位置指针移动到任何位置，一般用于二进制文件。
    - `int fflush(FILE *fp)`：清空文件\标准输入输出的缓冲区。
    - `long ftell(FILE *fp)`：获取位置指针距离文件开头的字节数，<u>要以二进制方式打开文件</u>。
    - `FILE *freopen(char *filename, char *type, FILE *stream)`：文件流重定向，流替换。
- `<stdlib.h>`
  - `malloc()`
  - `realloc()`
  - `free()`
- `<math.h>`
- `<string.h>`

- C++ ：http://c.biancheng.net/cplus/80/