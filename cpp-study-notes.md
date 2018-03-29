## Basics of C++
1. `using namespace std;` (All the elements in the standard C++ library are declared within what is called a *namespace*: the namespace `std`.)
2. `auto`
3. `decltype`
4. `int a=5;` (c-like initialization)
5. `int b(3);` (constructor initialization)
6. `int c{2};` (uniform initialization)
7. `#include <string>`
8. `75 // decimal` or `0113 // octal` or `0x4b // hexadecimal`
9. `75 // int` or `75u // unsigned int` or `75l // long` or `75ul // unsigned long` or `75lu // unsigned long` 
10.  `u // char16_t` or `U // char32_t` or `L // wchar_t`
11. `R"&%$(string with \backslash)&%$"`
12. `nullptr`
13. `const double pi = 3.1415926;`
14. `#define identifier replacement` (replacement is any sequence of characters until the end of the line)
15. `a = (b=3, b+2);` (comma operator)
16. bitwise operators
17. `x = sizeof (char);`
18. `\n` vs `endl`
19. `cin >> a >> b;`
20. `getline(cin, str);`
21. `#include <sstream>` => `stringstream`

## Program structure
1. `for ( declaration : range ) statement;`
2. `void duplicate (int& a, int& b, int& c)`
3. `string concatenate (const string& a, const string& b)`
4. `inline string concatenate (const string& a, const string& b)`
5. `int divide (int a, int b=2)`
6. `void odd (int x);`
7. `template <class SomeType> SomeType sum (SomeType a, SomeType b)`
8. `x = sum<int>(10,20);`
9. `template <class T, int N> T fixed_multiply (T val)`
10. An inner block, because it is a different block, can re-utilize a name existing in an outer scope to refer to a different entity.
11. `myNamespace::a` (scope operator ::) (namespaces can be split)
12. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQ2MTU2NTAwOF19
-->