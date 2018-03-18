---
layout:     post
title:      STL源码理解 - vector
subtitle:   vector 数据结构分析与内存管理机制剖析
date:       2018-03-17
author:     YangGuang
header-img: img/post-bg-c++mysql.jpg
catalog: true
tags:
    - C++
    - STL
---
> STL 源码 -> vector

## Vector
Vector的数据操作方式与C++自带的array非常类似，它们之间的差别在于空间运用的灵活性，array是静态空间，一旦配置了就不能改变，想要换一个大一点的容量，非常的麻烦，需要进行内存重新申请三大步骤，首先需要配置一块新空间，然后copy旧空间内容到心空空间，接着释放旧空间。而Vector是动态空间，随着元素的加入内部会自行扩充空间。


## Vector的迭代器
Vector的迭代器其实就是一个普通的指针，也就是Random Access Iterator。因为vector维护的是一个连续的线性空间，一个普通指针足矣。vector迭代器所需要的操作行为如 : operator* , operator->, operator++, operator--, operator+, operator-, operator+=, operator-=.这些操作普通指针都支持.

  **vector 迭代器的定义**
  ```c++
    template <class T, class Alloc = alloc>
    class Vector {
      typedef T value_type;
      typedef value_type*       iterator;
      typedef const value_type* const_iterator;
      //...
    };

  ```

  我在这里只给出了一个迭代器的定义，如果想查看完整Vector代码，我自己利用C++11正在写一个stl，我把它命名为gstl，有需要的点击-> [gstl源码GitHub](https://github.com/Sunshine334419520/gstl)

## Vector 数据结构
Vector所采用的数据结构是STL中最简单的了，就是一个线性表,Vector 维护着三个迭代器，start用来指向线性表开始的位置，finish用来指向线性表被使用的结束位置 + 1（这是STL惯用的套路，而且非常实用，这样start其实就是begin(), finish就是end() ), end_of_storage这个迭代器指向整个线性表的尾端.

  ```c++
    template <class T, class Alloc = alloc>
    class Vector {
    //...
    private:
    iterator start_;
    iterator finish_;
    iterator end_of_storage_;
    };
    ```

一张图就可以明白这个简单的数据结构了

![Markdown](https://farm1.staticflickr.com/797/39068102040_bed6272b4c_z.jpg)


## Vector 的内存管理

Vector是一个动态空间，在你空间不足的时候会申请2倍当前大小的新空间，虽然还是需要上面所说的三大步骤，但是它缺大大的减少了需要重新配置空间的次数。

## Vector 元素操作

#### 插入(push_back， insert 为例)
push_back :
在尾端插入其实就是在finish的前面插入一个数，如果有配用空间就直接插入，没有配用空间代表线性表空间不够了，就重新分配内存

重新分配内存几大步骤
1. 首先确定新大小（vector规定为原大小的两倍）
2. 申请新空间
3. 拷贝旧空间元素到新空间
3. 将新内容插入
4. 析构旧空间

#### 删除(erase)
vector的erase函数原型 :
iterator erase(iterator position);
iteartor erase(iterator first, iteartor last);
ps ：在这里只列出两个erase函数的版本

删除指定位置的元素步骤:
1. 如果删除的元素不是最后一个元素，就把删除元素后面一个元素一直到finish，拷贝到删除元素到位置 copy(position + 1, finish, position);， 然后改变finish大小(--finish);
2. 如果删除到元素是最后一个元素，直接改变finish大小(--finish)
3. 释放最后一个元素 (destroy(finish))


删除[first, last), 记住STL采用的是半开闭区间

删除元素步骤 :
1. 拷贝要删除后面的元素到删除元素的首位置，也就是i = copy(last, finish, first);
2. 析构从i到最后一个节点的元素 destroy(i, finish);
3. 调整finish，让它指向最后的元素 finish = finish - （last - first);

# 结语
我在这里只是大概讲了一下vector到部分操作，主要是为了重新回顾vector内容，我自己利用C++11写到一个gstl已经把map容器写完，如果也有同样想写STL到同穴可以看一哈 [gstl源码GitHub](https://github.com/Sunshine334419520/gstl)









  
