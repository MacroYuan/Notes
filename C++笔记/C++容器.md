# C++容器

## vector

动态数组，C++中最常用的容器，相当于Java中的ArrayList

vector内部有begin、end、front、back成员函数

```cpp
#inluce <vector>

vector<int> v;


v.pop_back(); //删除最后一个元素
v.push_back(a); // 在尾部插入一个元素a
v.insert(i, a); // 在位置i插入一个元素a
v.erase(i); // 删除指定位置的一个元素
v.emplace(i, a); // 在指定位置构造一个元素
v.emplace(a); // 在尾部新构造一个元素
```

vector适合在尾部进行操作，其他位置插入删除复杂度为O(n)

## deque

双端队列，可以从头部和尾部自由地进行删除、插入操作。

## list

双向链表，任意位置插入和删除操作复杂度为O(1)，查找操作为O(n)

## forward_list

单向链表,C++11开始，阉割版的list，不需要反向查找的场景下，性能更优

## queue

采用deque实现，与deque相比：

- 不能按下标访问元素

- 没有begin、end成员函数

- 用emplace替代了emplace_back，用push替代了push_back，用pop替代了pop——front；没有其他的pop_\*、push_\*、emplace\*、insert、erase函数

## stack

同样采用deque实现，相比deque：

- 不能按下标访问元素

- 没有begin、end成员函数

- back成了top、没有front

- 用 emplace 替代了 emplace_back，用 push 替代了 push_back，用 pop 替代了 pop_back；没有其他的 push_…、pop_…、emplace…、insert、erase 函数


