---
title: Golang笔记
birth: 2020-06-05
category:
- devops 
---

keyword

expression

statement

variable

function

# Package

每一个Go程序都由n个包构成(n>=1)，程序从main包开始运行.

**`import`**

**exported name**

- 包内大写字母开头的变量或函数.

- 当import一个包时，只能参考exported name.



# Variable

**`var`**

**basic types**

-   bool
-   string
-   int, int8, int16, int32, int64
-   uint, uint8, uint16, uint32, uint64, uintptr
-   byte <=> unit8
-   rune <=> int32
-   float32, float64
-   complex64 , complex128

**zero value**

-   没有显式初始值的变量被赋予其zero value.
-   numeric types的zero value为0
-   boolean type的zero value为false
-   string type的zero value为""

**type conversion**

-   与C语言不同，Golang只支持显式转换

**type inference**

-   没有显示定义变量类型时，根据右侧的值推断变量类型.
-   若右侧为已显示定义类型的变量，左侧类型也确定.
-   若右侧为值，左侧类型取决于右侧值的精度.

**constant**

-   **`const`** 关键字声明常量，一旦声明便不可修改.
-   存放数值的常量具有高精度. 若是未指定具体类型，则会根据上下文进行类型选择.

```go
// eg1.go
package main

import "fmt"

var num1 int
var bool1 bool

const pi = 3.14

func main() {
  var num2 int
  var bool2 bool
  var i, j int = 1, 2
  var var1, var2 = 10, "no"
  var3 := 3 // 只能在function中使用
  var (
		var4 bool = false
    var5 int = 4
    var6 string = "yes"
  )
  const pi = 3.14 // 只能这样声明
  fmt.Println(num1, num2, bool1, bool2) // 0 0 false false
  fmt.Println(i, j, var1, var2) // 1 2 10 "no" 
}
```



# Function

```go
// eg1.go
package main

import "fmt"

// func1
func add(x int, y int) int {
		return x + 
}

// func2: 与func1等同
func add(x, y int) int {
		return x + y
}

// func3
func swap(x, y string) (string, string) {
  	return y, x
}
	
// func4
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
}
```



# Flow control

-   for
    -   continue
    -   break
-   if
    -   else
-   switch
-   defer

```go
// eg1.go
package main

import (
	"fmt"
  "runtime"
)

func sum(n int) int {
  sum := 0
  for i:=0; i<n; i++ { // i只在for语句作用域中可见
    sum += 1
  }
  return sum
}

func sum1(n int) int {
  sum := 0
  for sum<n { // for is Go's while
    sum += 1
  }
  return sum
}

func forever() {
  for {
    // loop forever
  }
}

func ifelse1(x float64) float64 {
  if y:=x*0.1; y<0 {
    return y
  } else if x>0 {
    return y
  } else {
    return y
  }
  // if语句作用域外是访问不到y的
}

func switch1() {
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
}
```



# Method

# Interface

# Concurrency

# Go Tool

`go get`

`go install`

`go build`

`go run`



# Std pkg

# Third pkg

- godotenv：从`.env`加载环境变量
- viper：支持多种文件格式加载环境变量



# References

- golang官网：https://golang.org/
- golang官方社区：https://go.dev/

- vscode安装go插件失败问题
  - https://zhuanlan.zhihu.com/p/56567884
  - https://blog.csdn.net/qq_41065919/article/details/107710144
- go 1.16前后关于GO111MODULE的变化：https://polarisxu.studygolang.com/posts/go/dynamic/go-get-install/
