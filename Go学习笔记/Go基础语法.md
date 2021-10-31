## 变量赋值

var a int = 1

简明赋值

a := 1 //自动赋值变量a为int性，值为1

函数外的每个语句必须以关键字（var、func等）开头，:=不能用于函数外。

## 数据类型

bool

string

int	int8	int16	int32	int64

uint	uint8	uint16	uint32	uint64	uintptr

byte	//uint8的别名

rune	//int32的别名，表示一个Unicode码点

float32	float64

complex64	complex128

数值转换

int -> float -> uint

常量：使用`const`关键字，可以是字符、字符串、布尔值或数值

### defer

defer语句会将函数推迟到外层函数返回之后执行，推迟调用

### make

make与切片相关，用于创建切片，也就是创建动态数组

eg. a := make([]int, 10) //创建一个一个零值的数组，并引用长度为10的切片

b := make([]int, 0, 5) //创建一个长度为0，但容量为5的切片

### map

map将键映射到值。

eg. m := make(map[string]int)

m["hello"] = 1

```go
package main

import "fmt"

func main() {
   rank := map[string]int{}
   rank["Macro"] = 1
   rank["Greken"] = 2
   fmt.Println(rank)
   delete(rank, "Greken")
   fmt.Println(rank)
   rank["Bob"] = 3
   rank["Mary"] = 4

   for key, value := range rank {
      fmt.Println(key," ", value)
   }
   /*
   如何有序遍历map，建立一个包含所有键的切片
   切片排序，再以切片的顺序遍历map
    */
}
```

### 函数值

函数也是值，可以像值一样传递，可以用作函数的参数或返回值

