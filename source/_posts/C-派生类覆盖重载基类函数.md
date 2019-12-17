---
title: C++ 派生类覆盖重载基类函数
date: 2019-12-17 17:02:07
categories: C++
tags:
---
原文链接：https://www.cnblogs.com/azbane/p/11073125.html

### 派生类希望基类重载函数可见，情况有三种：

a. 派生类中覆盖某个版本，则某个版本可见，全部都覆盖重写，则全部版本可见。

b. 派生类中一个也不覆盖，则全部基类版本可见。

c. 派生类需要添加新的重载版本，同时又需要可见基类的重载版本，此时并不是必须全部覆盖重写全部版本。可在派生类中用using声明基类重载的函数。

说明：对派生类没有重新定义的重载版本的访问实际上是对using声明点的访问。

### 两种注意点：

a. 指向派生类的基类指针或引用，不可调用派生类中的重载函数。
  原因：静态编译名字查找先于类型检查，在基类中找到同名函数，就不喊在派生类中继续找，而基类不存在目标调用形式的函数

b. 若不用using声明基类的重载版函数，派生类对象不可调用基类的重载版本。
  原因：派生类的函数，隐藏了基类的同名函数。

```cpp
h----------------------
#ifndef DEMOCLASS_H
#define DEMOCLASS_H

#include <iostream>
using namespace std;

class DemoClass
{
public:
    DemoClass();
    void func(int);
    void func(char);
};

class DerivedClass:public DemoClass
{
 public:
     using DemoClass::func;
     void func(string);
};


#endif // DEMOCLASS_H

cpp---------------------------
#include "democlass.h"

DemoClass::DemoClass()
{
    cout<<"construct..."<<endl;
}

void DemoClass::func(int)
{
    cout<<"DemoClass func(int)..."<<endl;
}

void DemoClass::func(char)
{
    cout<<"DemoClass func(char)..."<<endl;
}

void DerivedClass::func(string)
{
    cout<<"DerivedClass func(string)..."<<endl;
}

main----------------------
#include <QCoreApplication>
#include "democlass.h"

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    DemoClass *demo;
    DerivedClass derived;
    demo = &derived;

    cout<<"...Demo object that point to Derived access func..."<<endl;
    demo->func(1);
    demo->func('a');

    cout<<endl<<"...derived object access func..."<<endl;
    derived.func(1);
    derived.func('a');
    derived.func("abc");
    return a.exec();
}

运行结果--------------------
construct...
...Demo object that point to Derived access func...
DemoClass func(int)...
DemoClass func(char)...

...derived object access func...
DemoClass func(int)...
DemoClass func(char)...
DerivedClass func(string)...
```
### 覆盖
基类虚构函数被子类函数重写或者覆盖。
结论：覆盖是指派生类函数覆盖基类函数
补充：特征是：
       （1）不同的范围（分别位于派生类与基类）；
       （2）函数名字相同；
       （3）参数相同；
       （4）基类函数必须有virtual 关键字。

### 隐藏
隐藏比如父类有一非虚函数，子类又重写改写了它，那么父类该函数就被隐藏了。调用子类对象的该函数，就只会调用子类的该函数。
解释：
（1）如果派生类的函数与基类的函数同名，但是参数不同。此时，不论有无virtual
关键字，基类的函数将被隐藏（注意别与重载混淆）。
（2）如果派生类的函数与基类的函数同名，并且参数也相同，但是基类函数没有virtual
关键字。此时，基类的函数被隐藏（注意别与覆盖混淆）。