# STL源码剖析 读书笔记

## STL概论

### OOP 与 GP
- OOP: Object oriented programming 
面向对象编程，即将数据、函数等一系列东西都封装在一个类中
- GP: generic programming
类属编程 可以提供更加灵活的组装效率而不损失效率

### STL六大组件
1. 容器(containers) 包含vector, list, deque, set, map
2. 算法(algorithms) 包含各种常用算法sort, search, copy, erase
3. 迭代器(iterators) 也被称为泛型指针 用于链接容器和算法 容器内部存储数据，利用迭代器将数据传入算法中进行计算
4. 仿函数(functions) 不理解这个概念
5. 适配器(adapters) 用于修饰容器或者仿函数或者迭代器的接口 比如stack和queue都是基于deque实现 所以被称为容器适配器 
6. 分配器(allocators) 用于管理内容空间 主要是分配内存与回收内存

## 分配器



# STL源码理解