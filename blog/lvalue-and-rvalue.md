# Part 1: Understanding the meaning of lvalues and rvalues in C++

I have been struggling with the concepts of **lvalue** and **rvalue** in C++ since forever. I think that now is the right time to understand them for good, as they are getting more and more important with the evolution of the language.

Once the meaning of lvalues and rvalues is grasped, you can dive deeper into advanced C++ features like **move semantics** and **rvalue references** (more on that in future articles).

## Lvalues and rvalues: a friendly definition

First of all, let's keep our heads away from any formal definition. In C++ an lvalue is something that points to a specific memory location. On the other hand, a rvalue is something that doesn't point anywhere. In general, rvalues are temporary and short lived, while lvalues live a longer life since they exist as variables. It's also fun to think of lvalues as containers and rvalues as things contained in the containers. Without a container, they would expire.

Let me show you some examples right away.

```c++
int x = 666;   // ok
```

Here `666` is an rvalue; a number (technically a literal constant) has no specific memory address, except for some temporary register while the program is running. That number is assigned to `x`, which is a variable. A variable has a specific memory location, so its an lvalue. C++ states that an assignment requires an lvalue as its left operand: this is perfectly legal.

Then with `x`, which is an lvalue, you can do stuff like that:

```c++
int* y = &x;   // ok
```

Here I'm grabbing the the memory address of `x` and putting it into `y`, through the address-of operator `&`. It takes an lvalue argument and produces an rvalue. This is another perfectly legal operation: on the left side of the assignment we have an lvalue (a variable), on the right side an rvalue produced by the address-of operator.

However, I can't do the following:

```c++
int y;
666 = y; // error!
```

Yeah, that's obvious. But the technical reason is that `666`, being a literal constant — so an rvalue, doesn't have a specific memory location. I am assigning `y` to nowhere.

This is what GCC tells me if I run the program above:

```
error: lvalue required as left operand of assignment
```

He is damn right; the left operand of an assigment always require an lvalue, and in my program I'm using an rvalue (`666`).

I can't do that too:

```c++
int* y = &666; // error!
```

GCC says:

```
error: lvalue required as unary '&' operand`
```

He is right again. The `&` operator wants an lvalue in input, because only an lvalue has an address that `&` can process.

## Functions returning lvalues and rvalues

We know that the left operand of an assigment must be an lvalue. Hence a function like the following one will surely throw the `lvalue required as left operand of assignment` error:

```c++
int setValue()
{
    return 6;
}

// ... somewhere in main() ...

setValue() = 3; // error!
```

Crystal clear: `setValue()` returns an rvalue (the temporary number `6`), which cannot be a left operand of assignment. Now, what happens if a function returns an lvalue instead? Look closely at the following snippet:

```c++
int global = 100;

int& setGlobal()
{
    return global;    
}

// ... somewhere in main() ...

setGlobal() = 400; // OK
```



## References

1. [Understanding the meaning of lvalues and rvalues in C++](https://www.internalpointers.com/post/understanding-meaning-lvalues-and-rvalues-c)
