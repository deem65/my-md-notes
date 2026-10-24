A *callback* is an operation supplied to another part of a program so it can be invoked later or during an operation.
The callback mechanism does not determine when, how often, or on which thread it executes. Those are part of the caller's contract.
#### Function pointers
```cpp
int add(int a, int b) { return a + b; }
using operation = int (*)(int, int);
int main() {
    operation selected{&add};
    int result{selected(2, 3)};
}
```
`operation` is a pointer to a function accepting two integers and returning an integer.
The parentheses around `*` matter: `int* f(int)` instead declares a function returning a pointer.
A function name ordinarily converts to a function pointer where needed. A null function pointer cannot be called.
#### Passing behavior
```cpp
int calculate(int a, int b, operation action) {
    return action(a, b);
}
```
The function can invoke `action` without knowing which compatible function it represents. Decide whether null is forbidden or handled explicitly.
Function pointers do not contain arbitrary captured local state.
#### Function objects
A class with `operator()` can be invoked like a function.
```cpp
struct multiplier {
    int factor;
    int operator()(int value) const { return factor * value; }
};
multiplier double_it{2};
int answer{double_it(21)};
```
A lambda creates a closure object with a call operator; its captured data becomes part of that object.
#### std::function
```cpp
#include <functional>
int main() {
    int seconds{30};
    std::function<int()> duration{[seconds] { return seconds; }};
    int result{duration()};
}
```
`std::function<R(Args...)>` stores a compatible copyable callable behind a common type.
- It can be empty; calling an empty one throws `std::bad_function_call`.
- It may allocate and introduces indirect invocation overhead.
- A lambda owning a `unique_ptr` is not copyable and cannot be stored in ordinary `std::function`.
- C++23 offers `std::move_only_function` for move-only callables; the project can also use a concrete callable type.
#### Member functions
A pointer to a non-static member function needs an object as well as the member-function pointer.
```cpp
#include <functional>
struct recorder { void stop() {} };
recorder instance;
auto method{&recorder::stop};
// inside a function: std::invoke(method, instance);
```
Do not cast a member-function pointer into an ordinary C callback pointer. C APIs commonly supply a separate user-data pointer for state; otherwise use the API's supported routing mechanism.
#### Lifetime contract
A registry storing a lambda that captures `this` borrows the object. Unregistering the callback must account for callbacks already executing or queued.
Capture ownership deliberately and document exception handling, thread affinity and reentrancy.
For Clipper, one command can be invoked by a hotkey, Lua or the UI through the same dispatch layer.
#### Related notes
- [[Lambdas]]
- [[Commands and Events]]
- [[Thread Lifetimes and Shutdown]]
