> https://juejin.cn/post/6844903861895823368

Scala是基于JVM重新设计的更加现代化、可扩展的语言。

Scala是面向对象语言，那对于我Java程序员应该好入门。

Scala适用于大数据分析

```scala
val s1="hello" ; println(s1) // 必须有分号
println("world") // 不必有分号

// while 循环版本
var i = 0
var found = false
while (i<args.length && !found)
{
    if (!args(i).startsWith("-")){
        if (args(i).endsWith(".scala")) find = true
    }
    i += 1
}
// 递归函数版本
def search(i:Int):Int = {
    if (i>=args.length) -1
    else if (args(i).startsWith("-")) search(i+1)
    else if (args(i).endsWith(".scala")) i
    else search(i+1)
}
var  i = search(0)
```

语法还需学习下，