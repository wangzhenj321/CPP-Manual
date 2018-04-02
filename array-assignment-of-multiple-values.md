## Question

so when you initialize an array, you can assign multiple values to it in one spot:

```c++
int array [] = {1,3,34,5,6}
```

but what if the array is already initialized and I want to completely replace the values of the elements in that array in one line

```c++
int array [] = {1,3,34,5,6}
array [] = {34,2,4,5,6}
```

doesn't seem to work...

is there a way to do so?

## Answer

There is a difference between **initialization** and **assignment**. What you want to do is not initialization, but assignment. But such assignment to array is not possible in C++.

Here is what you can do:

```c++
#include <algorithm>

int array [] = {1,3,34,5,6};
int newarr [] = {34,2,4,5,6};
std::copy(newarr, newarr + 5, array);
```

However, in C++0x, you can do this:

```c++
std::vector<int> array = {1,3,34,5,6};
array = {34,2,4,5,6};
```

Of course, if you choose to use `std::vector` instead of raw array.

## References

1. [array assignment of multiple values](https://stackoverflow.com/questions/5732798/c-array-assignment-of-multiple-values)
