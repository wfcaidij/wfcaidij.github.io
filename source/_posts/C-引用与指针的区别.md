---
title: C++引用与指针的区别
date: 2019-09-05 14:27:27
categories: C++
tags:
---
原文链接：https://blog.csdn.net/weixin_41042404/article/details/86717934

### 相同点

都是地址的概念；
   指针指向一块内存，它的内容是所指内存的地址；引用是某块内存的别名。
   指针的权威定义：
```
In a declaration T D where D has the form * cv-qualifier-seqopt D1 And the type of the identifier in the declaration T D1 is “derived-declarator-type-list T”, then the type of the identifier of D is “derived-declarator-type-list cv-qualifier-seq pointer to T”. The cv-qualifiers apply to the pointer and not to the object pointer to. 
——摘自《ANSI C++ Standard》 
注：可能有些读者并不明白cv-qualifier-seq 
CV-qualifiers(CV限定符) 
CV-qualifiers有三种：const-qualifier(const限定符)、Volatile-qualifier(volatile限定符)、以及const-volatile-qualifier(const-volatile限定符)。 
const类对象的非静态、非mutable、以及非引用数据成员是const-qualified； 
volatile类对象的非静态、非引用数据成员是volatile-qualified； 
const-volatile类对象的非静态、非引用数据成员是const-volatile-qualified。 
当CV-qualifiers用于限定数组类型时，实际上是数组成员被该CV-qualifiers限定，而非该数组类型。 
复合类型并不因其成员被CV-qualifier限定而被该CV-qualifier限定，也就是说，即使复合类型的成员有CV-qualifier限定，该复合类型也不是CV-qualified对象。

引用的权威定义：

In a declaration T D where D has the form& D1 And the type of the identifier in the declaration T D1 is “derived-declarator-type-list T”, then the type of the identifier of D is “derived-declarator-type-list cv-qualifier-seq reference to T”. Cv-qualified references are ill-formed except when the cv-qualifiers are introduced through the use of a typedef or a template type argument, in which case the cv-qualifiers are ignored. 
——摘自《ANSI C++ Standard》

上面这些定义初看有些难懂，如果是这样的话，那说明你对C++还不够熟悉，你还有很长的路要走。下面用通俗易懂的话来概述一下：

指针-对于一个类型T，T*就是指向T的指针类型，也即一个T*类型的变量能够保存一个T对象的地址，而类型T是可以加一些限定词的，如const、volatile等等。

引用-引用是一个对象的别名，主要用于函数参数和返回值类型，符号X&表示X类型的引用。
```

---
### 区别

指针是一个实体，而引用仅是个别名；
引用使用时无需解引用（\*）指针需要解引用
```
引用比指针使用起来形式上更漂亮，使用引用指向的内容时可以直接用引用变量名，而不像指针一样要使用*；定义引用的时候也不用像指针一样使用&取址。 
```
引用只能在定义时被初始化一次，之后不可变；指针可变；    引用“从一而终” ^_^

引用没有 const，指针有 const，const 的指针不可变；

引用不能为空，指针可以为空；

“sizeof 引用”得到的是所指向的变量（对象）的大小，而“sizeof 指针”得到的是指针本身（所指向的变量或对象的地址）的大小；typeid（T） == typeid（T&） 恒为真，sizeof（T） == sizeof（T&） 恒为真，但是当引用作为成员时，其占用空间与指针相同（没找到标准的规定）。

 ```
    最后，引用比指针更安全。由于不存在空引用，并且引用一旦被初始化为指向一个对象，它就不能被改变为另一个对象的引用，因此引用很安全。对于指针来说，它可以随时指向别的对象，并且可以不被初始化，或为NULL，所以不安全。const 指针虽然不能改变指向，但仍然存在空指针，并且有可能产生野指针（即多个指针指向一块内存，free掉一个指针之后，别的指针就成了野指针）。

总而言之，言而总之——它们的这些差别都可以归结为”指针指向一块内存，它的内容是所指内存的地址；而引用则是某块内存的别名，引用不改变指向。”
```

---
### 指针和引用的自增（++）运算意义不一样

声明指针是可以不指向任何对象，也正是因为这个原因，使用指针之前必须做判空操作，而引用就不必。 

其次，引用不可以改变指向，对一个对象”至死不渝”；但是指针可以改变指向，而指向其它对象。说明：虽然引用不可以改变指向，但是可以改变初始化对象的内容。例如就++操作而言，对引用的操作直接反应到所指向的对象，而不是改变指向；而对指针的操作，会使指针指向下一个对象，而不是改变所指对象的内容。见下面的代码：

```c++
#include <iostream>
using namespace std;
int main()
{
    int a = 1;
    int& b =a;

    b++;
    cout << a << endl;   // 输出2
    cout << b << endl;  // 输出2
    
    int j =5;
    
    b = j;
    b++;
    
    cout << a << endl;   // 输出6
    cout << b << endl;  // 输出6
    cout << j << endl;   // 输出5
    
    return 0;
}
```
对ref的++操作是直接反应到所指变量之上，对引用变量ref重新赋值”ref=j”，并不会改变ref的指向，它仍然指向的是i，而不是j。理所当然，这时对ref进行++操作不会影响到j。

---
### 联系

引用在语言内部用指针实现（如何实现？）。

对一般应用而言，把引用理解为指针，不会犯严重语义错误。引用是操作受限了的指针（仅容许取内容操作）。

引用是C++中的概念，初学者容易把引用和指针混淆一起。一下程序中，n 是m 的一个引用（reference），m是被引用物（referent）。

int m；
int &n = m；
n 相当于m 的别名（绰号），对n 的任何操作就是对m 的操作。例如有人名叫王小毛，他的绰号是“三毛”。说“三毛”怎么怎么的，其实就是对王小毛说三道四。所以n 既不是m 的拷贝，也不是指向m 的指针，其实n就是m 它自己。

引用的一些规则如下：
（1）引用被创建的同时必须被初始化（指针则可以在任何时候被初始化）。
（2）不能有NULL 引用，引用必须与合法的存储单元关联（指针则可以是NULL）。
（3）一旦引用被初始化，就不能改变引用的关系（指针则可以随时改变所指的对象）。

---
### 什么时候使用引用、什么时候使用指针

使用引用参数的主要原因有两个：

程序员能修改调用函数中的数据对象

通过传递引用而不是整个数据–对象，可以提高程序的运行速度 

---
### 一般的原则：

**对于使用引用的值而不做修改的函数：**

如果数据对象很小，如内置数据类型或者小型结构，则按照值传递

如果数据对象是数组，则使用指针（唯一的选择），并且指针声明为指向const的指针

如果数据对象是较大的结构，则使用const指针或者引用，已提高程序的效率。这样可以节省结构所需的时间和空间

如果数据对象是类对象，则使用const引用（传递类对象参数的标准方式是按照引用传递） 

**对于修改函数中数据的函数：**

如果数据是内置数据类型，则使用指针

如果数据对象是数组，则只能使用指针

如果数据对象是结构，则使用引用或者指针

如果数据是类对象，则使用引用