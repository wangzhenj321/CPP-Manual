## static local variables

Variables declared at block scope with the specifier **static** have static storage duration but are initialized the first time control passes through their declaration (unless their initialization is **zero- or constant-initialization**, which can be performed before the block is first entered). On all further calls, the declaration is skipped.

If the initialization throws an exception, the variable is not considered to be initialized, and initialization will be attempted again the next time control passes through the declaration.

If the initialization recursively enters the block in which the variable is being initialized, the behavior is undefined.

If multiple threads attempt to initialize the same static local variable concurrently, the initialization occurs exactly once (similar behavior can be obtained for arbitrary functions with `std::call_once`). Note: usual implementations of this feature use variants of the double-checked locking pattern, which reduces runtime overhead for already-initialized local statics to a single non-atomic boolean comparison.

The destructor for a block-scope static variable is called at program exit, but only if the initialization took place successfully.

Function-local static objects in all definitions of the same inline function (which may be implicitly inline) all refer to the same object defined in one translation unit.

### References

1. [Storage class specifiers](https://en.cppreference.com/w/cpp/language/storage_duration)


## zero initialization

### Syntax

(1)
    ```c++
    static T object ;
    ```

(2)
    ```c++
    T () ;
    T t = {} ; 
    T {} ;
    ```

(3)
    ```c++
    char array [ n ] = "";
    ```

### Explanation

Zero initialization is performed in the following situations:

(1) For every named variable with static or thread-local storage duration that is not subject to constant initialization (since C++14), before any other initialization.

(2) As part of value-initialization sequence for non-class types and for members of value-initialized class types that have no constructors, including value initialization of elements of aggregates for which no initializers are provided.

(3) When a character array is initialized with a string literal that is too short, the remainder of the array is zero-initialized.

### References

1. [zero initialization](https://en.cppreference.com/w/cpp/language/zero_initialization)


## constant initialization

### References

1. [constant initialization](https://en.cppreference.com/w/cpp/language/constant_initialization)
