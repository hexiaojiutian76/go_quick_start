# 变量作用域
与其他语言的概念类似。

## 变量作用域
Go作用域通常会随着大括号{}的出现而开启和结束。也有特殊情况，比如包级别的作用域
```go
package main

import (
	"fmt"
	"math/rand"
)

var era = "AD" //这个变量的作用域就是在整个包（package）是可用的

func main() {
	var times = 0
	for times < 10 {
		//变量num的作用域就在这个循环中
		var num = rand.Intn(10) + 1
		fmt.Println(num)
		times++
	}  //num的作用域结束
}  //times的作用域结束
```
## 短声明
短声明写法在GO语言中更加流行，并且功能也更加强大
1. 写法：
```go
count := 10
```
等同于
```go
var count = 10
```
2. for中使用短声明  

短声明不光是少打几个字母那么简单，它还可以用在一些无法使用var关键字的地方,比如说，在for循环中，我们无法使用var来定义count，只能在for循环之前定义好先
```go
var count = 0
for count = 10; count > 0; count-- {
    fmt.Println(count)
}
```
可以简写成
```go
for count := 10; count > 0; count-- {
    fmt.Println(count)
}
```
3. if中使用短声明
```go
if num := rand.Intn(3); num == 0 {
    fmt.Println("Space Adventure")
} else if num == 1 {
    fmt.Println("SpaceX")
} else if num == 2 {
    fmt.Println("Virgin Galatic")
}
```
4. Switch中 使用短声明
```go
switch num := rand.Intn(3); num {
case 0:
    fmt.Println("Space Adventure")
case 1:
    fmt.Println("SpaceX")
case 2:
    fmt.Println("Virgin Galatic")
}
```
总得来说，学会多使用短声明

# 基本类型之数值类型

## 实数

### 浮点类型
go语言中有两种浮点类型：
1. float64（占8字节内存）,默认的浮点类型。意思是说不显式地指定float32，都是使用float64来定义一个带小数点的变量
2. float32（占4字节内存）
```go
days := 365.2425    //短声明，days会被Go编译器推断为浮点类型默认的float64类型
```
如果需要使用float32，我们必须指定类型
```go
var pi64 = math.Pi      
var pi32 float32 = math.Pi  //这样声明的变量才会是float32
```
### 零值
零值就是某个类型的默认值。在Go中，当创建一个变量但没有为其赋值时，Go会自动为其赋值对应类型的零值。浮点类型的零值很容易想到
```go
var price float
```
等同于
```go
price := 0.0
```
### 格式化输出浮点值
格式化输出需要使用到`fmt.Printf()`函数：
```go
fmt.Printf("%f\n", third)     //0.333333
fmt.Printf("%.2f\n", third)   //0.33，.2f就是表示小数点后保留2位
fmt.Printf("%4.2f\n", third)  //0.33，4.2f表示总宽（长）度为4，小数点后保留2位
fmt.Printf("%5.2f\n", third)  //0.33，5.2f表示总宽（长）度为5，小数点后保留2位，长度不够使用空格来补
fmt.Printf("%05.2f\n", third) //00.33，05.2f表示总宽（长）度为5，小数点后保留2位，长度不够使用“0”来补
```
### 浮点类型的精确性
由于计算机只能通过0和1来表示浮点数，所以浮点数会经常受到舍入错误的影响
比如：
```go
piggyBank := 0.1
piggyBank += 0.2
fmt.Println(piggyBank) //0.30000000000000004
```
由上面提到的浮点类型的精确度问题，就会导致浮点数的比较出现意外。
```go
fmt.Println(piggyBank == 0.3) //false
```
一个折中的解决方案就是使用一定精确度来判断是否相等
```go
fmt.Println(math.Abs(piggyBank-0.3) < 0.0001) //true
```

那么说到底，避免浮点数精确度问题的最佳方案就是：不使用浮点数🐶

## 整数

Go中提供了10种整数类型，根据不同的大小和是否有符号分为
* int8      1字节（8位）
* uint8     1字节
* int16     1字节
* uint16    1字节
* int32     1字节
* uint32    1字节
* int64     1字节
* uint64    1字节
* 还有两个是int和uint，Go在进行类型推断是会默认推断成int类型。在Go中，int类型会根据目标硬件（电脑是32位还是64位）选择合适的位长（32位机器上int就是32位值，64位机器就是64位长），所以如果想在32位的机器上操作特别大的数，要定义成int64而不是int。但是也不要认为int类型和int32或int64是一种类型，他们是3种类型。

### 选择合适的类型
适应不同的常见选择不同的类型来
【例】：使用uint8来表示颜色rgb值，是个很好的选择：
* （1）能将变量限制在合法的范围之内
* （2）对于未压缩的图片这种需要按顺序存储大量颜色的场景，可以极大的节省空间
```go
var red, green, blue uint8 = 0, 141, 213
fmt.Printf("color：#%02x%02x%02x;", red, green, blue) //color：#008dd5;
```
### 回绕（wrap around）
整数类型不会有浮点类型的精度问题，但是存在自己的回绕问题。就是整数类型在达到自己的边界值（最大值或最小值），再向边界外延伸时，会回到最小值或最大值。例如一个uint8，它的最大值应该是255，此时再进行加1操作后，值就变成了0。其实只要知道整型在计算机中的二进制存储方式，这个事情还是很好理解的。
```go
var numberA uint8 = 255	//到达类型最大值
numberA++
fmt.Println(numberA)	//0	环绕
```
## 大数
顾名思义，就是特别大的数，一般情况下，比较大的数我们也可使用简便写法
表示方法（类似科学计数法）：
```go
var distance int64 = 41.3e12    //就是41.3 * 10<sup>12</sup>
```
但是如果需要使用超过uint64上限的数时，Go为我们提供了big包来解决问题，引用时包名为："math/big"。

### big包
* 存储大整数：big.Int
* 存储任意精度的浮点数：big.Float
* 存储如1/3的分数：big.Rat 
* ** 大整数(big.Int)、大有理数(big.Rat)和大浮点数(big.Float)**

[数学](https://zh.wikipedia.org/wiki/数学)上，可以表达为两个整数比的数（![{\displaystyle {\frac {a}{b}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/9fbb66e57f89debc3cde3213de12228971148a93), a ≠0,![{\displaystyle b\neq 0}](https://wikimedia.org/api/rest_v1/media/math/render/svg/ad073253b4c817f2ec7e3dd7517b7f89a8e581dc)）被定义为**有理数**，例如38![{\displaystyle {\frac {3}{8}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/f6fe52a498e9788c548326304098d2122ca4645d)，0.75(可被表达为34![{\displaystyle {\frac {3}{4}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/7572f1241ec7c2f9311985bc3dfb0b7d6f491e44))；[整数](https://zh.wikipedia.org/wiki/整数)和[整数分数](https://zh.wikipedia.org/w/index.php?title=整数分数&action=edit&redlink=1)统称为有理数。

与有理数相对的是[无理数](https://zh.wikipedia.org/wiki/无理数)，如2![{\displaystyle {\sqrt {2}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/b4afc1e27d418021bf10898eb44a7f5f315735ff)无法用整数比表示。

有理数与[分数](https://zh.wikipedia.org/wiki/分數)形式的区别，[分数](https://zh.wikipedia.org/wiki/分數)形式是一种表示比值的记法，如 [分数形式](https://zh.wikipedia.org/wiki/分數)22![{\displaystyle {\frac {\sqrt {2}}{2}}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/2fb9b5960bf5eae3065db9c23495e465f5fef61e)是[无理数](https://zh.wikipedia.org/wiki/无理数)。
所有有理数的[集合](https://zh.wikipedia.org/wiki/集合_(数学))表示为**Q**，Q+,或。定义如下：

![{\displaystyle \mathbb {Q} =\left\{{\frac {m}{n}}:m\in \mathbb {Z} ,n\in \mathbb {Z} ,n\neq 0\right\}}](https://wikimedia.org/api/rest_v1/media/math/render/svg/5d32c576a132a89e30c1083da67c4423d2a37227)

有理数的[小数](https://zh.wikipedia.org/wiki/小数)部分有限或为[循环](https://zh.wikipedia.org/wiki/循环小数)。不是有理数的[实数](https://zh.wikipedia.org/wiki/實數)遂称为[无理数](https://zh.wikipedia.org/wiki/無理數)。

big.Int的一些常用方法:
Add(): 加法
Sub(): 减法
Mul(): 乘法
Div(): 除法
Mod(): 取模
Cmp(): 比较
Abs(): 绝对值
Exp(): 指数运算
And(), Or(), Xor(): 位运算

Add()
英文全称: Addition
中文翻译: 加法
解释: 执行两个大整数的加法运算


Sub()
英文全称: Subtraction
中文翻译: 减法
解释: 执行两个大整数的减法运算

Mul()
英文全称: Multiplication
中文翻译: 乘法
解释: 执行两个大整数的乘法运算

Div()
英文全称: Division
中文翻译: 除法
解释: 执行两个大整数的除法运算


Mod()
英文全称: Modulus
中文翻译: 取模(求余)
解释: 计算两个大整数相除的余数

Cmp()
英文全称: Compare
中文翻译: 比较
解释: 比较两个大整数的大小

Abs()
英文全称: Absolute Value
中文翻译: 绝对值
解释: 计算一个大整数的绝对值


Exp()
英文全称: Exponentiation
中文翻译: 指数运算
解释: 计算一个大整数的幂


位运算:
a) And()
英文全称: Bitwise AND
中文翻译: 按位与
解释: 对两个大整数执行按位与运算

b) Or()
英文全称: Bitwise OR
中文翻译: 按位或
解释: 对两个大整数执行按位或运算

c) Xor()
英文全称: Bitwise XOR (Exclusive OR)
中文翻译: 按位异或
解释: 对两个大整数执行按位异或运算


#### big.Int的创建方式有两种
1. 使用big.NewInt(int val)的方法
```go
lightSpeed := big.NewInt(299792)
```
2. 使用big.SetString(string val, 10)的方法，其中“10”表示参数1这个字符串是个10进制的数
```go
distance := new(big.Int)
distance.SetString("24000000000000000000000", 10)
```
大数类型可以精确地承载很大的数值，但是代价就是空间和性能的损耗。

### 大数在常量中的表现
和变量不同，当我们不为常量指定类型，并直接为其赋值一个很大的数，Go会直接将其标记为无类型（untyped）而不会引发溢出异常，并且可以在程序中正常使用
```go
const distance = 240000000000000000000000
fmt.Println("Andromeda Galaxy is ", distance/299792/86400)  //output: Andromeda Galaxy is  9265683466462
```

# 基本类型之字符串类型

## string类型

关于字符串类型（string），和其他语言一样没什么区别，使用双引号包起来，如
```go
peace := "peace"
var peace = "peace"
var peace string = "peace"
```
使用双引号包起来的字符串称为“字符串字面量”。“字符串字面量”中可以包含转义字符，比如说 `\n` 可以表示换行。另外一种表示字符串字面量的方法是使用反引号`，这种称为“原始字符串字面量”。使用``，可以方便的定义跨行字符串，如
```go
fmt.Println(`
peace be upon you
upon you be peace`)
```
“字符串字面量”和“原始字符串字面量”都是string类型。

## 字符、代码点、符文和字节
* 都知道计算机中字符是通过编码存取的，也就是每个字符都使用一个特定的数字表示。比如A就是65，那么书中将这个65称为字符A的**代码点**。
* rune类型（Rune type 符文类型）：Go中使用rune类型来表示字符的代码点，该类型本质上是int32类型的别名，也就是说rune和int32可以相互转换
* byte类型：Go中的byte类型不仅可以表示二进制数据，而且被拿来表示ASCII码（ASCII共包含128个字符）。本质上byte类型是uint8类型的别名
```go
var pi rune = 960
var alpha rune = 940
var omega rune = 969
var bang byte = 33

fmt.Printf("%v %v %v %v\n", pi, alpha, omega, bang) //960 940 969 33
//通过使用格式化变量%c，可以将代码点表示成字符
fmt.Printf("%c %c %c %c\n", pi, alpha, omega, bang) //π ά ω !
```
在Go中使用单引号来表示字符字面量，如果用户声明一个字符变量而没有为其制定类型，那么Go会将其推断成rune类型。下面三种写法是一样的功能。
```go
grade := 'A'
var grade = 'A'
var grade rune = 'A'
```

## 字符串无法修改
字符串虽然是有字符“串”起来的，但是和C#、java等语言一样，Go中的字符串类型也是不可修改的。
```go
//通过索引的方式访问字符串中的字符
message := "shalom"
c := message[5]
fmt.Printf("%c\n", c)

//字符串不可被修改
//message[5] = 'd'  //报错：cannot assign to message[5]
```

## 字符串与符文
Golang中字符串使用UTF-8编码。UTF-8是一种变长的编码方式，也就是说每个字符可能占用不同的字节长度。比如常见的中文字符通常需要占据3个字节长度，而英文字符或者数字则只需要占据1个字节长度。有些特殊字符可能占4个字节。
为了方便，Go为此提供了utf包，里面提供了两个实用的方法
* RuneCountInString函数，能够返回字符串中Unicode字符（符文）的个数，而不是像len方法一样返回字节的长度。
* DecodeRuneInString函数，解码字符串的首个Unicode字符并返回解码后的字符及其占用的字节长度。
```go
question := "今天星期几？"
fmt.Println(len(question), "bytes")                    //18 bytes
fmt.Println(utf8.RuneCountInString(question), "runes") //6 runes

c, size := utf8.DecodeRuneInString(question)
fmt.Printf("First rune: %c %v bytes", c, size) //First rune: 今 3 bytes

//遍历字符串，挨个打印出来
for i, c := range question {
    fmt.Printf("%v %c\n", i, c)
}
```
上面的示例中使用到了`range`关键字来进行遍历操作，其中`i`为索引，`c`为值。有点python的味道。



# 基本类型之类型转换

Go中与其他强类型语言（比如C#）类似，类型之间进行操作时，需要经过类型转换否则会报“类型不匹配”的错误。

## 数字类型转换
* 整数类型 → 浮点类型：

```go
age := 41
marsAge := float64(age)
```
---
* 浮点类型 → 整数类型：  

【注意】：浮点型的小数部分是被截断，而不是四舍五入
```go
earthDays := 365.2425
fmt.Println(int(earthDays)) //output: 365
```
在数值类型进行转换时，一样要注意超出范围的问题，比如一个较大float64转成int16时。

## 字符串转换
* rune/byte → string

```go
var pi rune = 960
var alpha rune = 940
fmt.Println(string(pi), string(alpha)) //output: π ά
```
---
* 数字类型 → string

情况特殊一点，为了将一串数组转换为string类型，必须将其中的每个数字都转换为相应的代码点（char）。也就是代表字符0的48~代表字符9的57。我们需要使用到strconv（代表“string conversion”）包提供的Itoa函数来完成这一工作。
```go
countdown := 10
str := "Launch in T minus " + strconv.Itoa(countdown) + " seconds".
```
另一种方法，使用fmt.Sprintf函数，该函数会返回格式化后的string而不是打印
```go
countdown := 9
str := fmt.Sprintf("Launch in T minus %v seconds", countdown)
fmt.Println(str) //Launch in T minus 9 seconds
```
> 注：使用 `strconv.Itoa()` 比 `fmt.Sprintf()` 要快一倍左右
---
* string → 数字
一种不太常用的转换，也是使用strconv包的Atoi函数
```go
count, err := strconv.Atoi("10")
if err != nil {
    //出错
}
fmt.Println(count) //10
```
上面这种写法是之后经常看到的，是Go处理异常的一种常用写法。由于Go的函数可以返回多个值，一般会将可能产生的异常一并返回。

## 布尔类型转换
如果使用`fmt`的`Print`系函数直接打印bool类型，会输出true或false的文本
```go
launch := false
launchText := fmt.Sprintf("%v", launch)
fmt.Println("Ready for launch:", launchText) //Ready for launch: false
```
某些语言中会把1和0当做true和false，Go中是不行的。