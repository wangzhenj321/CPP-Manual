## Friend functions

In principle, private and protected members of a class cannot be accessed from outside the same class in which they are declared. However, this rule does not apply to "friends".

---

**Friends are functions or classes declared with the `friend` keyword.**

---

A non-member function can access the private and protected members of a class if it is declared a friend of that class. That is done by including a declaration of this external function within the class, and preceding it with the keyword `friend`:

```c++
// friend functions
#include <iostream>
using namespace std;

class Rectangle {
    int width, height;
  public:
    Rectangle() {}
    Rectangle(int x, int y) : width(x), height(y) {}
    int area() {return width * height;}
    friend Rectangle duplicate(const Rectangle&);
};

Rectangle duplicate(const Rectangle& param)
{
    Rectangle res;
    res.width = param.width*2;
    res.height = param.height*2;
    return res;
}

int main () {
    Rectangle foo;
    Rectangle bar(2,3);
    foo = duplicate(bar);
    cout << foo.area() << '\n';
    return 0;
}
```

The `duplicate()` function is a friend of class `Rectangle`. Therefore, function `duplicate()` is able to access the members `width` and `height` (which are private) of different objects of type `Rectangle`. Notice though that neither in the declaration of `duplicate()` nor in its later use in `main()`, function `duplicate()` is considered a member of class `Rectangle`. It isn't! It simply has access to its private and protected members without being a member.

## Friend classes

Similar to friend functions, a friend class is a class whose members have access to the private or protected members of another class:

```c++
// friend class
#include <iostream>
using namespace std;

class Square;

class Rectangle {
    int width, height;
  public:
    int area() {return (width * height);}
    void convert(Square a);
};

class Square {
    friend class Rectangle;
    private:
        int side;
    public:
        Square(int a) : side(a) {}
};

void Rectangle::convert(Square a) {
    width = a.side;
    height = a.side;
}
  
int main () {
  Rectangle rect;
  Square sqr(4);
  rect.convert(sqr);
  cout << rect.area();
  return 0;
}
```

In this example, class `Rectangle` is a friend of class `Square` allowing `Rectangle`'s member functions to access private and protected members of `Square`. More concretely, `Rectangle` accesses the member variable `Square::side`, which describes the side of the square.

Friendships are never corresponded unless specified: In our example, `Rectangle` is considered a friend class by `Square`, but `Square` is not considered a friend by `Rectangle`. Therefore, the member functions of `Rectangle` can access the protected and private members of `Square` but not the other way around. Of course, `Square` could also be declared friend of `Rectangle`, if needed, granting such an access.

Another property of friendships is that they are not transitive: The friend of a friend is not considered a friend unless explicitly specified.

## References

1. [Friendship and inheritance](http://www.cplusplus.com/doc/tutorial/inheritance/)
