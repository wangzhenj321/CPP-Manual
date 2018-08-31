## Part 1: Pointers to functions

***References:*** http://www.cplusplus.com/doc/tutorial/pointers/

C++ allows operations with pointers to functions. The typical use of this is for passing a function as an argument to another function. Pointers to functions are declared with the same syntax as a regular function declaration, except that the name of the function is enclosed between parentheses () and an asterisk (*) is inserted before the name:

```c++
// pointer to functions
#include <iostream>
using namespace std;

int addition (int a, int b)
{ return (a+b); }

int subtraction (int a, int b)
{ return (a-b); }

int operation (int x, int y, int (*functocall)(int,int))
{
  int g;
  g = (*functocall)(x,y);
  return (g);
}

int main ()
{
  int m,n;
  int (*minus)(int,int) = subtraction;

  m = operation (7, 5, addition);
  n = operation (20, m, minus);
  cout <<n;
  return 0;
}
```

In the example above, `minus` is a pointer to a function that has two parameters of type `int`. It is directly initialized to point to the function subtraction:

```c++
int (* minus)(int,int) = subtraction;
```

## Part 2: Function pointer to member function

***References:*** https://stackoverflow.com/questions/2402579/function-pointer-to-member-function

The syntax is wrong. A member pointer is a different type category from a ordinary pointer. The member pointer will have to be used together with an object of its class:

```c++
class A {
public:
 int f();
 int (A::*x)(); // <- declare by saying what class it is a pointer to
};

int A::f() {
 return 1;
}


int main() {
 A a;
 a.x = &A::f; // use the :: syntax
 printf("%d\n",(a.*(a.x))()); // use together with an object of its class
}
```

`a.x` does not yet say on what object the function is to be called on. It just says that you want to use the pointer stored in the object `a`. Prepending `a` another time as the left operand to the `.*` operator will tell the compiler on what object to call the function on.
