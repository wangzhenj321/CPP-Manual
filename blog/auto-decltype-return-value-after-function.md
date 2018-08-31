**Table of Contents**

[auto](#auto)

[decltype and the new return value syntax](#decltype-and-the-new-return-value-syntax)

[Auto, References, Pointers and Const](#Auto, References, Pointers-and-Const)

[References](#References)


## auto

In C++11, if the compiler can infer the type of a variable at the point of declaration, instead of putting in the variable type, you can just write auto:

```c++
int x = 4;
```

can now be replaced with

```c++
auto x = 4;
```

This, of course, is not the intended use of auto at all! It really shines when working with ***templates*** and ***iterators***:

```c++
vector<int> vec;
auto itr = vec.iterator(); // instead of vector<int>::iterator itr
```

There are other times where auto comes in handy, too. For example, let's say that you had some code of the form:

```c++
template <typename BuiltType, typename Builder>
void
makeAndProcessObject (const Builder& builder)
{
    BuiltType val = builder.makeObject();
    // do stuff with val
}
```

In this code, you can see that there are two necessary template parameters--one for the type of the "builder" object, and a second for the type of the object being built. Even worse, the type of the built object cannot be deduced by the template parameter. Every call must look like this:

```c++
MyObjBuilder builder;
makeAndProcessObject<MyObj>( builder );
```

But auto immediately reduces this ugliness to nothing because you no longer need to be able to write the specific type at the point where you build the object. You can let C++ do it for you:

```c++
template <typename Builder>
void
makeAndProcessObject (const Builder& builder)
{
    auto val = builder.makeObject();
    // do stuff with val
}
```

Now you only need a single template parameter, and that parameter is easily inferred when calling the function:

```c++
MyObjBuilder builder;
makeAndProcessObject( builder );
```

This is way better for the caller, and the template code loses nothing in readability--if anything, it's easier to understand!


## decltype and the new return value syntax

Now you might be saying here--okay, that's great, but what if I wanted to *return* the value that this builder object created? I still need to provide the template argument becuase I need to provide the return type. Well, it turns out that the standards committee is full of smart people, and they have an extremely nice solution to this problem. It comes in two parts: decltype and the new return value syntax.

#### New Return Value Syntax

Let's start off with the new, optional, return value syntax, since it manages to find yet another use for auto. In all prior versions of C and C++, the return value of a function absolutely had to go before the function:

```c++
int multiply (int x, int y);
```

In C++11, you can now put the return value at the end of the function declaration, substituting auto for the name of the return type, if you want to:

```c++
auto multiply (int x, int y) -> int;
```

So would you want to do this? Let's look at a simple example where it helps us: a class with an enum declared inside it:

```c++
class Person
{
public:
    enum PersonType { ADULT, CHILD, SENIOR };
    void setPersonType (PersonType person_type);
    PersonType getPersonType ();
private:
    PersonType _person_type;
};
```

Here we have a simple class, Person, that has a type: whether the person is an adult, a child, or a senior citizen. Not much special about it, but what happens when you go to define the methods?

The first method, the setter, is trivial to declare, you can use the enum type PersonType without any trouble:

```c++
void Person::setPersonType (PersonType person_type)
{
    _person_type = person_type;
}
```

On the other hand, the second method is a bit of a mess. The nice clean looking code won't compile:

```c++
// the compiler doesn't know what PersonType is because PersonType is being used outside of the 
// Person class
PersonType Person::getPersonType ()
{
    return _person_type;
}
```

You have to write:

```c++
Person::PersonType Person::getPersonType ()
{
    return _person_type;
}
```

to make the return value work correctly. This isn't that big of a deal, but it's pretty easy to do by mistake, and it can get much messier when templates are involved.

This is where the new return value syntax comes in. Because the return value goes at the end of the function, instead of before it, you don't need to add the class scope. By the point the compiler reaches the return value, it already knows the function is part of the Person class, so it knows what PersonType is.

```c++
auto Person::getPersonType () -> PersonType
{
    return _person_type;
}
```

Okay, while that's very nice, does it really help us out? We can't use this new syntax to solve the problem we had before, can we? Well, not yet. Let's add one more concept: decltype

#### decltype

Decltype is auto's not-evil twin. Auto lets you declare a variable with a particular type; decltype lets you extract the type from a variable (or any other expression). What do I mean?

```c++
int x = 3;
decltype(x) y = x; // same thing as auto y = x;
```

You can use decltype for pretty much any expression, including to express the type for a return value from a method. Hmm, that sounds like a familiar problem doesn't it? What if we could write:

```c++
decltype( builder.makeObject() )
```

This would give us the type that is returned from makeObject, allowing us to specify the return value from makeAndProcessObject. We can combine this with the new return value syntax to produce this method:

```c++
template <typename Builder>
auto
makeAndProcessObject (const Builder& builder) -> decltype( builder.makeObject() )
{
    auto val = builder.makeObject();
    // do stuff with val
    return val;
}
```

This only works with the new return value syntax because under the old syntax, we couldn't refer to the function argument, builder, at the point where we declare the return type. With the new syntax, all of the arguments to a function are fair game!


## Auto, References, Pointers and Const

One question that is sure to come up is how auto handles references:

```c++
int& foo();

auto bar = foo(); // int& or int?
```

The short answer is in C++11, auto defaults to being by-value for references, so in the above code bar is an int. However, you can add the & as a modifier to force it to be a reference:

```c++
int& foo();

auto bar = foo(); // int
auto& baz = foo(); // int&
```

On the other hand, if you have a pointer auto will automatically pick up pointerness:

```c++
int* foo();

auto p_bar = foo(); // int*
```

But you can also (thankfully) be explicit about it, and indicate that the variable is a pointer:

```c++
int* foo();
auto *p_baz = foo(); // int*
```

You can similarly tack const onto auto if you need it, when dealing with references:

```c++
int& foo();

const auto& baz = foo(); // const int&
```

Or with pointers:

```c++
int* foo();
const int* const_foo();
const auto* p_bar = foo(); // const int*
auto p_bar = const_foo(); // const int*
```

Overall, it feels quite natural and normal, and it follows the normal type inference rules of templates in C++.


## References

1. [Improved Type Inference in C++11: auto, decltype, and the new function declaration syntax](https://www.cprogramming.com/c++11/c++11-auto-decltype-return-value-after-function.html)
