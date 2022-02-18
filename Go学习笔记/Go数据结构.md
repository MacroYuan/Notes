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

## 数据结构

### 数组

```go
arr1 := [3]int{1, 2, 3}
arr2 := [...]int{1, 2, 3}
```

### 切片

```go
[]int
[]interface{}
```

切片长度是动态的，可用叫作动态数组

一个切片的结构体：

```go
type SliceHeader struct {
    Data unitptr
    Len int
    Cap int
}
```

![golang-slice-struct](https://img.draveness.me/2019-02-20-golang-slice-struct.png)

切片就是一片连续的内存空间加上长度与容量的标识。

追加：

```go
//apend(slice, 1, 2, 3)
ptr, len, cap := slice
newlen := len + 3
if newlen > cap {
    ptr, len, cap = growslice(slice, newlen)
	newlen = len + 3
}
*(ptr+len) = 1
*(ptr+len+1) = 2
*(ptr+len+2) = 3
return makeslice(ptr, newlen, cap)
```

下面这种方式会存在覆盖原切片

```go
//slice = append(slice, 1, 2, 3)
a := &slice
ptr, len, cap := slice
newlen := len + 3
if unit(newlen) > unit(cap) {
    newptr, len, newcap = growslice(slice, newlen)
    vardef(a)
    *a.cap = newcap
    *a.ptr = newptr
}
newlen = len + 3
*a.len = newlen
*(ptr+len) = 1
*(ptr+len+1) = 2
*(ptr+len+2) = 3
```

如果选择覆盖原有变量，就不需要担心切片发生拷贝影响性能

### 哈希表

哈希表最主要的特性是O(1)时间复杂度的读写性能。关于数据结构最常问的问题就是哈希，需要注意哈希函数与冲突解决方法，冲突解决方法有**开放寻址法**与**拉链法**。

#### 读写与扩容与删除

扩容

发生扩容的触发情况

1. 装载因子已经超过6.5
2. 哈希使用了太多溢出桶
   //TODO

### 字符串

字符串可以理解成一个字符数组

```go
type StringHeader struct {
    
}
```



