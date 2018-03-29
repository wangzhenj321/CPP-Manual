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
12. `using first::x;` vs `using namespace first;`
13. All the entities (variables, types, constants, and functions) of the standard C++ library are declared within the std namespace.
14. global variable (static storage) (Variables with static storage (such as global variables) that are not explicitly initialized are automatically initialized to zeroes.)
15. local variable (automatic storage) (Variables with automatic storage (such as local variables) that are not explicitly initialized are left uninitialized, and thus have an undetermined value.)

## Compound data types
1. `int bar [5] = { 10, 20, 30 };` (initialize arrays) (Arrays cannot be assigned values.)
2. `int jimmy [3][5];` (multidimensional arrays)
3. `void procedure (int arg[])`
4. `void procedure (int myarray[][3][4])`
5. `#include <array>` `array<int,3> myarray {10,20,30};`
6. By convention, the end of strings represented in character sequences is signaled by a special character: the null character, whose literal value can be written as '\0' (backslash, zero).
7. `char myword[] = { 'H', 'e', 'l', 'l', 'o', '\0' };` or `char myword[] = "Hello";`
8. `cout << mystring.c_str();`
9. `foo = &myvar;` (address-of operator)
10. `baz = *foo;` (dereference operator)
11. `int * p1, * p2;`
12. The main difference being that **mypointer** can be assigned a different address, whereas **myarray** can never be assigned anything.
13. `a[5] = 0;` vs `*(a+5) = 0;`
14. Adding one to a pointer, the pointer is made to point to the following element of the same type, and, therefore, the size in bytes of the type it points to is added to the pointer.
15. `const int * current = start;` (These pointers point to constant content they cannot modify, but they are not constant themselves: i.e., the pointers can still be incremented or assigned different addresses, although they cannot modify the content they point to.)
16. `int x;`
	- `int * p1 = &x;  // non-const pointer to non-const int`
	- `const int * p2 = &x;  // non-const pointer to const int`
	-  `int * const p3 = &x;  // const pointer to non-const int`
	- `const int * const p4 = &x;  // const pointer to const int`
	- `int const * p2b = &x;  // also non-const pointer to const int`
17. `char ** c;`
18. `void increase (void* data, int psize)` (void pointers)
19. `int * p = 0;` vs `int * q = nullptr;`
20. Do not confuse **null pointers** with **void pointers**! A null pointer is a value that any pointer can take to represent that it is pointing to "nowhere", while a void pointer is a type of pointer that can point to somewhere without a specific type. One refers to the value stored in the pointer, and the other to the type of data it points to.
21. `int operation (int x, int y, int (*functocall)(int,int))` (pointers to functions)
22. `pointer = new type` and `pointer = new type [number_of_elements]` (dynamic memory)
23. `foo = new (nothrow) int [5];` (`#include <new>`) (When a memory allocation fails, instead of throwing a **bad_alloc** exception or terminating the program, the pointer returned by new is a null pointer, and the program continues its execution normally.)
24. `delete pointer;` vs `delete[] pointer;`
25. ```c++
	struct type_name {
		member_type1 member_name1;
		member_type2 member_name2;
		member_type3 member_name3;
		...
	} object_names;
	```
26. `getline (cin, pmovie->title);` (arrow operator) (`(*pmovie).title`)
27. `typedef existing_type new_type_name;` vs `using new_type_name = existing_type;` (**type aliases**)
28. ```c++
	union type_name {
		member_type1 member_name1;
		member_type2 member_name2;
		member_type3 member_name3;
		...
	} object_names;
	```
29. ```c++
	// structure with anonymous union
	struct book2_t {
		char title[50];
		char author[50];
		union {
			float dollars;
			int yen;
		};
	} book2;
	```
30. ```c++
	// enumerated types (enum)
	enum type_name {
		value1,
		value2,
		value3,
		...
	} object_names;
	```
31. `enum class EyeColor : char {blue, green, brown}; ` (enumerated types with enum class)

## Classes
1. ```c++
	class class_name {
		access_specifier_1:
			member1;
		access_specifier_2:
			member2;
		...
	} object_names;
	```
2. - **private** members of a class are accessible only from within other members of the same class (or from their **friends**)
	- **protected** members are accessible from other members of the same class (or from their **friends**), but also from members of their derived classes
	- **public** members are accessible from anywhere where the object is visible
	
	By default, all members of a class declared with the class keyword have **private** access for all its members.
3. ```c++
	class Rectangle {
		int width, height;
		public:
			void set_values (int,int);
			int area() {return width*height;}
	};
	
	void Rectangle::set_values (int x, int y) {
		width = x;
		height = y;
	}
	```
4. ```c++
	Rectangle::Rectangle () {
		width = 5;
		height = 5;
	}
	
	Rectangle rectb;	// default constructor
	```
5. ```c++
	  Circle foo (10.0);   // functional form
	  Circle bar = 20.0;   // assignment init
	  Circle baz {30.0};   // uniform init
	  Circle qux = {40.0}; // POD-like
	```
6. `Rectangle::Rectangle (int x, int y) : width(x) { height=y; }` (member initialization)
7. Classes can be defined not only with keyword **class**, but also with keywords **struct** and **union**.
8. **overloadable operators**
	```c++
	CVector CVector::operator+ (const CVector& param) {
		CVector temp;
		temp.x = x + param.x;
		temp.y = y + param.y;
		return temp;
	}
	```
9. ```c++
	int get() const {return x;}        // const member function
	const int& get() {return x;}       // member function returning a const&
	const int& get() const {return x;} // const member function returning a const& 
	```
10. **class templates**
	```c++
	template <class T>
	T mypair<T>::getmax ()
	{
		T retval;
		retval = a>b? a : b;
		return retval;
	}
	```
11. **class template specialization**
12. 
		
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIzNTY1ODY2MV19
-->