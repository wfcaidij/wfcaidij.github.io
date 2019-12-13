---
title: 如何让相同签名的C++函数返回不同数据类型
date: 2019-12-13 08:22:44
categories: C++
tags:
---

```cpp
class Widget
{
public:
    auto getEntity()
    {
        struct result
        {
            operator Foo*()
            {
                return widget->getEntityAsFoo();
            }

            operator Bar*()
            {
                return widget->getEntityAsBar();
            }
            Widget* widget;
        };

        return result{ this };
}
private:
    Foo* getEntityAsFoo()
    {
        std::cout << "I'm Foo." << std::endl;

        return new Foo();
    }

    Bar* getEntityAsBar()
    {
        std::cout << "I'm Bar." << std::endl;

        return new Bar;
    }
};

int main()
{
    // CC Development Studio - http://ccrun.com
    Widget widget;
    Foo* foo = widget.getEntity();
    Bar* bar = widget.getEntity();
}
```

