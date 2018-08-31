***References:*** https://stackoverflow.com/questions/1452721/why-is-using-namespace-std-considered-bad-practice

## Question

I've been told by others that writing `using namespace std` in code is wrong, and that I should use `std::cout` and `std::cin` directly instead.

Why is `using namespace std` considered a bad practice? Is it inefficient or does it risk declaring ambiguous variables (variables that share the same name as a function in `std` namespace)? Does it impact performance?

## Answer

This is not related to performance at all. But consider this: you are using two libraries called Foo and Bar:

```c++
using namespace foo;
using namespace bar;
```

Everything works fine, you can call `Blah()` from Foo and `Quux()` from Bar without problems. But one day you upgrade to a new version of Foo 2.0, which now offers a function called Quux(). Now you've got a conflict: Both Foo 2.0 and Bar import `Quux()` into your global namespace. This is going to take some effort to fix, especially if the function parameters happen to match.

If you had used `foo::Blah()` and `bar::Quux()`, then the introduction of `foo::Quux()` would have been a non-event.
