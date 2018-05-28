## Question

In C++, is there any difference between:

```c++
struct Foo { ... };
```

and

```c++
typedef struct { ... } Foo;
```

## Answer

In C++, there is only a subtle difference. It's a holdover from C, in which it makes a difference.

The C language standard mandates separate namespaces for different categories of identifiers, including ***tag identifiers*** (for `struct`/`union`/`enum`) and ***ordinary identifiers*** (for `typedef` and other identifiers).

If you just said:

```c++
struct Foo { ... };
Foo x;
```

you would get a compiler error, because `Foo` is only defined in the tag namespace.

You'd have to declare it as:

```c++
struct Foo x;
```

Any time you want to refer to a `Foo`, you'd always have to call it a `struct Foo`. This gets annoying fast, so you can add a `typedef`:

```c++
struct Foo { ... };
typedef struct Foo Foo;
```

Now both `struct Foo` (in the tag namespace) and just plain `Foo` (in the ordinary identifier namespace) both refer to the same thing, and you can freely declare objects of type `Foo` without the struct keyword.

The construct:

```c++
typedef struct Foo { ... } Foo;
```

is just an abbreviation for the declaration and typedef.

Finally,

```c++
typedef struct { ... } Foo;
```

declares an anonymous structure and creates a `typedef` for it. Thus, with this construct, it doesn't have a name in the tag namespace, only a name in the typedef namespace. This means it also cannot be forward-declared. If you want to make a forward declaration, you have to give it a name in the tag namespace.

---

In C++, all `struct`/`union`/`enum`/`class` declarations act like they are implicitly `typedef`'ed, as long as the name is not hidden by another declaration with the same name. See Michael Burr's answer for the full details.

## References

1. [Difference between 'struct' and 'typedef struct' in C++?](https://stackoverflow.com/questions/612328/difference-between-struct-and-typedef-struct-in-c)
