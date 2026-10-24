Every expression has a type and a *value category*. The category describes aspects of identity and value computation that affect reference binding, overload resolution and object construction.
It does not simply describe whether an expression is a valid value or which side of `=` it appears on.
#### The three primary categories
| Category | Meaning | Typical example |
|---|---|---|
| `lvalue` | Identifies an entity, without being an xvalue | A variable name such as `count` |
| `prvalue` | Computes a value or initializes a result object | `42`, `a + b`, a function returning an int by value |
| `xvalue` | Identifies an object whose resources may be reused | `std::move(object)` |
Every expression belongs to exactly one of these primary categories.
- A *glvalue* is an lvalue or xvalue.
- An *rvalue* is a prvalue or xvalue.
- Move semantics is a use of these rules, not another category.
#### Identity and constness
```cpp
int count{10};
const int limit{20};
```
The expressions `count` and `limit` are both lvalues. `count` is modifiable; `limit` is not.
An lvalue can appear on the right side of an assignment:
```cpp
count = limit;
```
Where a value is needed, an appropriate lvalue-to-rvalue conversion reads the stored value. Operators have their own operand requirements; they do not all require rvalues.
#### References
```cpp
int value{10};
int& a{value};
const int& b{42};
int&& c{42};
// int& bad{42}; // non-const lvalue reference cannot bind this way
```
`const T&` can bind to many lvalues and rvalues. `T&&` normally binds to suitable rvalues.
The expressions `a`, `b` and `c` are lvalues because these are names of references used to identify their referents. A variable declared with an rvalue-reference type is not automatically an rvalue whenever named.
#### std::move
```cpp
#include <string>
#include <utility>
std::string first{"clip"};
std::string second{std::move(first)};
```
`std::move(first)` is an xvalue. It lets overload resolution select a move constructor when appropriate. The cast itself does not transfer the string's resource.
#### Function return categories
- A call returning `T` by value is ordinarily a prvalue.
- A call returning `T&` is an lvalue.
- A call returning `T&&` to an object is an xvalue.
Returning a reference never makes a destroyed local object valid again. Value category and lifetime are related but distinct questions.
#### Temporary lifetime
Binding a local reference directly to a suitable temporary can extend that temporary's lifetime according to specific rules.
Passing a temporary through another reference-returning function does not generally transfer lifetime extension to a stored result.
For stored views, callbacks and API buffers, identify the actual owner instead of assuming a reference keeps the data alive.
#### Related notes
- [[References]]
- [[Move Semantics]]
- [[Return]]
#### References
- [C++ working draft: value categories](https://eel.is/c++draft/basic.lval).
