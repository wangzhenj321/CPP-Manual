**Overload(重载)：**在C++程序中，可以将语义、功能相似的几个函数用同一个名字表示，但参数或返回值不同（包括类型、顺序不同），即函数重载。
1. 相同的范围（在同一个类中）
2. 函数名字相同
3. 参数不同
4. virtual 关键字可有可无

**Override(覆盖)：**是指派生类函数覆盖基类函数，特征是：
1. 不同的范围（分别位于派生类与基类）
2. 函数名字相同
3. 参数相同
4. 基类函数必须有virtual 关键字

**Overwrite(重写)：**是指派生类的函数屏蔽了与其同名的基类函数，规则如下：
1. 如果派生类的函数与基类的函数同名，但是参数不同。此时，不论有无virtual关键字，基类的函数将被隐藏（注意别与重载混淆）。
2. 如果派生类的函数与基类的函数同名，并且参数也相同，但是基类函数没有virtual关键字。此时，基类的函数被隐藏（注意别与覆盖混淆）。

```c++
#include <stdio.h>
#include <iostream>
class Parent
{
public:
    void F()
    {
        printf("Parent.F()/n");
    }
    virtual void G()
    {
        printf("Parent.G()/n");
    }
    int Add(int x, int y)
    {
        return x + y;
    }
    //重载(overload)Add函数
    float Add(float x, float y)
    {
        return x + y;
    }
};

class ChildOne:Parent
{
    //重写(overwrite)父类函数
    void F()
    {
        printf("ChildOne.F()/n"); 
    }
    //覆写(override)父类虚函数,主要实现多态
    void G()
    {
        printf("ChildOne.G()/n");
    }
};


int main()
{
    ChildOne childOne;// = new ChildOne();
    Parent* p = (Parent*)&childOne;
    //调用Parent.F()
    p->F();
    //实现多态
    p->G();
    Parent* p2 = new Parent();
    //重载(overload)
    printf("%d/n",p2->Add(1, 2));
    printf("%f/n",p2->Add(3.4f, 4.5f));
    delete p2;
    system("PAUSE");
    return 0;
}
```

## References

1. [C++中Overload、Overwrite及Override的区别](https://blog.csdn.net/penzo/article/details/6001193)
