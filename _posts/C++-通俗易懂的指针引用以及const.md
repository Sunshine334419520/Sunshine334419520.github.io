---
layout:     post
title:      C/C++ 引用 指针 const
subtitle:   深度解析引用与指针，以及const的使用
date:       2017-12-08
author:     YG
header-img: img/post-bg-debug.png
catalog: true
tags:
    - C
    - C++
    - Mac OS
---
> 深度理解指针以及引用

# 引用
首先，引用没有对象。引用就是某一个变量的别名，并不会申请内存，所以也就没有对象,引用的初始化必须是是一个对象，而且也必须初始化。
#### example

```C/C++

  int a = 10;
  int& x = &a;  //正确
  int& y = 0;   //错误，引用必须绑定到对象上
  int& z;       //错误，引用必须初始化

```


# 指针

首先，指针是一个对象。通俗一点讲，指针也是一种类型，不过指针类型保存的是地址，而整数类型保存的是值。



#### 一级指针
  ```C/C++

    int a = 10;
    int* p1 = 0;     //正确，空指针
    int* p2 = &a;    //正确，保存a的地址
    int* p3 = p2;    //正确，保存a的地址，p2是a的地址，*p2是a的值
    *p2 = 20;        //正确， a的值被改为20
  ```
\*p2，这个写法叫解引用，就是取p1保存的地址的值，p2保存的是a的地址，也就是说*p2就是10

#### 二级指针
>二级指针也叫指针的指针，一级指针保存地址，二级指针保存指针的地址

  ```

  int a = 10;
  int b = 20;
  int* ptr1 = &b;
  int* ptr2 = &a;      //正确，保存a的地址
  int** pptr = &ptr2;  //正确，保存ptr指针的地址
  **pptr = b;          //正确，改变a的值
  *pptr = ptr1;         //正确，改变指针ptr1的指向，现在ptr1保存的是b的地址
  ```
你很有可能已经迷糊了，但是你还是要记住，二级指针可以改变一级指针的指向。\*\*pptr就是a，\*pptr就是ptr2，pptr代表二级指针。里面保存着ptr2的地址。

# const关键字

用const修饰的类型叫做常量，常量是不可以被改变的，数字就是常量比如0，1，……. 因为const对象一旦创建就不能改变，所以const对象必须初始化。

#### const + 普通类型

  ```C/C++

    const int i = get_size();   //正确，运行时初始化
    const int j = 100;          //正确，编译时初始化
    const int k;                //错误，必须初始化
    j = 10;                     //错误，const对象不能改变
    int x = j;                  //错误，int类型不能接受const int 对象
  ```

#### const + 引用
  > const引用，也叫做常量引用

  ```
    int i = 100;
    const int& r1 = i;          //正确， 允许将const int&绑定到普通的对象
    const int& r2 = 42;         //正确，r1常量引用
    const int& r3 = r1 * 2;     //正确，r3常量引用
  ```

#### const + 指针
>底层 const example-> const int*

  ```c/c++

    int i = 0;
    int j = 1;
    const int* p1 = &i;    //正确，底层const,表示i是一个常量，文艺一点叫指针常量
    *p1 = 10;              //错误，i是常量，不能改变。
    p1 = &j;               //正确，改变指针p1的指向。
  ```

>顶层 const example-> int* const，顶层const只适合指针对象

  ```
      int i = 0;
      int j = 1;
      int const* p1 = &i; //正确，顶层const，也叫做常量指针
      *p1 = 20;           //正确，注意，上面这个代码是错误的
      p1 = &j;            //错误，p1是一个常量指针
  ```

**请注意！！！底层const的说明是指针常量，而顶层const的说明是常量指针，指针常量表示指针保存的对象是一个常量，而常量指针表示指针本身就是常量。**
