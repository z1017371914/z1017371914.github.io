

[the way to go](https://learnku.com/docs/the-way-to-go)的读书笔记。

## 基本结构和要素

### 程序的基本结构和要素

#### 包

##### 包的基本概念

>1.每个go文件的非注释第一行声明该文件所属的包
>
>```go
>package main
>```
>
>2.包是编译的最小单元。某个包的内容变更时，其他引用了该包的文件也需要重新进行编译。
>
>3.go文件引用包使用import关键字
>
>```go
>import (
>"fmt"
>"os")
>```

##### 包的可见性规则

>1.包中某个go文件中的标识符：such as 变量，常量，类型，函数名，结构字段等首字母大写时，表明该包若被其他文件引用(即import)，那么这些元素是可见的，相当于public。同理，如果是小写开头，那就是private。
>
>2.引用了某个包的可见性标识符，需要通过***包名.元素***来引用。
>
>```go
>package test
>
>import(
>"fmt"
>"main")
>
>func main() {
>fmt.Println("这里调用了Println,需要使用包名.元素来引用")
>main.func1(var1)//这里调用了Println,需要使用包名.元素来引用
>}
>```
>
>

#### 函数

##### 函数的基本定义形式

```go
//第一个分号必须和函数名在同一行。
func funcName (parameter1 type1, parameter2 type2...) (returnType1, returnType2, ......) {
    return returnType1, returnTypr2, ....
}
```

#### 注释

###### 注释的基本约束

>1.每个包都应该有注释。共同的包只要在一个地方进行注释。并且第一行简要描述，空一行再具体描述
>
>```go
>// Package superman implements methods for saving the world.
>//
>// Experience has shown that a small number of procedures can prove
>// helpful when attempting to save the world.
>package superman
>```
>
>2.函数需要注释的时候，第一个词必须是函数名
>
>```go
>// enterOrbit causes Superman to fly into low Earth orbit, a position
>// that presents several possibilities for planet salvation.
>func enterOrbit() error {
>...
>}
>```

#### 类型

##### 基本数据类型

| 数据类型 | 类型                 |
| -------- | -------------------- |
| int      | 基本数据类型         |
| float    | 基本数据类型         |
| bool     | 基本数据类型         |
| string   | 基本数据类型         |
| struct   | 结构(复合)的数据类型 |
| array    | 结构(复合)的数据类型 |
| slice    | 结构(复合)的数据类型 |
| map      | 结构(复合)的数据类型 |
| channel  | 结构(复合)的数据类型 |

结构的数据类型的初始值为nil，相当于Java中的null。

#### 程序的一般结构

>1.第一行声明包名
>
>2.接下来是import包
>
>3.对常量，变量进行声明
>
>4.如果是main包，需要先定义main函数
>
>5.定义其他函数

### 常量

>隐式定义常量：
>
>```go
>const PI = 3.14159
>```
>
>显式定义常量：
>
>```go
>const PI int = 3.14159
>```
>
>数字型的常量是没有大小和符号的，并且可以使用任何精度而不会导致溢出：
>
>```go
>const Ln2= 0.693147180559945309417232121458\
>       176568075500134360255254120680009
>const Log2E= 1/Ln2 // this is a precise reciprocal
>const Billion = 1e9 // float constant
>const hardEight = (1 << 100) >> 97
>```
>
>反斜杠在常量定义时可充当多行的连接符号的作用。

### 变量

>定义变量的方式
>
>```go
>//var 变量名 变量类型
>var var1 int = 0
>
>//因式分解的方法进行定义，用于声明全局变量
>var (
>a int 
>b bool
>c string
>d float32
>e float64
>)
>
>//短声明，用于局部变量的声明
>var2 := 0
>```

### 基本类型和运算符

#### 布尔类型

>1,和Java一样，只不过关键字是bool
>
>2.类型相同的值才能进行比较，如果值的类型是interface，那么如果他们要比较的话，需要二者都实现相同的接口。

#### 基本运算符号

>和Java一样，与，或，非；按位与，按位或，按位异或。
>
>位左移：<<.将位左移n位，左边空白部分补零。效果相当于乘以2的n次方
>
>位右移：>>.将位右移n位，右边空白部分补零，效果相当于除以2的n次方。

#### 数字类型

##### 整数类型int

| 类型   | 范围               |
| ------ | ------------------ |
| int8   | -(2^7 -1)  ~ 2^7   |
| int16  | -(2^15 -1 ) ~ 2^15 |
| int32  | -(2^31 - 1) ~ 2^31 |
| int64  | -(2^63 - 1) ~ 2^63 |
| uint8  | 0 ~ 2^8-1          |
| uint16 | 0 ~ 2^16 - 1       |
| uint32 | 0~ 2^32 -1         |
| uint64 | 0 ~ 2^64 - 1       |

##### 浮点数类型

只有*float32*和*float64*。不指定类型的定义一个小数时，值默认为float64.

| 类型    | 范围                         | 精确范围     |
| ------- | ---------------------------- | ------------ |
| float32 | +- 1e-45 -> +- 3.4 * 1e38    | 小数点后7位  |
| float64 | +- 5  *1e-324 -> 107*  1e308 | 小数点后15位 |

##### 字符类型

严格来说，这并不算是go的数据类型，字符类型是整数类型的特殊类型。

字符类型是byte，其取值范围是0~2^8-1,很明显其实是uint8.

 ##### 特殊表示

| 表示的形式       | 表示方法              | 例子                     |
| ---------------- | --------------------- | ------------------------ |
| 表示成八进制     | **在前面补0**         | 比如077，表示7x8 + 7x8^2 |
| 表示成十六进制   | **在前面补0x**        | 比如0xFF                 |
| 表示成科学计数法 | e接数字表示十的几次方 | 1000就是1e3              |

#### 格式化输出

>即在print中怎么输出数值的类型,和C语言类似
>
>直接按变量的类型输出：%v
>
>整数：%d
>
>浮点数：%f
>
>科学计数法：%e
>
>按比特位：%b
>
>%.mg：精确到小数点后m位

#### 复数

>go有两种复数类型：complex64，complex128。

##### 复数的表示

复数是这么表示的

```go
var a complex64 = 1 + 1i
```

##### 获取实部和虚部

>实部：real()函数
>
>虚部：imag()函数

### 字符串

 >字符串是定长数组，创建之后不能再修改。即Java的String类型同理。

获取字符串的长度：使用len()函数

获取某个索引的字符：str[i]

### strings封装的方法

| 方法                                            | 作用                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| strings.HasPrefix(s string, prefix string) bool | 是否以prefix开头                                             |
| strings.HasSuffix(s string, suffix string) bool | 是否以suffix结尾                                             |
| strings.Contains(s string, substr string) bool  | s中是否有字串substr                                          |
| strings.Index(s string, str string) int         | 返回str在s中的索引，返回的是str的第一个字符，-1表示s中没有该str |
| strings.LastIndex(s string, str string)         | 返回最后一个str在s中的索引，-1表示没有                       |
| strings.Replace(str, old, new , n) string       | 将str中前n个字符串old替换成new，然后返回。n=-1表示将所有的old替换成new |
| strings.Count(s string, str string) int         | 统计str在s中出现的次数                                       |
| strings.Repeat(s string, count int) string      | 将s重复count次，返回重复后的字符串。比如strings.Repeat("hi ",2)的值为"hi hi ".其实就是拼接count次 |
| strings.ToLower(s string) string                | 将s全改为小写                                                |
| strings.ToUpper(s string)string                 | 将s全改为大写                                                |
| strings.TrimSpace(s)                            | 剔除掉开头和结尾的空格，剔除左边：TrimLeft()，右边：TrimRight() |

### 指针

```go
var ptr *int
var i = 5
ptr = &i
```

>1.指针未被赋值时，值为nil
>
>2.指针在print中用的是%p
>
> 3.获取指针指向的值：*ptr

```go
package main

import "fmt"

func main() {
	var i int = 1
	var ptr *int = &i
	fmt.Printf("初始值：%d\n", i)
	fmt.Printf("i的地址：%v\n", &i)
	fmt.Printf("i的地址：%p\n", ptr)
	fmt.Printf("获取指针指向的内容：%d\n", *ptr)//指针的反引用
}


```

输出结果：

```
初始值：1
i的地址：0xc00000a0e0
i的地址：0xc00000a0e0
获取指针指向的内容：1
```

>指针的一个高级应用是你可以传递一个变量的引用（如函数的参数），这样不会传递变量的拷贝。指针传递是很廉价的，只占用 4 个或 8 个字节。当程序在工作中需要占用大量的内存，或很多变量，或者两者都有，使用指针会减少内存占用和提高效率。被指向的变量也保存在内存中，直到没有任何指针指向它们，所以从它们被创建开始就具有相互独立的生命周期。 

## 控制结构

### if-else结构

``` go
if condition1 {
    //code
} else if condition2 {
    //code
} else {
    //code
}
```

>1.和Java不同的地方是，判断条件不需要加括号。
>
>2.大括号必须在同一行：关键字 if 和 else 之后的左大括号 `{` 必须和关键字在同一行，如果你使用了 else-if 结构，则前段代码块的右大括号 `}` 必须和 else-if 关键字在同一行。

```go
if x{
}
else {  // 无效的
}
```

### 测试多返回值函数的错误

```go
an, err := strconv.Atoi(orig)
    if err != nil {
        fmt.Printf("orig %s is not an integer - exiting with error\n", orig)
        return
    } 
```

>1.Atoi函数返回两个值。err用于接受错误与否。
>
>2.如果想要忽略错误，可以使用以下格式，也就是用下划线替代

```go
an, _ := strconv.Atoi(orig)
```

### switch结构

>1.switch判断的类型不像C那样只有整型。所有的类型都可以判断
>
>2.不用写break，默认是满足条件就帮你break
>
>3.如果需要顺延执行的话，需要加关键字fullthrough

```go
switch i {
    case 0: fallthrough
    case 1:
        f() // 当 i == 0 时函数也会被调用
}
```

### for循环结构

#### 基于计数器的迭代

>1.条件不用写括号

```go
//基本形式
for 初始化语句;判断条件;修饰语句 {
	//code
}								
```

#### 基于条件判断的迭代

>1.相当于while循环

```go
for condition {
    //code
}
```

#### 无限循环

>1.没有条件的执行，跳出来可以用break或者return
>
>2.无限循环的经典应用是服务器，用于不断等待和接受新的请求。

```go
for {
    //code
}

//无限循环的经典应用是服务器，用于不断等待和接受新的请求
for t, err = p.Token(); err == nil; t, err = p.Token() {
    ...
}
```

### for-range结构

>1.类似于for-each结构。但除了能获取到值，还能获取到索引。

 ```go
 str := "Java is the best language ~"
 for pos, char := range str {
     fmt.Printf("Character on position %d is: %c \n", pos, char)
 }
 //执行结果：
 Character on position 0 is: J 
 Character on position 1 is: a 
 Character on position 2 is: v 
 Character on position 3 is: a 
 Character on position 4 is:   
 Character on position 5 is: i 
 Character on position 6 is: s 
 Character on position 7 is:   
 Character on position 8 is: t 
 Character on position 9 is: h 
 Character on position 10 is: e 
 Character on position 11 is:   
 Character on position 12 is: b 
 Character on position 13 is: e 
 Character on position 14 is: s 
 Character on position 15 is: t 
 Character on position 16 is:   
 Character on position 17 is: l 
 Character on position 18 is: a 
 Character on position 19 is: n 
 Character on position 20 is: g 
 Character on position 21 is: u 
 Character on position 22 is: a 
 Character on position 23 is: g 
 Character on position 24 is: e 
 Character on position 25 is:   
 Character on position 26 is: ~ 
 
 Process finished with exit code 0
 
 ```

### Break与Continue

>1.和其他语言一样的用法
>
>2.continue可以跳到标签Lable处。

### goto与标签

>1.标签，相当于标记一段代码的开始位置，方便跳转
>
>```go
>package main
>
>import "fmt"
>
>func main() {
>
>LABEL1://标准形式 lable名（全大写）接冒号
>    for i := 0; i <= 5; i++ {
>        for j := 0; j <= 5; j++ {
>            if j == 4 {
>                continue LABEL1
>            }
>            fmt.Printf("i is: %d, and j is: %d\n", i, j)
>        }
>    }
>
>}
>```
>
>2.Lable区分大小写，规范写法是全部大写。
>
>3.goto语句不推荐使用，C语言课上讲过。

## 函数

>1.go的函数不支持重载。因为函数重载对性能影响比较大
>
>2.没有范型，不像Java那样一个函数能支持多个类型的值
>
>3.go的函数可多值返回。
>
>4.函数的形参可以没有名字而只有类型
>
>```go
>func f(int, int, float64)
>```
>
>这种函数称为**niladic函数**
>
>5.go的函数默认是传递参数的副本，传参数进去，如果参数的值在函数中改变了，并不影响原来的参数。如果需要按引用传递，就在参数前加上&。
>
>6.传递指针给函数的开销比传递副本的开销要小得多，因为指针无非是32或64位系统中一个地址的长度，而参数副本类型可能是基本类型等。

### 传递变长参数

>1.函数的形参以 ...type结尾，就是接受变长参数。
>
>2.变长参数就是，能再继续接受0个到多个这个类型的参数
>
>```go
>func myFunc(a, b, arg ...int) {}
>//调用
>myFunc(1,2,33,44,55,66,77)
>myFunc(1,2,33,44,55,66,77,88)
>```
>
>

### defer和追踪

