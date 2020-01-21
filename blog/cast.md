- `static_cast`
- `const_cast`
- `dynamic_cast`
- `reinterpret_cast`
- C-style cast `(type)value`
- Function-style cast `type(value)`

# Part 1

- **`static_cast`** is the first cast you should attempt to use. It does things like implicit conversions between types (such as `int` to `float`, or pointer to `void*`), and it can also call explicit conversion functions (or implicit ones). In many cases, explicitly stating `static_cast` isn't necessary, but it's important to note that the `T(something)` syntax is equivalent to `(T)something` and should be avoided (more on that later). A `T(something, something_else)` is safe, however, and guaranteed to call the constructor.

    `static_cast` can also cast through inheritance hierarchies. It is unnecessary when casting upwards (towards a base class), but when casting downwards it can be used as long as it doesn't cast through virtual inheritance. It does not do checking, however, and it is undefined behavior to `static_cast` down a hierarchy to a type that isn't actually the type of the object.
    
    ---

- **`const_cast`** can be used to remove or add `const` to a variable; no other C++ cast is capable of removing it (not even `reinterpret_cast`). It is important to note that modifying a formerly `const` value is only undefined if the original variable is `const`; if you use it to take the `const` off a reference to something that wasn't declared with `const`, it is safe. This can be useful when overloading member functions based on `const`, for instance. It can also be used to add `const` to an object, such as to call a member function overload.

    `const_cast` also works similarly on `volatile`, though that's less common.
    
    ---

- **`dynamic_cast`** is exclusively used for handling polymorphism. You can cast a pointer or reference to any polymorphic type to any other class type (a polymorphic type has at least one virtual function, declared or inherited). You can use it for more than just casting downwards – you can cast sideways or even up another chain. The `dynamic_cast` will seek out the desired object and return it if possible. If it can't, it will return `nullptr` in the case of a pointer, or throw `std::bad_cast` in the case of a reference.

    `dynamic_cast` has some limitations, though. It doesn't work if there are multiple objects of the same type in the inheritance hierarchy (the so-called 'dreaded diamond') and you aren't using `virtual` inheritance. It also can only go through `public` inheritance - it will always fail to travel through `protected` or `private` inheritance. This is rarely an issue, however, as such forms of inheritance are rare.
    
    ---

- `reinterpret_cast` is the most dangerous cast, and should be used very sparingly. It turns one type directly into another — such as casting the value from one pointer to another, or storing a pointer in an int, or all sorts of other nasty things. Largely, the only guarantee you get with `reinterpret_cast` is that normally if you cast the result back to the original type, you will get the exact same value (but not if the intermediate type is smaller than the original type). There are a number of conversions that `reinterpret_cast` cannot do, too. It's used primarily for particularly weird conversions and bit manipulations, like turning a raw data stream into actual data, or storing data in the low bits of a pointer to aligned data.

    ---

- C-style cast and function-style cast are casts using `(type)object` or `type(object)`, respectively, and are functionally equivalent. They are defined as the first of the following which succeeds:

    - `const_cast`
    - `static_cast` (though ignoring access restrictions)
    - `static_cast` (see above), then `const_cast`
    - `reinterpret_cast`
    - `reinterpret_cast`, then `const_cast`

    It can therefore be used as a replacement for other casts in some instances, but can be extremely dangerous because of the ability to devolve into a `reinterpret_cast`, and the latter should be preferred when explicit casting is needed, unless you are sure `static_cast` will succeed or `reinterpret_cast` will fail. Even then, consider the longer, more explicit option.

    C-style casts also ignore access control when performing a `static_cast`, which means that they have the ability to perform an operation that no other cast can. This is mostly a kludge, though, and in my mind is just another reason to avoid C-style casts.

## References

1. [When should static_cast, dynamic_cast, const_cast and reinterpret_cast be used?](https://stackoverflow.com/questions/332030/when-should-static-cast-dynamic-cast-const-cast-and-reinterpret-cast-be-used)

# Part 2

- **`static_cast`**

    `static_cast` is used for cases where you basically want to reverse an implicit conversion, with a few restrictions and additions. `static_cast` performs no runtime checks. This should be used if you know that you refer to an object of a specific type, and thus a check would be unnecessary. Example:

    ```cpp
    void func(void *data) {
    // Conversion from MyClass* -> void* is implicit
    MyClass *c = static_cast<MyClass*>(data);
    ...
    }

    int main() {
    MyClass c;
    start_thread(&func, &c)  // func(&c) will be called
        .join();
    }
    ```

    In this example, you know that you passed a `MyClass` object, and thus there isn't any need for a runtime check to ensure this.

- **`dynamic_cast`**

    `dynamic_cast` is useful when you don't know what the dynamic type of the object is. It returns a null pointer if the object referred to doesn't contain the type casted to as a base class (when you cast to a reference, a `bad_cast` exception is thrown in that case).

    ```cpp
    if (JumpStm *j = dynamic_cast<JumpStm*>(&stm)) {
    ...
    } else if (ExprStm *e = dynamic_cast<ExprStm*>(&stm)) {
    ...
    }
    ```

    You cannot use `dynamic_cast` if you downcast (cast to a derived class) and the argument type is not polymorphic. For example, the following code is not valid, because `Base` doesn't contain any `virtual` function:

    ```cpp
    struct Base { };
    struct Derived : Base { };
    int main() {
    Derived d; Base *b = &d;
    dynamic_cast<Derived*>(b); // Invalid
    }
    ```

    An "up-cast" (cast to the base class) is always valid with both `static_cast` and `dynamic_cast`, and also without any cast, as an "up-cast" is an implicit conversion.

- **`Regular Cast`**

    These casts are also called C-style cast. A C-style cast is basically identical to trying out a range of sequences of C++ casts, and taking the first C++ cast that works, without ever considering `dynamic_cast`. Needless to say, this is much more powerful as it combines all of `const_cast`, `static_cast` and `reinterpret_cast`, but it's also unsafe, because it does not use `dynamic_cast`.

    In addition, C-style casts not only allow you to do this, but they also allow you to safely cast to a private base-class, while the "equivalent" `static_cast` sequence would give you a compile-time error for that.

    Some people prefer C-style casts because of their brevity. I use them for numeric casts only, and use the appropriate C++ casts when user defined types are involved, as they provide stricter checking.

## References

1. [Regular cast vs. static_cast vs. dynamic_cast](https://stackoverflow.com/questions/28002/regular-cast-vs-static-cast-vs-dynamic-cast)
