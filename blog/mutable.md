**`mutable`**: permits modification of the class member declared mutable even if the containing object is declared const.

```c++
class ThreadsafeCounter {
    mutable std::mutex m; // The "M&M rule": mutable and mutex go together
    int data = 0;
 public:
    int get() const {
        std::lock_guard<std::mutex> lk(m);
        return data;
    }
    void inc() {
        std::lock_guard<std::mutex> lk(m);
        ++data;
    }
};
```
