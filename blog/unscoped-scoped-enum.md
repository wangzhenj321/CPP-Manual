## Unscoped enum (Standard enum)

The following example shows the creation and usage of a unscoped enumerator.

```c++
namespace MyNamespace
{
  enum Colors
  {
    Red,
    Green,
    Blue
  };
}
 
using namespace MyNamespace;
 
int _tmain(int argc, _TCHAR* argv[])
{
  Colors color = Red;
  int enumValue = Colors::Red;
 
  enumValue = Red + Blue;
 
  return 0;
}
```

A second issue can be seen in case we add another enumerator. Enumerator names are in the same scope as the enum definition. Therefore "Red", "Green" and "Blue" are within the "MyNamespace" scope. **The following source code shows what happens if we add another enumerator which contains an already used name: a name collision will occur and the compiler reports an error.**

```c++
namespace MyNamespace
{
  enum Colors
  {
    Red,
    Green,
    Blue
  };
 
  enum OtherColors
  {
    Yellow,
    Blue,   // error
  };
}
 
using namespace MyNamespace;
 
int _tmain(int argc, _TCHAR* argv[])
{
    return 0;
}
```

That's why the standard enumerator is also called unscoped. The values of an enumeration exist in whatever scope it was declared in.

## Scoped enum (Strong enum)

C++ offers a second kind of enumerators. This strong enum can be declared by writing "enum class". In contrast to the standard enum the strong one is scoped. Therefore you don't have to fear conflicts if you use the same names for enumerator values. The following code shows the adapted example and this time we can add both enums to one namespace.

```c++
namespace MyNamespace
{
  enum class Colors
  {
    Red,
    Green,
    Blue
  };
 
  enum class OtherColors
  {
    Yellow,
    Blue,
  };
}
 
using namespace MyNamespace;
 
int _tmain(int argc, _TCHAR* argv[])
{
  Colors color = Colors::Blue;
  OtherColors otherColors = OtherColors::Blue;
 
  int enumValue = Colors::Red;  // error
 
  return 0;
}
```

Furthermore the main method shows another nice concept of the strong enumerator. **It is strongly typed and therefore you cannot assign its values to an integer variable.**
