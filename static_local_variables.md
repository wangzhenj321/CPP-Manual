***References:*** https://en.cppreference.com/w/cpp/language/storage_duration

Variables declared at block scope with the specifier **static** have static storage duration but are initialized the first time control passes through their declaration (unless their initialization is zero- or constant-initialization, which can be performed before the block is first entered). On all further calls, the declaration is skipped.

If the initialization throws an exception, the variable is not considered to be initialized, and initialization will be attempted again the next time control passes through the declaration.

If the initialization recursively enters the block in which the variable is being initialized, the behavior is undefined.

If multiple threads attempt to initialize the same static local variable concurrently, the initialization occurs exactly once (similar behavior can be obtained for arbitrary functions with std::call_once).

Note: usual implementations of this feature use variants of the double-checked locking pattern, which reduces runtime overhead for already-initialized local statics to a single non-atomic boolean comparison.	(since C++11)
The destructor for a block-scope static variable is called at program exit, but only if the initialization took place successfully.

Function-local static objects in all definitions of the same inline function (which may be implicitly inline) all refer to the same object defined in one translation unit.
