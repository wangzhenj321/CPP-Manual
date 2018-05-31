***References:*** https://www.cprogramming.com/c++11/c++11-auto-decltype-return-value-after-function.html

C++11 introduces several new handy-dandy type inference capabilities that mean you can spend less time having to write out things the compiler already knows. There are, of course, times when you need to help out the compiler or your fellow programmers. But with C++11, you can spend less time on the mundane stuff and focus on the logic.

Let's start by looking at the most immediately obvious new benefit, the auto keyword.

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

MyObjBuilder builder;
makeAndProcessObject<MyObj>( builder );
But auto immediately reduces this ugliness to nothing because you no longer need to be able to write the specific type at the point where you build the object. You can let C++ do it for you:

template <typename Builder>
void
makeAndProcessObject (const Builder& builder)
{
    auto val = builder.makeObject();
    // do stuff with val
}
Now you only need a single template parameter, and that parameter is easily inferred when calling the function:

MyObjBuilder builder;
makeAndProcessObject( builder );
This is way better for the caller, and the template code loses nothing in readability--if anything, it's easier to understand!

## decltype and the new return value syntax

## Auto, References, Pointers and Const

## So, do compilers support this stuff?
