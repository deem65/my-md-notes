A *lambda expression* creates an unnamed function object called a *closure*. It can store selected values from its surrounding context.
#### Basic syntax
```cpp
auto add{[](int a, int b) { return a + b; }};
int result{add(2, 3)};
```
The capture list appears in `[]`, parameters in `()`, and the body in `{}`. A trailing return type can be written when deduction is unsuitable.
#### Capturing by value
```cpp
int seconds{30};
auto duration{[seconds] { return seconds; }};
seconds = 60;
// duration() still returns 30
```
The closure holds its own captured value. It does not reread the original variable each time it runs.
Capturing a raw pointer by value copies the pointer, not the pointed-to object or its ownership.
#### Capturing by reference
```cpp
int saved{};
auto increment{[&saved] { ++saved; }};
increment();
```
The closure borrows `saved`. The variable must remain alive for every invocation, and concurrent accesses need appropriate synchronization.
Returning or storing such a closure can create a dangling reference if the captured local variable goes out of scope.
#### mutable
```cpp
auto counter{[count = 0]() mutable { return ++count; }};
```
`mutable` permits changing the closure's value-captured state through its call operator. It does not change the original variables that were copied and does not make the closure thread-safe.
#### Move capture
```cpp
#include <memory>
auto read{[owner = std::make_unique<int>(42)] { return *owner; }};
```
The closure owns the integer through its captured `unique_ptr`. This closure is movable but not copyable.
It therefore cannot be stored in ordinary `std::function`, whose target must be copyable.
#### Capturing an object
- `[this]` captures a pointer and borrows the current object.
- `[*this]` captures a copy of the object when copying is supported.
- `[=]` is not a guarantee that every referenced object is independently owned.
- Explicit captures make long-lived callbacks easier to review.
A callback registered by a recorder must not outlive the recorder merely because its closure object still exists.
#### Conversion to function pointers
A suitable captureless lambda can convert to an ordinary function pointer.
```cpp
int (*operation)(int){[](int value) { return value * 2; }};
```
A capturing lambda generally cannot, because its stored state needs an object. Check calling conventions when working with Windows callbacks.
#### Generic lambdas
```cpp
auto identity{[](auto value) { return value; }};
```
An `auto` parameter makes the call operator a template. Deduced return and parameter forms still have ordinary copy/reference implications.
For Clipper, use lambdas for short predicates and callbacks while keeping significant resource-management logic in named types or functions.
#### Related notes
- [[Function Pointers and Callbacks]]
- [[Smart Pointers]]
- [[Thread Lifetimes and Shutdown]]
