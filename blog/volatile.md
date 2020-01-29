https://stackoverflow.com/questions/12878344/volatile-in-c11

https://stackoverflow.com/questions/8819095/concurrency-atomic-and-volatile-in-c11-memory-model


## Answer 1

Consider this code,

```c++
int some_int = 100;

while(some_int == 100)
{
   //your code
}
```

When this program gets compiled, the compiler may optimize this code, if it finds that the program **never** ever makes any attempt to change the value of `some_int`, so it may be tempted to optimize the `while` loop by changing it from `while(some_int == 100)` to something which is equivalent to `while(true)` so that the execution could be fast (since the condition in `while` loop appears to be true always). (if the compiler doesn't optimize it, then it has to fetch the value of `some_int` and compare it with 100, in each iteration which obviously is a little bit slow.)

However, sometimes, optimization (of some parts of your program) may be **undesirable**, because it may be that someone else is changing the value of `some_int` from **outside the program which compiler is not aware of**, since it can't see it; but it's how you've designed it. In that case, compiler's optimization would **not** produce the desired result!

So, to ensure the desired result, you need to somehow stop the compiler from optimizing the `while` loop. That is where the `volatile` keyword plays its role. All you need to do is this,

```c++
volatile int some_int = 100; //note the 'volatile' qualifier now!
```

Quoting from the C++ Standard ($7.1.5.1/8)

> [..] volatile is a hint to the implementation to avoid aggressive optimization involving the object because the value of the object might be changed by means undetectable by an implementation.[...]

## Answer 2

`volatile` is needed if you are reading from a spot in memory that, say, a completely separate process/device/whatever may write to.

I used to work with dual-port ram in a multiprocessor system in straight C. We used a hardware managed 16 bit value as a semaphore to know when the other guy was done. Essentially we did this:

```c++
void waitForSemaphore()
{
   volatile uint16_t* semPtr = WELL_KNOWN_SEM_ADDR;/*well known address to my semaphore*/
   while ((*semPtr) != IS_OK_FOR_ME_TO_PROCEED);
}
```

Without `volatile`, the optimizer sees the loop as useless (The guy never sets the value! He's nuts, get rid of that code!) and my code would proceed without having acquired the semaphore, causing problems later on.

## References

1. [Why do we use volatile keyword?](https://stackoverflow.com/questions/4437527/why-do-we-use-volatile-keyword)
2. [Why does volatile exist?](https://stackoverflow.com/questions/72552/why-does-volatile-exist)
