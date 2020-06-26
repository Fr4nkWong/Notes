# 数据类型
## 基本类型
- 定点数
  - `char`
  - `short`
  - `int`
  - `unsigned`
  - `long`
  - `bool `(C++新增)
- 浮点数
  - `float`
  - `double`
- 枚举型`enum`

```C++
/*
数据运算：
- 取余操作 %
	- 取余只能针对整数。
	- 余数可正可负，由%左边决定。
*/
```

## 复合类型

###数组`[]`

- 定义:
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

###指针`*`

- 常指针 `const *p`
- 引用 `&a = b`：C++
  - 引用类似别名，本质上是对指针的一层封装。定义时加&，使用时不加&取值，加&取地址。
  - **引用必须定义时初始化，并且要从一而终**，不能再引用其他数据。
  - 引用不能绑定临时数据。表达式的结果一般放入寄存器，&取不到地址。注意！&只能取内存里的地址。
  - 引用与指针的区别：
    - 引用和指针都需要类型严格一致，但常引用允许类型不一致，类型会转化。
    - 指针可以有多级，但引用只能有一级。
    - 自增、自减意义不同。指针的++表示指向下一份数据(存的地址+1)，引用的++表示所指的数据+1，自减同理。
    - 类型不一致场景下，编译器禁止指针指向不同类型的数据。
  - 常引用 `const int &a`
    - 引用和指针都不能绑定到临时数据，但**常引用可以绑定临时数据**。绑定临时变量时，编译器会为其创建新的、无名的临时变量(只要是变量都会被分配内存)。
    - const关键字可以避免意外修改数据。
    - **常引用遵循类型的自动转换原则**，临时变量和引用的类型一样。
    - 常引用能让函数接收const和非const类型的实参，否则只能接收非const类型实参。

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

### 结构体`struct`

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

### 共用体`union`

- 结构体和共用体的区别：
  - 结构体的各个成员会占用不同的内存，互相之间没有影响；
  - 而共用体的所有成员占用同一段内存，修改一个成员会影响其余所有成员，共用体占用的内存等于最长的成员占用的内存。
- 共用体使用了**内存覆盖**技术，同一时刻只能保存一个成员的值，如果对新的成员赋值，就会把原来成员的值覆盖掉。

## 静态变量`static`

- 隐藏：程序有多个模块时，将全局变量或函数的作用范围限制在当前模块，对其他模块隐藏。
- 保持变量内容的持久化：将局部变量存储到全局数据区，使它不会随着函数调用结束而被销毁。

## 常量`const`

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

## 运算符

- 参考 http://c.biancheng.net/view/161.html

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

## 重载函数

C++

- 函数的重载只与<u>函数名、参数个数、参数类型</u>有关。
- 重载函数存在多个参数时的二义性(C++标准) 
  - 函数对每个实参的匹配都不劣于其他函数。
  - 至少有一个实参的匹配优于其他函数。

## 内联函数`inline`

- 用`inline`关键字修饰的函数，叫做内联函数。
- 编译器会把内联函数体替换调用内联函数的地方，节省函数调用、参数传递、控制转移的开销。
- 内联函数体内不能有循环语句和`switch`语句。
- 内联函数的定义必须出现在第一次被调用之前。
- 对内联函数不能进行异常接口声明。

## 静态函数`static`

## 系统函数

# 预处理命令`#`

- `#define`与`typeof`的区别
  - 宏定义只是简单的字符串替换，由预处理器处理；`typeof`是在编译阶段由编译器处理，并不是简单的字符串替换，而是给原有的数据类型起一个新的名字，将其作为一种新的数据类型。
- `#define`与`const`的区别
  - C++中的const更像编译阶段的`#define`，都是一个值替换的过程，不过`#define`在预处理阶段替换，const在编译阶段替换。注意！C和C++对const的处理是有区别的，<u>C对const的处理和普通变量一样</u>，都会去内存中读取数据。具体参考例子1。

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
#include <iostream> // c++

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

// eg1 C,C++对const处理的区别
const int m = 10; // C中编译器会先从m所在内存去除一份数据再赋给n
int n = m; // C++中编译器直接将10赋给n，没有读取内存的过程！
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

# 类&对象`class`

C++

## 抽象&封装

### 构造函数

- 构造函数命名必须与类名保持一致。

- 调用顺序：基类的构造函数(继承链顺序) -> 成员对象的构造函数(声明顺序) -> 派生类的构造函数

- 默认构造函数
  - 一个类必须要有构造函数，自己未定义编译器也会定义。
  - 构造函数也符合重载规则。
  
- 初始化列表
  - const 成员和引用成员必须在构造函数的初始化列表中初始化，此后值不可修改
  - 初始化列表中初始化顺序只与类中属性的声明顺序有关。
  
- 拷贝构造函数：`类名 (类名 &对象);`

  - 当用类的一个对象去初始化该类的另一个对象时。

  - 如果函数的形参是类的对象，调用函数时，进行形参和实参结合时。只有把对象用值传递时才会调用拷贝构造函数；如果传递引用，则不会调用拷贝构造函数。

  - 如果函数的返回值是类的对象，函数执行完成返回调用者时。

  - 如果一个类拥有指针类型的成员变量，那么绝大部分情况下就需要深拷贝。因为只有这样，才能将指针指向的内容再复制出一份来，让原有对象和新生对象相互独立，彼此之间不受影响。
  
  - ```c++
    class Point {
      public:
      	Point(double x, double y);
      	Point(Point &p); // 拷贝构造函数
        getX(Point p);
      private:
      	double x,y;
    }
    
    Point::Point(Point &p) {
      x = p.x;
      y = p.y
    }
    
    Point::getX(Point p) {
      return x;
    }
    
    void func1(Point p) {
      cout<<p.getX()<<endl;
    }
    
    Point func2(Point p) {
      Point a(1,2);
      return a;
    }
    
    int main() {
      Point a(1,2);
      Point b(a); // 拷贝构造函数被调用，深拷贝
      func1(a); // 调用拷贝构造函数，深拷贝
      Point c = func2(); // 调用拷贝构造函数，深拷贝
      return 0;
    }
    
    /*
    拷贝构造函数
    */
    Student {
      public:
      	Student(const Student &stu);
      private:
      	char *name;
    };
    
    Student::Student(const Student &stu) {
      this->name = stu.name;
      memcpy(this->name, stu->name, 20*sizeof(char)); // 实现浅拷贝
    }
    
    Student stu1('a'); // 初始化对象
    Student stu2(stu1); // 调用拷贝构造
    Student stu3 = stu1; // 调用拷贝构造，默认构造函数属于浅拷贝
    void func(Student s) {
      Student s('b');
      return s;
    }
    Student stu4 = func(stu1); // 拷贝实参作为形参，返回值拷贝赋值给stu4
    ```
### 析构函数

- 一种特殊的成员函数，没有参数和返回值，无法被显式调用，在销毁对象时自动执行。
- 析构函数没有参数，不能重载，因此一个类只能有一个析构函数。
- 若未给类定义析构函数，编译器会为其自动生成一个析构函数。
- 析构函数命名规则为：`~` + 类名。

### 封闭类

- 包含**成员对象**的类叫做封闭类。
- 封闭类对象生成时，先执行所有成员对象的构造函数，再执行自己的构造函数。**成员对象构造函数执行顺序只与成员对象在类中定义的次序一致**。
- 封闭类对象销毁时，先执行封闭类析构函数，再执行所有成员对象的析构函数。成员对象析构函数执行顺序与构造函数执行顺序相反。

``` C++
/*
- 类的成员 = 成员变量 + 成员函数
- 成员函数之间可相互调用，成员函数内部可访问成员变量；私有成员只能在类的成员函数内部访问。
- 默认情况下，class的成员是私有的，struct的成员是共有的。
- 任何生成对象的语句都要说明对象是用哪个构造函数初始化的。
- 对象的内存中只包含成员变量，存储在栈区或堆区。成员变量的地址按照派生的层级依次排列，发生遮蔽并不会抹去基类的成员。
- 成员函数与对象内存分离，存储在代码区。
*/

class Demo;	 // 提前声明，不然Sample里会报错，但还无法创建Demo对象
class Sample {
  	private:
  		int number;
	  public:
  		friend void Demo::testFriend1(Sample *sample);
};

class Sample1 {}

class Demo {	// 类的定义
	private:
		const int _a;
		char _b;
		int *_arr;				// 成员属性
  	Sample _sample; 	// 成员对象
  private:
  	static int count;
	public:
  	Demo();
		Demo(int, char, int);	// 构造函数
		~Demo();					// 析构函数
		void show();			// 成员方法
  	static getCount();	// 静态成员函数的声明
  	int getA() const;		// 常成员函数的声明
  	friend void testFriend();	// 友元函数的声明
  	void testFriend1(Sample *sample);
  	friend class Sample1; // 声明Sample1是Demo的友元，其可以对Demo进行查看
};

Demo::Demo(int a, char b, int c): _b(b), _a(a), _sample(c) { // 初始化列表
	this->_arr = new int[10];
  count++;
}

Demo::~Demo() {
	delete[] _arr;
}

void Demo::show() {}	// 成员方法的定义

int Demo::getCount() {	// 静态成员函数的定义
  return count;
}

int Demo::getA() const {}	// 常成员函数的定义

Demo::count = 1;	// 类访问static

void testFriend(Demo *demo) {	// 友元函数的定义
  // 非成员函数，但可通过指针进行访问其对象成员
}	

void Demo::testFriend1(Sample *sample) {
  // Sample里的需要传入指针，在函数内可随意访问其私有成员
}

int main() {
  Demo demo1(4, 'f');	// 类实例化对象
  Demo *demo2 = new Demo(7, 'w');	// 类实例化对象
  demo1.count = 2;	//对象访问static
  const Demo demo3();	// 定义常对象
  Demo const demo4();
  const Demo *demo5 = new Demo();	
  testFriend(&demo1);		// 调用友元函数
  Sample *sample = new Sample();
  demo1.testFriend1(sample);
  delete demo2;
  return 0;
}
```

## 共享&保护

### this指针

- `this`是C++的关键字，也是一个**const指针**，指向当前对象，通过它可以访问当前对象的所有成员(无论private,protected,public)。
- this虽然用在类的内部，但只有在对象创建后才会给this赋值，且赋值过程由编译器自动完成。
- **this只能在普通成员函数内使用**(static除外)。
- this本质上是成员函数的隐式形参，即成员函数的局部变量，只有在通过对象调用成员函数时才给this赋值。

### 静态成员`static`

- 静态成员变量
  - 静态成员变量**必须初始化且在类声明的外部进行**，其**内存仅在初始化时分配**，默认值为0。
  - 静态成员变量在其所有对象之外开辟内存，即使不创建对象也可以访问。
  - 静态成员变量与静态变量类似，都在全局数据区中分配内存，也意味着静态成员变量所占内存不会因为对象销毁而释放。
  - 一个类中可以有多个静态成员变量，其**所有对象共享这些静态成员变量**，访问的是同一块内存。
  - 静态成员变量可通过类名、对象名访问，但都遵循访问权限限制(private,protected,public)。
- 静态成员函数
  - 静态成员函数没有this指针，故**静态成员函数只能访问静态成员**；普通成员函数有this指针，可以访问类中任意成员。
  - 同静态成员变量一样，静态成员函数在定义时不加`static`。
### 常成员`const`
- const成员变量
  - 用法与普通成员变量类似，但**只能通过构造函数的初始化列表进行初始化**。
- const成员函数
  - `const char*function();`表示函数返回值是const类型。
  - `char *function() const;`表示**常成员函数**，只能读取成员变量的值，不能修改成员变量的值。
  - 常成员函数的声明与定义必须都加上`const`关键字。
- const对象
  -  常量对象上面不能执行非常量成员函数，只能访问const成员。
- const引用：`const int &a`

### 友元`friend`

- 友元函数：`friend`+函数
  - 借助`friend`在当前类以外定义的、不属于当前类的函数也可以在类中声明**，**可以使其他类中的成员函数以及全局范围内的函数**可通过对象访问当前类的private成员**。
  - friend声明后，友元函数内可以访问当前类中的所有成员，包括public,protected,private；非friend下，通过对象可以访问public成员，只有本类的函数才能访问private成员。
  - **1.将非成员函数声明为友元函数；2.将其他类的成员函数声明为友元函数**
  - 一个函数可以被多个类声明为友元函数，让其可以访问多个类中的private成员。
- 友元类：`friend`+类
  - 若B类是A类的友元类，那么B中的所有成员函数都是A的友元函数，可以访问类A的所有成员，包括public,protected,private。
- **友元关系是单向的**而非双向。
- **友元关系不能传递**。

## 继承&派生

- 继承`Inheritance`：一个类从另一个类获取成员变量和成员函数的过程。
  - 被继承的类称为父类、基类，继承的类被称为子类、派生类。父类派生了子类，子类继承了父类。
  - 派生类除了拥有基类的成员，还能拥有自己的成员。
  - 基类成员函数和派生类成员函数不构成重载，**只要函数名字相同就会造成遮蔽**，变量同理。
  - **类也是一种作用域**，派生类的作用域嵌套在基类的作用域内。
  - 类的**构造函数不能被继承**，一般的做法是在派生类的构造函数中调用基类的构造函数。
  - 派生类的构造函数只能调用直接基类的构造函数，间接基类的构造函数是无法调用的。
  - **析构函数也不能被继承**，派生类的析构函数不用显式调用基类析构函数，交给编译器处理。
    - 创建派生类时，构造函数的执行顺序和继承顺序相同，即先父后子。
    - 销毁派生类时，析构函数的执行顺序与继承顺序相反，即先子后父。
- 继承场景：
  - 设计出的新类与现有类相似，只是多出若干成员时...
  - 需要创建多个类，它们拥有很多相似的成员时，考虑抽出基类...
- 继承方式：
  - **用来指明基类成员在派生类中的最高访问权限的**。
  - 如果希望基类的成员既不向外暴露(不能通过对象访问)，还能在派生类中使用，那么只能声明为 protected。
  - **基类的 private 成员是能够被继承的**，并且（成员变量）会占用派生类对象的内存，它只是在派生类中不可见；**在派生类中访问基类 private 成员的唯一方法就是借助基类的非 private 成员函数**。
  - `using`关键字：using 只能改变基类中 public 和 protected 成员的访问权限，不能改变 private 成员的访问权限。
- 多重继承：`class C: public A, public C {};`
  - C++既支持单继承，也支持多继承，即派生类可以有多个基类。容易产生二义性，故一般不推荐此用法！
  - 多重继承时，基类构造函数的调用顺序只与声明派生类时基类出现的顺序相同。
  - 多重继承时，基类对象在内存中的排列顺序和继承时声明的顺序相同。
- **类的权限访问控制**：
  
  - `public`：类的public成员可通过类的成员函数、实例访问。
  
  - `protected`：类的protected成员无法通过类的实例访问，但可通过类的友元函数、友元类访问。
  
  - `private`：类的private成员无法通过类的实例访问，但可以通过类的友元函数、友元类访问。
  
  - | 继承方式/基类成员 | public成员 | protected成员 | private成员 |
    | ----------------- | ---------- | ------------- | ----------- |
    | public继承        | public     | protected     | 不可见      |
    | protected继承     | protected  | protected     | 不可见      |
    | private继承       | private    | private       | 不可见      |
- 虚继承&虚基类：`class B: virtual public A {};`
  - 让派生类中只保留一份间接基类的成员，即让对象对应的内存中只保留一份虚基类的成员，解决了多继承时的命名冲突和数据冗余问题，参考"棱形继承问题"。
  - 必须在虚派生的真实需求出现前就已经完成虚派生的操作，虚派生只影响从指定了虚基类的派生类中进一步派生出来的类，它不会影响派生类本身。
  - 虚继承中，虚基类是由最终的派生类初始化的，**最终的派生类的构造函数必须要调用虚基类的构造函数**。对于最终的派生类来说，虚基类是间接基类，这与普通继承是不同的！
  - 虚继承中，构造函数的调用：虚基类构造函数 -> 按出现的顺序调用其他构造函数
  - 对于虚继承，将派生类分为固定部分和共享部分，并把共享部分放在最后。
  - 不管是虚基类的直接派生类还是间接派生类，虚基类部分数据放在共享部分。
  - 当存在多继承时，派生类对象会维护一个虚基类表。
- **向上转型**Upcasting：类其实也是一种数据类型，也可以发生数据类型转换，不过这种<u>转换只有在基类和派生类之间才有意义，并且只能将派生类赋值给基类</u>。

  - 向上转型非常安全，可以由编译器自动完成；向下转型有风险，需要程序员手动干预。同理，同一基类的不同对象之间也不能赋值，参考内存模型。
  - 向上转型后通过基类的对象、指针、引用只能访问从基类继承过去的成员（包括成员变量和成员函数），不能访问派生类新增的成员。
  - 分类：派生类对象赋值给基类对象、派生类指针赋值给基类指针、派生类引用赋值给基类引用。

```c++
class People {
  private: // 权限声明的是基类中成员在基类中的权限等级
  	char *_name;
  	int _age;
  public:
  	People(char *name, int age);
  	char *getName();
  	int getAge();
  	void say();
  	void hello();
}

void People::say() {
  cout<<"people:"<<_age<<endl;
}

class Student: public People { // 声明继承关系，权限声明的是基类的成员在派生类里的权限等级
  private:
  	int _score;
  public:
  	Student(char *name, int age, int score);
  	getScore();
  	say();	// 子类方法重名父类方法
}

Student::Student(char *name, int age, int score):People('f', 17), _score(score){
  // 初始化列表调用了基类构造函数，此处的参数是实参！
}

void Student::say() {
  cout<<"student:"<<_age<<_score<<endl;
}

int main() {
  Student stu('frank', 23);
  stu.show();
  stu.People::show(); // 调用父类里的重名方法
  People *people = new People('a', 17);
  Student *student = new Student('b', 18);
  people = student;
  people->say(); // 18
  People *people1 = new Student('c', 19，100);
  people1->say(); // people 19
  return 0;
}

// 向上转型***
/*
派生类对象赋值给基类对象
- 对象之间的赋值是成员变量的赋值，成员函数不存在赋值问题。
- 只能用派生类对象赋值给基类对象，**对象的指针必须指向对象的起始位置**，这里一定要根据内存模型画出图理解。
*/
A a(10);
B b(66, 99);
a = b

/*
派生类指针赋值给基类指针
- 基类对象调用基类成员函数，使用的是派生类对象的成员变量。
- 编译器**通过指针访问成员变量**，指针指向哪个对象就访问哪个对象的数据；编译器**通过指针的类型访问成员函数**，指针属于哪个类型就使用哪个类的成员函数。
*/
A *a = new A(10);
B *b = new B(66, 99);
a = b;	// 指针指向B类对象，指针类型是A类
  
/*
派生类引用赋值给基类引用
*/
B b(66, 99);
A &a = b;
```

## 多态&虚函数`virtual`

- 多态的定义：使基类指针有多种同名函数的表现方式，称为**多态**，这也是虚函数的唯一作用。
- 多态的类型及应用场景：
  - 重载多态：函数重载、类的方法重载。
  - 强制多态：数据类型转换。
  - 包含多态：当基类指针指向派生类对象时，通过基类指针只能访问基类的成员变量，但不能访问派生类中覆盖的成员函数。多态就是要可以通过基类指针对所有派生类(包括直接派生和间接派生)的成员变量和成员函数进行“全方位”的访问，尤其是成员函数。如果没有多态，我们只能访问成员变量。
  - 参数多态：模板。
- **构成多态的条件**：
  - 必须存在继承关系。
  - 继承关系中必须有同名的虚函数，并且他们是覆盖关系(函数原型相同)。
  - 存在基类的指针，通过该指针调用虚函数。
- 虚函数：Virtual Function，声明处加`virtual`关键字，虚函数定义处可加可不加。
  - 声明虚函数的作用：让基类指针既可以按基类的方式执行方法，也可以按派生类的方式执行方法。
  - 执行规则：**虚函数是根据指针的指向来调用，指针指向哪个类的对象就调用哪个类的虚函数**。这与非虚函数下纯粹通过指针类型进行判断的逻辑是不同的。
  - 派生类较多时，有了多态，只需要定义一个指针变量就可以调用所有派生类的虚函数。
  - 只需要将基类中的函数声明为虚函数，这样所有派生类中的同名函数都将自动成为虚函数。
  - 当在基类中定义了虚函数时，如果派生类没有定义新的函数来遮蔽此函数，那么将使用基类的虚函数。
  - 只有当派生类的虚函数覆盖基类的虚函数(**函数原型相同**)时，才能构成多态(通过基类指针访问派生类函数)。非虚函数下，只要同名就会覆盖！
  - 构造函数不能是虚函数；析构函数可以声明为虚函数，而且有时候必须要声明为虚函数。
  - **虚析构函数**：在派生类对象赋值给基类指针时，将基类的析构函数声明为虚函数，使得编译器忽略指针的类型，而根据指针的指向来选择函数。否则，在销毁阶段只会执行基类析构函数，而派生类析构函数不会执行。
- 抽象类：Abstract Class，包含纯虚函数的类。
  - <u>抽象类无法实例化</u>，通常作为基类，让派生类去实现纯虚函数，<u>派生类必须实现纯虚函数才能被实例化</u>，否则派生类依然视为一个抽象类。
  - **纯虚函数**：
    - `virtual float area() = 0;`
    - 纯虚函数没有函数体，不是完整的函数，无法调用，也无法为其分配内存空间。
    - 一个纯虚函数可以使类成为抽象类，但抽象基类中除了包含纯虚函数外，还可包含其他成员函数和成员变量。
    - 只有类中的虚函数才能被声明为纯虚函数，普通函数和顶层函数均不能声明为虚函数。
  - 抽象类功能：
    - 约束派生类的功能。
    - 实现多态。
  - 虚函数表`vtable`：
    - 如果一个类包含了虚函数，那么在创建该类的对象时就会额外地增加一个数组，数组中的每一个元素都是虚函数的入口地址。但数组和对象是分开存储的！
    - 为了将对象和数组相关联起来，编译器还要在对象中安插一个指针`vfptr`，指向数组的起始位置。
    - 基类的虚函数在 vtable 中的索引(下标)是固定的，不会随着继承层次的增加而改变，派生类新增的虚函数放在 vtable 的最后。
    - 派生类对象中，若有同名的虚函数覆盖了基类的虚函数，那么在虚函数表中用派生类的虚函数替换基类的虚函数，派生类对象中这样具有遮蔽关系的虚函数在vtable中只会出现一次。
  - `typeid`运算符：获取一个表达式的类型信息。
    - 判断两个类型是否相等。
      - 内置类型比较：`typeid(a) == typeid(b)`
      - 类的比较。
    - `type_info`类：位于`typeinfo`头文件。

```c++
//基类People
class People{
public:
    People(char *name, int age);
    virtual void display();  //声明为虚函数
  	void say() {};
protected:
    char *m_name;
    int m_age;
};
People::People(char *name, int age): m_name(name), m_age(age){}
void People::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是个无业游民。"<<endl;
}

//派生类Teacher
class Teacher: public People{
public:
    Teacher(char *name, int age, int salary);
    void display();  //声明为虚函数
private:
    int m_salary;
};
Teacher::Teacher(char *name, int age, int salary): People(name, age), m_salary(salary){}
void Teacher::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是一名教师，每月有"<<m_salary<<"元的收入。"<<endl;
}
int main(){
    People *p = new People("王志刚", 23);
    p -> display();
    p = new Teacher("赵宏佳", 45, 8200);
    p -> display();
  
    People p1("王志刚", 23);
    Teacher t("赵宏佳", 45, 8200);
    People &rp = p1;	// 引用也可以实现多态，不过引用不能随意更改指向
    People &rt = t;
    rp.display();
    rt.display();
    return 0;
}
```

## 对象内存模型

- 没有虚函数、没有虚继承：对象内存中只有成员变量，成员函数在代码区。
- 类中有虚函数：对象内存中会额外添加一个虚函数表(数组)，并在对象内存中插入一个指针，指向虚函数表。
- 类中有虚继承：对象内存中会额外添加一个虚基类表(数组)，并在对象内存中插入一个指针，指向虚基类表。
- RTTI机制(C++运行时识别机制)
  - 静态绑定：在编译期间(包括链接期间)就能找到函数名对应的地址，完成函数的绑定，程序运行后直接使用这个地址即可。
  - 动态绑定：在编译期间想尽所有办法都不能确定使用哪个函数，必须要等到程序运行后根据具体的环境或者用户操作才能决定。本质上是，编译器在编译期间不能确定指针指向哪个对象，只能等到程序运行后根据具体的情况再决定。

# 模板`template`

C++

- 泛型编程(Generic Programming)：一种编程方式，即模板所支持的类型是宽泛的、没有限制的，可以使用任意类型来替换。
- **模板类不同于类模板，模板函数不同于函数模板！**
- 模板显式具体化
  - 优先级：非模板函数 > 显式具体化函数模板 > 函数模板
- 非类型参数：非类型参数的类型不能随意指定，它受到了严格的限制，只能是一个整数，或者是一个指向对象或函数的指针(也可以是引用)。
  - 非类型参数为整数：传递给它的实参，或者由编译器推导出的实参必须是一个常量表达式。
  - 非类型参数为指针或引用：绑定到该指针的实参必须具有静态的生存期，即实参必须存储在虚拟地址空间中的静态数据区。局部变量位于栈区，动态创建的对象位于堆区，它们都不能用作实参。
- 模板实例化：即由模板生成函数或类的过程。相应地，针对某个类型生成的特定版本的函数或类叫做模板的一个实例。
  - 编译器会根据传递给类型参数的实参(也可以是编译器自己推演出来的实参)来生成一个特定版本的函数或类，并且相同的类型只生成一次。实例化的过程也很简单，就是将所有的类型参数用实参代替。
  - **模板并不是真正的函数或类**，它仅仅是编译器用来生成函数或类的一张“图纸”。
    - 模板不会占用内存，最终生成的函数或者类才会占用内存。
    - 模板的实例化是按需进行的，用到哪个类型就生成针对哪个类型的函数或类，不会提前生成过多的代码。
    - 模板的实例化是在调用函数或者创建对象时由编译器自动完成的，而不是由链接器完成的，即**隐式实例化**。这可能会导致在链接期间找不到对应的实例，故不能将模板的声明和定义分散到多个文件中！
    - 在实例化过程中需要知道模板的所有细节，包含声明和定义。
  - 显式实例化
    - 必须将显式实例化的代码放在包含了模板定义的源文件中，而不是仅仅包含了模板声明的头文件中。**模板的声明和定义可分散到不同文件中**，否则模板的声明与定义只能放同一文件。
    - 显式实例化也包括声明和定义，定义要放在模板定义(实现)所在的源文件，声明要放在模板声明所在的头文件(当然也可以不写)。
      - 调用文件中声明：`extern template decalaration;`
      - 定义文件中定义：`temolate decalaration;`
      - 对于函数模板来说，declaration 就是一个函数原型；对于类模板来说，declaration 就是一个类声明。
    - 显式实例化一个类模板时，会一次性实例化该类的所有成员，包括成员变量和成员函数。
    - 缺陷：C++支持显式实例化的目的是为模块化编程提供解决方案，但是程序员必须在模版的定义文件中对所有使用到的类型进行实例化，并且模板调用文件和模版定义文件必须同步更新，若一个模板在多个文件中有调用到，同步略显麻烦。<u>故规范一点的写法还是采用模版声明和定义都放到一个头文件中</u>。

## 函数模板

  - Function Template
    - 实际上是建立一个通用函数，它所用到的数据的类型(包括返回值类型、形参类型、局部变量类型)可以不具体指定，而是用一个虚拟的类型来代替(实际上是用一个标识符来占位)，等发生函数调用时再根据传入的实参来逆推出真正的类型。
    - 函数模板除了支持值的参数化，还支持类型的参数化。一但定义了函数模板，就可以将类型参数用于函数定义和函数声明了。
    - 函数模板也可以提前声明，不过声明时要带上模板头，并且模板头和函数定义(声明)是一个不可分割的整体，它们可以换行，但中间不能有分号。
    - C++ 允许对函数模板进行重载，程序员可以像重载常规函数那样重载模板定义。
  - 隐式传参 & 实参推断 & 类型转换：
    - 相比于普通函数的类型转换，**函数模板的隐式类型转换**有更多限制。
    - 仅能进行`const 转换`和`数组或函数指针转换`，其他的都不能应用于函数模板。
    - **当函数形参是引用类型时，数组不会转换为指针。**
    - 算数转换。
    - 派生类向基类转换，即<u>向上转型</u>。
    - const 转换：也即将非 const 类型转换为 const 类型。
    - 数组或函数指针转换：如果函数形参不是引用类型，那么数组名会转换为数组指针，函数名也会转换为函数指针。
  - 显式传参：指明实参的类型时，可正常使用类型转换。

## 类模板
  - Class Template
    - 一但声明了类模板，就可以将类型参数用于类的成员函数和成员变量了。
    - 模板头和类头是一个整体，可以换行，但是中间不能有分号。
    - 与函数模板不同的是，类模板在实例化时必须显式地指明数据类型，编译器不能根据给定的数据推演出数据类型，故**赋值号两边都要指明具体的数据类型，且要保持一致。**
  - <u>部分显式具体化</u>(仅类模板)： 
    - C++ 还允许只为一部分类型参数提供实参。
    - 类名后面之所以要列出所有的类型参数，是为了让编译器确认“到底是第几个类型参数被具体化了“。
  - 非类型参数：与函数模板非类型参数相关规则同理。
  - 继承
      - 类模板继承类模板
      - <u>类模板从模板类派生</u>
      - 类模板从普通类派生
      - 普通类从模板类派生
  - 友元：以编译器转换后的对象的角度去审视友元。
      - 函数、类、类的成员函数作为类模板的友元
      - 函数模板作为类模板的友元
      - 函数模板作为类的友元
      - 类模板作为类模板的友元
  - 静态成员

``` c++
/*
函数模板
*/
// eg1
template<typename T> 
void swap(T *a, T *b) {
    T temp = *a;
    *a = *b;
    *b = temp;
}

template<template T1, T2>	// 模板头，这里不能有分号
T2 sum(T1 a, T1 b) {		// 
  return (a+b);
}

template<typename T> 
void Swap(T a[], T b[], int len){ // a[] 浅拷贝
    T temp;
    for(int i=0; i<len; i++){
        temp = a[i];
        a[i] = b[i];
        b[i] = temp;
    }
}

int main() {
  float f1 = 12.5, f2 = 56.93;
  swap(f1, f2);
  cout<<f1<<", "<<f2<<endl;
  return 0;
}

// eg2 隐式判断
template<typename T> void func1(T a, T b);
template<typename T> void func2(T *buffer);
template<typename T> void func3(const T &stu);
template<typename T> void func4(T a);
template<typename T> void func5(T &a);

int name[20];
Student stu1("frank", 20, 96.5);  //创建一个Student类型的对象
func1(12.5, 30);  //Error
func2(name);  //name的类型从 int [20] 换转换为 int *，所以T的真实类型为 int
func3(stu1);  //非const转换为const，T 的真实类型为 Student
func4(name);  //name的类型从 int [20] 换转换为 int *，所以T的真实类型为 int *
func5(name);  //name的类型依然为 int [20]，不会转换为 int *，所以T的真实类型为int[20]

// eg3 显式判断
// 只有尾部（最右）的类型参数的实参可以省略，而且前提是它们可以从传递给函数的实参中推断出来。
template<typename T1, typename T2> 
void func(T1 a) {
  return T2 b;
}

template<typename T> 
void func2(T a, T b) {
  return (a+b);
}

func(10); // 报错！编译器只能从函数调用中判断出T1类型，无法判断T2类型
func<int>(10);  // 省略T2的类型，T2的类型属于最右参数，故能够自动推断出来
func<int, int>(20);  // 指明T1、T2的类型
func2(10, 23.5); // 报错！无法判断T是int还是float
func2<float>(20, 23.5); // 正确！

// eg4 函数模板 显式具体化
template<typename T> 
const T& Max<T>(const T &a, const T &b) {}
template<> 
const Point& Max<Point>(const Point &a, const Point &b) {}

// eg5 非类型参数
template<typename T, unsigned N> void Swap(T (&a)[N], T (&b)[N]){
    T temp;
    for(int i=0; i<N; i++){
        temp = a[i];
        a[i] = b[i];
        b[i] = temp;
    }
}

int len;
cin>>len;
int a[len];
int b[len];
Swap(a, b); // 报错！
Array<int, len> arr; // 报错！
  
/*
模板类
*/
// eg1
template<typename T1, typename T2>  //模板头，这里不能有分号
class Point{	// 类头
public:
    Point(T1 x, T2 y): m_x(x), m_y(y){ }
public:
    T1 getX() const;  //获取x坐标
    void setX(T1 x);  //设置x坐标
    T2 getY() const;  //获取y坐标
    void setY(T2 y);  //设置y坐标
private:
    T1 m_x;  //x坐标
    T2 m_y;  //y坐标
};

template<class T1, class T2>  //模板头
T1 Point<T1, T2>::getX() const /*函数头*/ {  // 定义模板类里的模板函数
    return m_x;
}

int main() {
  Point<int, float> p2(10, 15.5);
  Point<float, char*> p3(12.4, "东经180度");	// 栈
  Point<char*, char*> *p = new Point<char*, char*>("东经180度", "北纬210度"); // 堆
  Point<float, float> *p = new Point(10.6, 109); // 错误！赋值号右边没有指明数据类型
  Point<float, float> *p = new Point<float, int>(10.6, 109); // 错误！两边不一致
  return 0;
}

// eg2 类模板的显示具体化
template<> 
class Point<char*, char*>{
public:
    Point(char *x, char* y): m_x(x), m_y(y){ }
public:
    char *getX() const{ return m_x; }
    void setX(char *x){ m_x = x; }
    char* getY() const{ return m_y; }
    void setY(char* y){ m_y = y; }
    void display() const;
private:
    char *m_x;  //x坐标
    char* m_y;  //y坐标
};

void Point<char*, char*>::display() const{ //这里不能带上模板头!!!
    cout<<"x="<<m_x<<" | y="<<m_y<<endl;
}

int main() {
  Point<char*, char*> p = new Point<char*, char*>("东京180度", "北纬210度");
  return 0;
}

// eg2 类模板的部分显式具体化
template<typename T2> 
class Point<char*, T2>{
public:
    Point(char *x, T2 y): m_x(x), m_y(y){ }
public:
    char *getX() const{ return m_x; }
    void setX(char *x){ m_x = x; }
    T2 getY() const{ return m_y; }
    void setY(T2 y){ m_y = y; }
    void display() const;
private:
    char *m_x;  //x坐标
    T2 m_y;  //y坐标
};

template<typename T2>  // 这里需要带上模板头!
void Point<char*, T2>::display() const{
    cout<<"x="<<m_x<<" | y="<<m_y<<endl;
}

// eg3 继承
template <typename T1, typename T2>
class A {};

template <typename T1, T2>
class B: public <T2, T1> {};  // 类模板从类模板继承

template <typename T1>
class C: public B <T1, T1> {}; // 类模板从类模板继承

class D: public C<float> {};	// 类模板从模板类派生

class E {};

template <typename T1>
class F: public E {};	// 类模板从普通类派生

class G: public D<double> {}; // 普通类从模板类派生

int main() {
  B<int, double> obj1; // B:int double A:double int
  C<int> obj2; // C:int B:int int A:int int
  D<char> obj3; // D:char C:float B:float float A:float float
  G obj4; // D:double C
  return 0;
}

// eg4 类模板中的静态成员
template <class T>
class A
{
private:
    static int count;
  	
public:
    A() { count ++; }
    ~A() { count -- ; };
    A(A &) { count ++ ; }
    static void PrintCount() { cout << count << endl; }
};

// template<> int A<int>::count = 1;
template<typename T> int A<T>::count = 1; // 对静态成员变量在类外部加以声明是必需的

int main()
{
    A<int> ia;
    A<double> da;
    ia.PrintCount(); // 1
    da.PrintCount(); // 1 在底层被编译器转化为2个不同的类，故他们是不可能共享静态变量的
    return 0;
}
```

# 运算符重载

C++

- 目的：同一个运算符可以有不同的功能。
- 实质：运算符重载是通过函数实现的，它本质上是函数重载。
- **重要规则**
  - 运算符重载函数除了函数名有特定的格式，其它地方和普通函数并没有区别。
  - 并不是所有的运算符都可以重载。例如以下运算符不能被重载：`.`、 `.*`、 `::`、`?:`、`sizeof`。http://c.biancheng.net/view/2308.html
  - 重载不会改变运算符的用法。
    - 优先级、结合性、几个操作数、操作数在左还是右...皆不变。
    - 运算符重载函数<u>不能有默认参</u>数，否则就改变了运算符操作数的个数。
    - 重载运算符时，应该尽量保留运算符原本的特性。
  - 运算符重载函数既可以作为类的成员函数，也可以作为全局函数。
    - 此时函数的参数个数就是运算符的操作数个数，运算符的操作数就成为函数的实参。
    - 运算符可以重载为全局函数，然后声明为类的友元。
    - 运算符也可以重载为成员函数。此时函数的参数个数就是运算符的操作数个数减一，运算符的操作数有一个成为函数作用的对象，其余的成为函数的实参。
    - C++ 只会对成员函数的参数进行类型转换，而不会对调用成员函数的对象进行类型转换，导致了类的成员函数重载运算符具有左右不对称性。
  - `->`、`[]`、`()`、`=`<u>只能以成员函数的形式重载</u>。
    - 必要时需要重载赋值运算符`=`，以避免两个对象内部的[指针](http://c.biancheng.net/c/80/)指向同一片存储空间。
- `<<`和`>>`是在 iostream 中被重载，才成为所谓的“流插入运算符”和“流提取运算符”的。
- 类型的名字可以作为强制类型转换运算符，也可以被重载为类的成员函数。它能使得对象被自动转换为某种类型。
- 自增、自减运算符各有两种重载方式，用于区别前置用法和后置用法。
```c++
class Complex {
  public:
  	Complex(): _real(0.0), _imag(0.0) {};
  	Complex(double real. double imag): 
 		Complex(double real): _real(real), _imag(0.0) {}; // 转换构造函数，细品！
  	Complex operator+(const Complex &a) const;
  	friend operator-(const Complex &a, const Complex &b);
  	void display() const;
  	Complex operator++(); // ++i
  	Complex &operator++(int); // i++
  private:
  	double _real;
  	double _imag;
};

Complex::Complex(double real, double imag): m_real(real), m_imag(imag){ }

void Complex:display() const {
  cout<<_real<<"+"_imag<<"i"<<endl;
}

Complex &Complex::operator++() {	// ++i
  _real++;
  _imag++;
  return *this;
}

Complex &Complex::operatpr++(int) { // i++，参数的存在只是用于区分
  Complex t = *this;
  ++*this; // 调用++i重载函数
  return t;
}

/*
- 默认编译后，函数的第一个参数为this指针，形势为 Complex * const this 
- 在普通的非const成员函数中，this的类型是一个指向类类型的const指针。可以改变this所指向的值，但不能改变 this所保存的地址。
- 在 const成员函数中，this的类型是一个指向 const类类型对象的 const指针。既不能改变 this所指向的对象，也不能改变 this所保存的地址。
*/
Complex Complex::operator+(const Complex &b) const { // 类范围内重载运算符
  Complex b;
  b._real = this._real + b._real;
  b._imag = this._imag + b._imag;
  return b;
}

Complex operator-(const Complex &a, const Complex &b) { // 全局范围内重载运算符
  Complex c;
  b._real = a._real - b._real;
  b._imag = a._imag - b._imag;
  return c;
}

int main() {
    Complex c1(4.3, 5.8);
    Complex c2(2.4, 3.7);
    Complex c3 = c1 + c2;	// c3 = c1.operator+(c2) 
    c3.display(); // 6.7+9.5i  
  	Complex c4 = c1 - c2; // c4 = operator-(c1,c2)
  	c4.display(); // 1.9+2.1i
  	Complex c5 = c1 + 15.6; // c1.oprator+(Complex(15.6))
  	Complex c6 = 15.6 + c1; // (15.6).oprator+(c1) 报错！
  	Complex c7 = 15.6 - c1; // operator-(Complex(15.6),c1)
  	return 0;
}
```

# 异常

C++

- 抛出(throw) -> 检测(try) -> 捕获(catch)

- catch可有多级，按类型匹配，匹配过程涉及类型转换。
  - 向上转型。
  - const转换。
  - 数组或函数指针转换。

```c++
/*
throw
- 派生类虚函数的异常规范必须与基类虚函数的异常规范一样严格，或者更严格。
- 异常规范在函数声明和函数定义中必须同时指明，并且要严格保持一致。
*/
// eg1
double func1(char param) throw (int, char, exception); // 函数只会抛出int, char, exception类型异常
double func2(char param) throw (); // 函数不会抛出任何异常

/*
try
- try只会捕获到抛出的异常(编译器抛出或人为抛出)
*/
```

# 标准库

- C
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
  - `<string>`：字符串。
    - 默认值是`""`。
    - 与C风格的字符串不同，string类型不用关心结束标志`'\0'`。
    - 减少风险：1.数组越界；2.通过未被初始化或者被赋以错误值的指针来访问数组元紊；3.释放了数组所占内存，但是仍然保留了“悬空”指针。
    - 被修改时才创建各自的拷贝。
  - `<iostream>`：输入\输出流。
  - `<complex>`：复数。