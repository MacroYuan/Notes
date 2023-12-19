> How to build a test framework for testing my tiny code.

C++测试框架

GoogleTest框架

```C++
#include <gtest/gtest.h>

int functionNeedBeTested(const int a)
{
    int b = a * a;
    return b;
}

TEST(SquareTest, PositiveNos) {
    ASSERT_EQ(0, functionNeedBeTested(0));
    ASSERT_EQ(1, functionNeedBeTested(1));
}

int main(int argc, char **argv)
{
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TEST();
}
```

1. 下载gtest包

```bash
sudo apt-get install libgtest-dev
```

2. 执行编译

```bash
cd /usr/src/gtest
sudo cmake CMakeLists.txt
sudo make
```

3. 部署

```bash
sudo make install
```



 使用TEST宏定义测试

```c++
TEST(test_suite_name, test_name) {
    // test body
}
```

test_suite_name是测试套名字，test_name是单个测试的名称.

TEST宏流程是怎样的？

源码中首先通过两个static_assert来判断输入的测试套名和测试名长度是否大于1，自动创建一个新的基于测试套名的类，类声明重写TestBody()方法

测试代码写好后，在main部分执行，流程为：

1. InitGoogleTest()初始化测试框架
2. RUN_ALL_TESTS()启动测试
3. 查找测试套件内的测试
4. 保存配置标志
5. 创建QueueTest实例
6. 调用QueueTest实例的SetUp()初始化数据配置
7. 执行测试
8. 调用QueueTest实例的TearDown()卸载数据配置
9. 恢复配置标志
10. 重复第3步，直到所有测试执行完毕

gmock

```c++
#include "gmock/gmock.h"

class MockMyClass : public MyClass {
public:
    
};
```

使用MOCK_METHOD()宏会自动生成定义

使用EXPECT_CALL()宏来设置对模拟方法的期望：

```c++
EXPECT_CALL(mock_object, method(matchers))
    .Times(cardinality)
    .WillOnce(action)
    .WillRepeatedly(action);
```

```c++
using ::testing::Return;

EXPECT_CALL(turtle, GetX())
    .Times(5)  // turtle对象的GetX()方法会被调用5次
    .WillOnce(Return(100)) // 第一次会返回100
    .WillOnce(Return(120)) // 第二次会返回120
    .WillRepeatedly(Return(200)); //剩下每次返回200
```

