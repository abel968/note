# 乱写


## memcpy memmove 区别 
有关内存重叠
## memcpy memccpy区别
## c++类型转换
- `static_cast<type-id> expression`
  1. 基类到派生类的转换
  2. 基本数据类型的转换
  3. 空指针到目标指针的转换
  4. 目标指针到空指针的转换  
**隐式转换也建议使用static_cast来转换**
- `dynamic_cast<type-id> expression`
  1. 只有在派生类之间的转换才用，type-id必须为指针、引用或void*
  2. 基类必须有虚函数，即只用于多态
  3. 可以判断向下转型是否安全，若不安全则返回NULL
- `const_cast<type-id> expression`
  1. 常量指针转为非常量指针，且仍指向原来的对象
  2. 常量引用转为非常量引用，且仍指向原来的引用
  3. 特定场景：非常量函数调用常量
- `reinterpret_cast<type-id> expression`

## 引用和指针的重要区别是：引用类型创建时必须被初始化,且一旦与某个变量关联，就一直效忠它。
## 组合 聚合
## 模板头文件与源文件分离
## struct初始化
## STL BoostC++
## UEFI模式硬盘GPT格式  BIOS模式硬盘MBR格式
## sizeof strlen函数
## c++允许将一个结构赋给另一个结构
## 堆和栈的区别
## thread_local 
## delete和delete[] 如何释放
## 深拷贝，浅拷贝
## 右引用
## new 定位
