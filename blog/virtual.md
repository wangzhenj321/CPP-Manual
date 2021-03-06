## Part 1 virtual function specifier

***References:*** https://en.cppreference.com/w/cpp/language/virtual

The virtual specifier specifies that a non-static member function is virtual and supports dynamic dispatch. It may only appear in the decl-specifier-seq of the initial declaration of a non-static member function (i.e., when it is declared in the class definition).

1. Then this function in the class Derived is also virtual (whether or not the keyword virtual is used in its declaration) and overrides Base::vf (whether or not the word override is used in its declaration).

2. Base::vf does not need to be visible (can be declared private, or inherited using private inheritance) to be overridden.

## Part 2 virtual base class

***References:*** https://stackoverflow.com/questions/21558/in-c-what-is-a-virtual-base-class

**Virtual base classes, used in virtual inheritance, is a way of preventing multiple "instances" of a given class appearing in an inheritance hierarchy when using multiple inheritance.**

Consider the following scenario:

```c++
class A { public: void Foo() {} };
class B : public A {};
class C : public A {};
class D : public B, public C {};
```

The above class hierarchy results in the "dreaded diamond" which looks like this:

```
  A
 / \
B   C
 \ /
  D
```

An instance of D will be made up of B, which includes A, and C which also includes A. So you have two "instances" (for want of a better expression) of A.

When you have this scenario, you have the possibility of ambiguity. What happens when you do this:

```c++
D d;
d.Foo(); // is this B's Foo() or C's Foo() ??
```

Virtual inheritance is there to solve this problem. When you specify virtual when inheriting your classes, you're telling the compiler that you only want a single instance.

```c++
class A { public: void Foo() {} };
class B : public virtual A {};
class C : public virtual A {};
class D : public B, public C {};
```

This means that there is only one "instance" of A included in the hierarchy. Hence

```c++
D d;
d.Foo(); // no longer ambiguous
```
