## Part 1: void pointers

The *void* type of pointer is a special type of pointer. In C++, *void* represents the absence of type. Therefore, *void* pointers are pointers that point to a value that has no type (and thus also an undetermined length and undetermined dereferencing properties).

This gives *void* pointers a great flexibility, by being able to point to any data type, from an integer value or a float to a string of characters. In exchange, they have a great limitation: the data pointed to by them cannot be directly dereferenced (which is logical, since we have no type to dereference to), and for that reason, any address in a *void* pointer needs to be transformed into some other pointer type that points to a concrete data type before being dereferenced.

One of its possible uses may be to pass generic parameters to a function. For example: 

    ```c++
    #include <iostream>
    using namespace std;

    void increase (void* data, int psize)
    {
      if ( psize == sizeof(char) )
      { char* pchar; pchar=(char*)data; ++(*pchar); }
      else if (psize == sizeof(int) )
      { int* pint; pint=(int*)data; ++(*pint); }
    }

    int main ()
    {
      char a = 'x';
      int b = 1602;
      increase (&a,sizeof(a));
      increase (&b,sizeof(b));
      cout << a << ", " << b << '\n';
      return 0;
    }
    ```

*sizeof* is an operator integrated in the C++ language that returns the size in bytes of its argument. For non-dynamic data types, this value is a constant. Therefore, for example, *sizeof(char)* is 1, because *char* has always a size of one byte. 

## Part 2: null pointers

In principle, pointers are meant to point to valid addresses, such as the address of a variable or the address of an element in an array. But pointers can actually point to any address, including addresses that do not refer to any valid element. Typical examples of this are *uninitialized pointers* and pointers to nonexistent elements of an array:

    ```c++
    int * p;               // uninitialized pointer (local variable)

    int myarray[10];
    int * q = myarray+20;  // element out of bounds 
    ```

Neither p nor q point to addresses known to contain a value, but none of the above statements causes an error. In C++, pointers are allowed to take any address value, no matter whether there actually is something at that address or not. What can cause an error is to dereference such a pointer (i.e., actually accessing the value they point to). Accessing such a pointer causes undefined behavior, ranging from an error during runtime to accessing some random value.

But, sometimes, a pointer really needs to explicitly point to nowhere, and not just an invalid address. For such cases, there exists a special value that any pointer type can take: the *null pointer* value. This value can be expressed in C++ in two ways: either with an integer value of zero, or with the nullptr keyword:

    ```c++
    int * p = 0;
    int * q = nullptr;
    ```

Here, both p and q are null pointers, meaning that they explicitly point to nowhere, and they both actually compare equal: all null pointers compare equal to other null pointers. It is also quite usual to see the defined constant NULL be used in older code to refer to the null pointer value:

    ```c++
    int * r = NULL;
    ```

NULL is defined in several headers of the standard library, and is defined as an alias of some null pointer constant value (such as 0 or nullptr).

> Do not confuse null pointers with void pointers! A null pointer is a value that any pointer can take to represent that it is pointing to "nowhere", while a void pointer is a type of pointer that can point to somewhere without a specific type. One refers to the value stored in the pointer, and the other to the type of data it points to.
