---
title: C++ cin方法小结
date: 2019-08-27 09:28:09
categories: C++
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

[分析1] ：会发现只执行了一次从键盘输入，显然第一个字符变量取的’a', 第二个变量取的是Enter(ASCII值为10)，这是因为该函数不丢弃上次输入结束时的Enter字符，所以第一次输入结束时缓冲区中残留的是上次输入结束时的Enter字符！

[分析2]：显然第一个字符变量取的’a', 第二个变量取的是Space(ASCII值为32)。原因同上，没有丢弃Space字符。

2.读取字符串的情况：

输入结束条件：默认Enter键（因此可接受空格，Tab键），可在第三个参数上自定义结束符

对结束符处理：默认时丢弃缓冲区中的Enter，自定义结束符时不丢弃缓冲区中的结束符

```c++
#include <iostream>

using namespace std;

int main()
{
   char  ch,str[20];
   cout<<"请输入";  // 第1次输入happy[Enter]，第2次输入hadppy
   cin.get(str,5,'d');  // 第1次接收happ，第2次接收ha
   cin.get(ch);    // 第1次接收y，第2次接收d
   cout<< str <<endl;  // 第1次输出happ，第2次输出ha
   cout<< (int)ch << endl;  //第1次输出121，第2次输出100
    return 0;
}
```
[分析1]：第一次输入超长，字符串按长度取了"1234"，而’5′仍残留在缓冲区中，所以第二次输入字符没有从键盘读入，而是直接取了’5′，所以打印的ASCII值是53(’5′的ASCII值)。

[分析2]：第二次输出为d，说明自定义结束符时不丢弃缓冲区中的结束符。

### 3. cin.getline()
cin.getline(数组名，长度，结束符) 大体与 cin.get(数组名，长度，结束符)类似。（实际能读取长度（-1）个字符，因为字符串结尾是0）；

区别在于：cin.get()当输入的字符串超长时，不会引起cin函数的错误，后面的cin操作会继续执行，只是直接从缓冲区中取数据。但是cin.getline()当输入超长时，会引起cin函数的错误，后面的cin操作将不再执行。

```c++
#include <iostream>

using namespace std;

int main()
{
   char  ch,str[20];
   cout<<"请输入";  // 第1次输入happy[Enter]，第2次输入happ
   cin.getline(str,5);  // 第1次接收happ，第2次接收happ
   cin.get(ch);    // 第1次接收y，第2次重新接收输入 a
   cout<< str <<endl;  // 第1次输出happ，第2次输出happ
   cout<< (int)ch << endl;  // 第1次输出0，第2次输出97
    return 0;
}
```
[分析] ：与cin.get()的例子比较会发现，这里的ch并没有读取缓冲区中的5，而是返回了-52，这里其实cin>>ch语句没有执行，是因为cin出错了！

### 4.cin.read(c, n)
从字符串流中读取n个字符到c数组中。

char score[20]; 

cin.read(score,20); 

## 三、其它方法
### 1. cin.putback(x)
将x内容置入缓冲区。

```c++
#include <iostream>

using namespace std;

int main()
{
    char ch1='M',ch2;
    cin.putback(ch1); // 把'M'字符回退到缓存区
    cin>>ch2;  // 接收缓存区的'M'字符
    cout<<ch1<<endl; // 输出M
    cout<<ch2<<endl; // 输出M
    return 0;
}
```
[分析]：将a中数据置入缓冲区后，直接流入b中。

### 2. cin.peek()
返回缓冲区中的下一个字符，但只是查看，并不从缓冲区中取出。

```c++
#include <iostream>

using namespace std;

int main()
{
    char input[20];
    int i=0;
    cout<<"请输入：";
    while(cin.peek()!='\n' && cin.peek()!='.')
    {
            cin.get(input[i++]);
    }
    input[i]='\0';
    cout<<input<<endl;
    return 0;
}
```
[分析]：程序遇到句号或换行符循环停止。句点或换行符仍停留在输入流中。可见，使用peek的效果相当于先用get()读取一个字符，再用putback()将字符放入输入流中。

### 3.cin.ignore(长度，结束符)
cin.ignore(a,ch)方法是从输入流（cin）中提取字符，提取的字符被忽略（ignore），不被使用。每抛弃一个字符，它都要计数和比较字符：如果计数值达到a或者被抛弃的字符是ch，则cin.ignore()函数执行终止；否则，它继续等待。它的一个常用功能就是用来清除以回车结束的输入缓冲区的内容，消除上一次输入对下一次输入的影响。比如可以这么用：cin.ignore(1024,'\n')，通常把第一个参数设置得足够大，这样实际上总是只有第二个参数'\n'起作用，所以这一句就是把回车（包括回车）之前的所以字符从输入缓冲（流）中清除出去。

```c++
#include <iostream>

using namespace std;

int main()
{
    char input[8];
    cout<< "请输入";
    cin.ignore(8,'a');
    cin.getline(input,8);
    cout<<input<<endl;
    return 0;
}
```
### 4.in.clear()、cin.sync()
cin.clear（）是用来更改cin的状态标示符的。

cin.sync（）是用来清除缓存区的数据流的。
如果标示符没有改变那么即使清除了数据流也无法输入。所以两个要联合起来使用。

```c++
#include <iostream>

using namespace std;

int main()
{
    int a;
    cout<<"输入一个字母："<<endl;
    cin >> a; // int 型变量中放了char型数据，failbit置1
    cout <<"cin.fail()="<<cin.fail()<<endl; // 输出1
    cout<<"输入一个数字："<<endl; // 由于failbit值为1，输入流不能正常工作
    cin>> a; // 故此处输入无效
    cout <<a<< endl;  //输出不确定值
    cin.clear(); // 此处用cin.clear() 流标志复位
    cout <<"cin.fail()="<<cin.fail()<<endl; // 此处failbit 已为0
    cout << "输入一个数字："<<endl; // 但刚才输入的字符并没有从流中清除,所以cin>>a又把那个字符放入a中,流输入流又不能正常工作
    cin >> a;
    cout<<a<<endl; // 输出不确定值
    cout <<"cin.fail()：=" << cin.fail()<<endl; //在此处failbit又为1
    cin.clear(); // 再次修复输入流
    cin.sync(); // 取走刚才流中的字符
    cout << "输入一个数字："<<endl; // 再次接收输入
    cin>>a;
    cout << "a="<<a<<endl;  // 正常输出
    cout << "cin.fail()="<<cin.fail()<<endl; // 查看failbit值为0 正常
    return 0;
}
```
### 5.cin.width(长度)
接收长度-1个字符，其他的放在流中等待接收

```c++
#include <iostream>

using namespace std;

int main()
{
    int w=4;
    char str[10];
    cout<<"Entera sentence:\n";
    cin.width(5); // 每次只接收4个字符，其他的放在流中等待接收
    while(cin >> str) // 输入happy new year
    {
       cout.width(w++); // 将4个字符输出，设置每次输出的域宽增加1
        cout << str <<endl; // 输出字符
        cin.width(5);
    }
    return 0;
}
```
[分析]：app（四个字符），y留在下一次。遇到空格接收结束，第二次只有y，到y输出时，输出域宽是5。下一个接收的是new（后面的空格断开了接收），……

### 6.数据进行合法性检查
cin对象有几个专门用来报告其工作情况的成员函数，他们将返回一个真/假值来表明cin的状态。
-eof（）：如果到达文件（或输入）末尾，返回true；

-fail（）：如果cin无法工作，返回ture；

-bad（）：如果cin因为比较严重的原因（例如内存不足）而无法工作，返回true；

-good（）：如果以上情况都没发生，返回true；