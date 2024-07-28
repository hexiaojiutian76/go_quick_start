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

