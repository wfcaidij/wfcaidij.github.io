---
title: C++中的几种初始化方式
date: 2020-08-31 10:00:30
categories: C++
tags:
---
作者原文链接：https://www.jianshu.com/p/1aa7adeb7328

###  默认初始化
如果定义了变量没有指定初值，则变量被默认初始化，此时变量被赋予了默认值，默认值到底是什么由变量类型和变量的位置决定的。
默认初始化的形式：
```c++
type variableName;
```
如果变量类型是类类型，则执行其默认构造函数来初始化；如果是内置类型，若其在函数内部且是非静态的，则其值是未定义的，若其在函数外部，则为‘0’或者NULL。

###  直接初始化/构造初始化
使用构造函数初始化，构造初始化形式：
```c++
type variableName(args);
```

###  列表初始化

C++11引入了initializer_list &lt;type&gt;模板类，该模板类中的元素是const的，故当其作为实参时可以防止实参的类型转换；笔者推测这种所谓的列表初始化其实还是构造初始化，通过增加参数类型为initializer_list &lt;type&gt;的构造函数来实现，故列表初始化方式不能用于没有形参类型相同的构造函数的类。
列表初始化形式： 
```c++
type variableName{args};
```

###  拷贝初始化
如果用=初始化一个变量，实际上执行的是拷贝初始化。
拷贝初始化形式：
```c++
type variableName=otherVariableName;
```
如果是内置类型，就是简单的拷贝；如果是类类型，则实际上是调用了拷贝构造函数来初始化。
或
```c++
type variableName=type (args)
```
这种形式在C++Primer 这本书中解释是先创建一个临时对象，让后调用拷贝构造函数创建对象variableName,实际在IDE测试中，根本没有临时对象的创建，直接调用参数对应的构造函数，这应该是由于编译器的优化导致的。

###  值初始化
值初始化形式：
```c++
type variable();
```
值得注意的是：对于类类型而言，值初始化和默认初始化是一样的，均调用类的默认构造函数；对于内置类型，值初始化会置为‘0’或者NULL

测试代码：
```c++
#include <iostream>
#include <initializer_list>
#include <vector>

using namespace std;
struct test
{
    test(){cout<<"默认构造函数被调用"<<endl;}
    test(initializer_list<int> args);
    test(int number,int value);
    test(const test &other);
    void display(ostream &os);
    vector<int> container;
    void operator=(test &other);
    ~test();
};

int main()
{
    int a;//默认初始化
    cout<<a<<endl;
    int b();//值初始化
    cout<<b<<endl;
    test c(5,1);//构造函数初始化
    c.display(cout);
    string str{'a','b','c'};//列表初始化
    cout<<str<<endl;
    test d{1,2,3,4};//自定义类列表初始化
    d.display(cout);
    test e=d;//拷贝构造函数初始化
    e.display(cout);
    test f=test(4,2);//编译器优化后的构造函数初始化
    e.display(cout);
    return 0;
}


test::test(initializer_list<int> args):container()
{
    for(auto it=args.begin();it!=args.end();it++)
    {
        container.push_back(*it);
    }
    cout<<"自定义列表初始化构造函数被调用"<<endl;
}

test::test(int number,int value):container(number,value)
{
    cout<<"普通构造函数初始化"<<endl;
}
test::test(const test &other)
{
    this->container=other.container;
    cout<<"拷贝构造函数被调用"<<endl;
}
void test::display(ostream &os)
{
    for(auto it=container.begin();it!=container.end();it++)
    {
        os<<*it<<' ';
    }
    os<<endl;
}

void test::operator=(test &other)
{
    container=other.container;
    cout<<"赋值运算符被调用"<<endl;
}

test::~test()
{
    cout<<"test对象被析构"<<endl;
}
```
输出结果：![](C:/Users/wfc/Desktop/QQ截图20200831102019.png)