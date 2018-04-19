## Question

I'm partial to using member initialization lists with my constructors... but I've long since forgotten the reasons behind this...

Do you use member initialization lists in your constructors? If so, why? If not, why not?

## Answer

For **POD** class members, it makes no difference, it's just a matter of style. ***For class members which are classes, then it avoids an unnecessary call to a default constructor.*** Consider:

```c++
class A
{
public:
    A() { x = 0; }
    A(int x_) { x = x_; }
    int x;
};

class B
{
public:
    B()
    {
        a.x = 3;
    }
private:
    A a;
};
```

In this case, the constructor for `B` will call the default constructor for `A`, and then initialize `a.x` to 3. A better way would be for `B`'s constructor to directly call `A`'s constructor in the initializer list:

```c++
B()
  : a(3)
{
}
```

This would only call `A`'s `A(int)` constructor and not its default constructor. In this example, the difference is negligible, but imagine if you will that `A`'s default constructor did more, such as allocating memory or opening files. You wouldn't want to do that unnecessarily.

***Furthermore, if a class doesn't have a default constructor, or you have a const member variable, you must use an initializer list:***

```c++
class A
{
public:
    A(int x_) { x = x_; }
    int x;
}

class B
{
public:
    B() : a(3), y(2)  // 'a' and 'y' MUST be initialized in an initializer list;
    {                 // it is an error not to do so
    }
private:
    A a;
    const int y;
};
```

> ***Remarks*** For members of fundamental types, it makes no difference which of the ways above the constructor is defined, because they are not initialized by default, ***but for member objects (those whose type is a class), if they are not initialized after the colon, they are default-constructed.***

## References

1. [Why should I prefer to use member initialization list?](https://stackoverflow.com/questions/926752/why-should-i-prefer-to-use-member-initialization-list)
