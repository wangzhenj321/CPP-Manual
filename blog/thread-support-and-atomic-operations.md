## Part 1

- [Thread support library](https://en.cppreference.com/w/cpp/thread)
    - \<thread\>
        - **class thread;**
            - `(constructor)`
            - `(destructor)`
            - `operator=`
            - `joinable`
            - `join`
            - `detach`
        - **namespace this_thread**
            - `sleep_for`
    - \<mutex\>
        - **class mutex;**
            - `lock`
            - `try_lock`
            - `unlock`
        - **class timed_mutex;**
            - `try_lock_for`
            - `try_lock_until`
    - \<condition_variable\>
    - \<future\>
- [Atomic operations library](https://en.cppreference.com/w/cpp/atomic)
    - \<atomic\>

## Part 2

- [C++11 并发指南一(C++11 多线程初探)](http://www.cnblogs.com/haippy/p/3235560.html)

- [C++11 并发指南二(std::thread 详解)](http://www.cnblogs.com/haippy/p/3236136.html)

- [C++11 并发指南三(std::mutex 详解)](https://www.cnblogs.com/haippy/p/3237213.html)
