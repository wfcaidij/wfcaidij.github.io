---
title: C++ cin方法小结
date: 2019-08-27 09:28:09
categories:
tags:
---

转载原文链接：https://blog.csdn.net/m0_37409332/article/details/55549933

##  一、输入原理
程序的输入都建有一个缓冲区，即输入缓冲区。一次输入过程是这样的，当一次键盘输入结束时会将输入的数据存入输入缓冲区，而cin函数直接从输入缓冲区中取数据。正因为cin函数是直接从缓冲区取数据的，所以有时候当缓冲区中有残留数据时，cin函数会直接取得这些残留数据而不会请求键盘输入

```c++
#include <iostream>

using namespace std;

int main()
{
    char str [8];
    cout << "输入：";  // 输入happy new year[Enter]
    cin.get(str,5);
    cout << str<< endl;   // 输出happ
    cin.get(str,5);
    cout << str << endl; // 输出y ne
    return 0;
}
```
[分析]：之所以第一次输入完后直接程序就结束了，而不是进行第二次输入，是因为第一次多输入的数据还残留在缓存区中，第二次输入就直接从缓存区中提取而不会请求键盘输入

## 二、几种常见的输入方式

### 1. cin >>
该操作符是根据后面变量的类型读取数据。

输入结束条件：遇到Enter、Space、Tab键。

对结束符的处理：丢弃缓冲区中使得输入结束的结束符(Enter、Space、Tab)

cin会检查输入格式，输入与预期格式不符时，会返回false，正常情况下则会返回输入流本身；

```c++
#include <iostream>

using namespace std;

int main()
{
    char str1[10],str2[10]; 
    cout << "请输入"; // 输入：happy new year[Enter]
    cin << str1;   // 接收happy  
    cin << str2;   // 接收new
    cout << str1<< endl; // 输出happy 
    cout << str2<< endl; // 输出new
    return 0;
}
```
[分析]：第一次读取字符串时遇到空格则停止了，将happy读入str1，并舍弃了空格，将后面的字符串给了第二个字符串。这证明了cin读入数据遇到空格结束；并且丢弃空格符；缓冲区有残留数据，读入操作直接从缓冲区中取数据。

### 2.cin.get(数组名，长度，结束符) 
其中结束符为可选参数，读入的字符个数最多为长度减1个，结束符规定结束字符串读取的字符，默认为Enter。

1.读取字符的情况：

若要读取字符，直接cin.get(char ch)或ch=cin.get()即可。

输入结束条件：Enter键

对结束符处理：不丢弃缓冲区中的Enter

cin.get()与cin.get(char ch)用于读取字符，他们的使用是相似的，即：ch=cin.get()与cin.get(ch)是等价的。若文件读取最好使用文件流.get(ch)。

```c++
#include <iostream>

using namespace std;

int main()
{
   char  ch1,ch2;
   cout<<"请输入";  // 第1次输入a[Enter] 第2次输入a[Space]b
   cin.get(ch1);   // 第1次接收a  第2次接收a
   cin.get(ch2);   // 第1次接收[Enter]  第2次接收[Space]
   cout<< (int)ch1<<"|"<< (int)ch2<<endl; // 第1次输出97|10 第2次输出97|32
    return 0;
}
```

[分析1]会发现只执行了一次从键盘输入，显然第一个字符变量取的’a', 第二个变量取的是Enter(ASCII值为10)，这是因为该函数不丢弃上次输入结束时的Enter字符，所以第一次输入结束时缓冲区中残留的是上次输入结束时的Enter字符！

[分析2]显然第一个字符变量取的’a', 第二个变量取的是Space(ASCII值为32)。原因同上，没有丢弃Space字符。
