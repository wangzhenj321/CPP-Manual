## Question

I'll start out by saying, use smart pointers and you'll never have to worry about this.

What are the problems with the following code?

```c++
Foo * p = new Foo;
// (use p)
delete p;
p = NULL;
```

This was sparked by an answer and comments to another question. One comment from Neil Butterworth generated a few upvotes:

> Setting pointers to NULL following delete is not universal good practice in C++. There are times when it is a good thing to do, and times when it is pointless and can hide errors.

There are plenty of circumstances where it wouldn't help. But in my experience, it can't hurt. Somebody enlighten me.

## Answer

Setting a pointer to 0 (which is "null" in standard C++, the NULL define from C is somewhat different) avoids crashes on double deletes.

Consider the following:

```c++
Foo* foo = 0; // Sets the pointer to 0 (C++ NULL)
delete foo; // Won't do anything
```

Whereas:

```c++
Foo* foo = new Foo();
delete foo; // Deletes the object
delete foo; // Undefined behavior 
```

In other words, if you don't set deleted pointers to 0, you will get into trouble if you're doing double deletes. An argument against setting pointers to 0 after delete would be that doing so just masks double delete bugs and leaves them unhandled.

It's best to not have double delete bugs, obviously, but depending on ownership semantics and object lifecycles, this can be hard to achieve in practice. **I prefer a masked double delete bug over UB.**

Finally, a sidenote regarding managing object allocation, I suggest you take a look at `std::unique_ptr` for strict/singular ownership, `std::shared_ptr` for shared ownership, or another smart pointer implementation, depending on your needs.

## References

1. [Is it good practice to NULL a pointer after deleting it?](https://stackoverflow.com/questions/1931126/is-it-good-practice-to-null-a-pointer-after-deleting-it)
