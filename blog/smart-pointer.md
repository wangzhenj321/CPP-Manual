内存控制这一大毒瘤，几乎一直伴随着C/C++工程师。随着计算机技术的发展，在boost准标准库的推动下，C++11终于将`unique_ptr`、`shared_ptr`、`weak_ptr`这几类智能指针纳入C++中。当然，在这之前还有一种`auto_ptr`智能指针，不过由于它的设计存在较大问题（比如：`auto_ptr`与STL不兼容），现阶段已经很少能看到它的出现了。

这里说明一下，（1）上文中提到C++11出现了智能指针`unique_ptr`、`shared_ptr`、`weak_ptr`，（2）但是文中的代码示例使用了`make_unique()`和`std::move()`，这些为C++14中的内容。

---

> **智能指针实质是一个对象，行为表现的却像一个指针。**

---

`shared_ptr`和`unique_ptr`之间的区别在于：`shared_ptr`是引用计数的智能指针，而`unique_ptr`不是。这意味着，可以有多个`shared_ptr`实例指向同一块动态分配的内存，当最后一个`shared_ptr`离开作用域时，才会释放这块内存。~shared_ptr也是线程安全的~。 另一方面，`unique_ptr`意味着所有权。单个`unique_ptr`离开作用域时，会立即释放底层内存。

> `shared_ptr`本身不是线程安全的。陈硕的书中也明确提到了，`shared_ptr`的计数功能是原子的，但对象的读写不是原子的。c++标准也只是保证的是`weak_ptr`的`lock()`指针是线程安全的。所以，要实现线程安全，可能需要`weak_ptr`与`shared_ptr`配合使用，详见陈硕的多线程书籍。

也可以参考：http://www.pandademo.com/2017/08/thread-safety-of-shared_ptr-and-weak_ptr/

默认的智能指针应该是`unique_ptr`。只有需要共享资源时，才使用`shared_ptr`。

这两个智能指针都需要包含`<memory>`头文件。

在开始本文之前，首先给出一个类。因为下文中，为了演示智能指针的使用方式，在较多时候都有用到这个类demo。

```c++
#include <memory>
#include <utility> //std::move()

class demo {
public:
    demo() : uptr(std::make_unique<int[]>(10)){
        printf("demo\n");
        for (int i = 0; i < 10; ++i){
            uptr[i] = i;
        }
    }
    ~demo(){
        printf("~demo\n");
    }
    void show(){
        printf("%d\n", uptr[9]);
    }
private:
    std::unique_ptr<int[]> uptr;
};
```

## `unique_ptr`

`unique_ptr`是唯一的，适用于存储动态分配的旧C风格的数组。`auto`关键字会自动识别指针类型，当与`make_unique`配合使用时，即表示`unique_ptr`智能指针。

```c++
void my_unique_ptr(){
    auto uptr = std::make_unique<int[]>(10);
    uptr[5] = 17;
    printf("%d\n", uptr[5]);
}
```

这里应该总是使用`auto/make_unique`写法。除非编译器不支持的情况下，可以这样写，

```c++
std::unique_ptr<int[]> uptr(new int[10]);
//std::unique_ptr<int[]> uptr = new int[10];//error
```

注意，上文中，被注释的那种写法是不被接受的。再整理一下，`unique_ptr`可以有以下使用方式，

```c++
void my_unique_ptr2(){
    auto uptr = std::make_unique<demo>();
    uptr->show();
    std::unique_ptr<demo> uptr2(new demo());///等效写法
    uptr2->show();
}
```

另外，需要说明的是，`unique_ptr`无法使用拷贝构造函数的，上文已经提到过了，这里再给出一个示例，

```c++
void my_unique_ptr3(){
    auto uptr = std::make_unique<int>(42);
    printf("%d\n", *uptr);//42
    ///std::unique_ptr<int> uptr1 = uptr; ///unique_ptr无拷贝构造函数
    ///std::unique_ptr<int> uptr1(uptr);  ///等效写法
    std::unique_ptr<int> uptr2 = std::move(uptr);
    ///printf("%d\n",*uptr); ///error 所有权已交给uptr2
    printf("%d\n", *uptr2);
}
```

既然，无法使用拷贝构造函数，那么就无法直接使用赋值“=”来转移指针所有权。但是C++14设计者给开了另外一扇门：`std::move`。它被包含在`<utility>`头文件中。

## `shared_ptr`

`shared_ptr`用法与`unique_ptr`类似。如果编译器支持，你应该总是使用`auto/make_shared`的写法，它比直接创建`shared_ptr`更高效。

```c++
void my_shared_ptr(){
    ///auto sptr = std::make_shared<int[]>(10); //error
    ///sptr[6] = 20;
    auto sptr = std::make_shared<demo>();
    sptr->show();
}
```

上文中已经提到了，智能指针家族中，`unique_ptr`是唯一可以适用于旧C风格数组的指针，`shared_ptr`等其他智能指针不能。

`shared_ptr`除了用于管理纯粹的内存之外还可以用于其他的目的，比如管理`FILE`、`SOCKET`等，极大的增加了编程的方便性。

```c++
void auto_run_fun(FILE* f){
    printf("auto running.\n");
    fclose(f);
}

void my_shared_ptr2(){
    FILE* f = fopen("data.txt","w");
    std::shared_ptr<FILE> file_ptr(f, auto_run_fun);
}
```

由于`shared_ptr`是引用计数的，这里需要极为注意的一点是：**糟糕！只调用一次构造函数，却调用了两次析构函数。**

正确的使用方式应该是使用`make_shared`和拷贝构造函数建立副本。范例如下，

```c++
void my_shared_ptr3(){
    /*demo* d = new demo();
    std::shared_ptr<demo> sptr1(d);
    std::shared_ptr<demo> sptr2(d);//~demo() error
    */
    auto sptr3 = std::make_shared<demo>();
    std::shared_ptr<demo> sptr4(sptr3); ///shared_ptr拷贝构造函数
}
```

**`shared_ptr`引用计数，完全可以返回一个子函数的指针。** 在以往的认知中，子函数中的栈空间上的内存是无法返回的，而子函数中堆空间上的内存是可以返回的（同时，还必须注意手动释放它，否则必然内存泄漏）。

```c++
std::shared_ptr<demo> my_shared_ptr4(){
    auto sptr = std::make_shared<demo>();
    return sptr;
}
```

那么，对于以上代码的返回值，下文中这样子使用它，也是非常正确的。

```c++
my_shared_ptr4()->show();
```

智能指针，完全继承了JAVA和C#中内存托管的风格，而且智能指针在很多情况下完全可以具体推算出：它会在何时被释放。

关于`shared_ptr`更多使用方式，推荐[《C++14 N叉树使用shared_ptr智能指针》](https://blog.csdn.net/qingdujun/article/details/87805085) 一文。

## `weak_ptr`

`weak_ptr`是`shared_ptr`的黄金伙伴。从上文知道`shared_ptr`与`shared_ptr`之间，每拷贝一次，引用计数就会+1，而如果使用`weak_ptr`则不会出现这个现象。

如果将一个`shared_ptr`指针赋值给`weak_ptr`指针，对`shared_ptr`指针本身不会造成任何影响。对于`weak_ptr`指针来说，却可以通过一些方法来探测被赋值过来的`shared_ptr`指针的有效性，同时`weak_ptr`指针也可以间接操纵`shared_ptr`指针。以下主要介绍两个方法：

- `lock()`: `weak_ptr`指针调用`lock()`方法会获得一个返回值：`shared_ptr`。而这个返回值就是被赋值过来的`shared_ptr`指针，那么指针都获得了，当然可以操纵它。
- `expired()`: 该方法主要用来探测`shared_ptr`指针的有效性。`shared_ptr`一旦被释放，指针就会被置为`nullptr`。

```c++
///weak_ptr -> shared_ptr
void my_weak_ptr(){
    std::weak_ptr<demo> wptr;
    {
        auto sptr = std::make_shared<demo>();
        wptr = sptr;
        auto sptr2 = wptr.lock();
        if (!wptr.expired()){///等价于sptr2 != nullptr
            printf("shared_ptr ok\n");
            sptr2->show();
        }
    }
    if (wptr.expired()){
        printf("shared_ptr deleted\n");
    }
}
```

以上代码中，之所以要加个大括号{}在文中，主要是：为了利用变量的作用域原理。 让`shared_ptr`指针离开{}作用域后，立即被释放。

## References

1. [C++14 智能指针unique_ptr、shared_ptr、weak_ptr](https://blog.csdn.net/qingdujun/article/details/74858071)
