## Class templates

Just like we can create function templates, we can also create class templates, allowing classes to have members that use template parameters as types. For example:

```c++
template <class T>
class mypair {
    T values [2];
  public:
    mypair (T first, T second)
    {
      values[0]=first; values[1]=second;
    }
};
```

The class that we have just defined serves to store two elements of any valid type. For example, if we wanted to declare an object of this class to store two integer values of type `int` with the values 115 and 36 we would write:

```c++
mypair<int> myobject (115, 36);
```

This same class could also be used to create an object to store any other type, such as:

```c++
mypair<double> myfloats (3.0, 2.18);
```

The constructor is the only member function in the previous class template and it has been defined inline within the class definition itself. In case that a member function is defined outside the defintion of the class template, it shall be preceded with the template <...> prefix:

```c++
// class templates
#include <iostream>
using namespace std;

template <class T>
class mypair {
    T a, b;
  public:
    mypair (T first, T second)
      {a=first; b=second;}
    T getmax ();
};

template <class T>
T mypair<T>::getmax ()
{
  T retval;
  retval = a>b? a : b;
  return retval;
}

int main () {
  mypair <int> myobject (100, 75);
  cout << myobject.getmax();
  return 0;
}
```

Notice the syntax of the definition of member function `getmax`:

```c++
template <class T>
T mypair<T>::getmax ()
```

Confused by so many T's? There are three T's in this declaration: The first one is the template parameter. The second T refers to the type returned by the function. And the third T (the one between angle brackets) is also a requirement: It specifies that this function's template parameter is also the class template parameter.

## Template specialization

It is possible to define a different implementation for a template when a specific type is passed as template argument. This is called a *template specialization*.

For example, let's suppose that we have a very simple class called *mycontainer* that can store one element of any type and that has just one member function called *increase*, which increases its value. But we find that when it stores an element of type char it would be more convenient to have a completely different implementation with a function member *uppercase*, so we decide to declare a class template specialization for that type:

```c++
// template specialization
#include <iostream>
using namespace std;

// class template:
template <class T>
class mycontainer {
    T element;
  public:
    mycontainer (T arg) {element=arg;}
    T increase () {return ++element;}
};

// class template specialization:
template <>
class mycontainer <char> {
    char element;
  public:
    mycontainer (char arg) {element=arg;}
    char uppercase ()
    {
      if ((element>='a')&&(element<='z'))
      element+='A'-'a';
      return element;
    }
};

int main () {
  mycontainer<int> myint (7);
  mycontainer<char> mychar ('j');
  cout << myint.increase() << endl;
  cout << mychar.uppercase() << endl;
  return 0;
}
```

This is the syntax used for the class template specialization:

```c++
template <> class mycontainer <char> { ... };
```

First of all, notice that we precede the class name with `template<>`, including an empty parameter list. This is because all types are known and no template arguments are required for this specialization, but still, it is the specialization of a class template, and thus it requires to be noted as such.

But more important than this prefix, is the `<char>` specialization parameter after the class template name. This specialization parameter itself identifies the type for which the template class is being specialized (`char`). Notice the differences between the generic class template and the specialization:

```c++
template <class T> class mycontainer { ... };
template <> class mycontainer <char> { ... };
```

The first line is the generic template, and the second one is the specialization.

When we declare specializations for a template class, we must also define all its members, even those identical to the generic template class, because there is no "inheritance" of members from the generic template to the specialization.

## References

1. [Classes (II)](http://www.cplusplus.com/doc/tutorial/templates/)
