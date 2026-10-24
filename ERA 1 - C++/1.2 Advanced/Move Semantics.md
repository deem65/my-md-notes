*Move semantics* lets a type initialize or assign an object by transferring appropriate state from another object.
Whether a transfer occurs, what it costs and what remains in the source depend on the type's move operation.
#### Value categories and references
```cpp
int value{42};
int& borrowed{value};
int&& temporary_reference{42};
```
An rvalue reference can bind to suitable rvalues. The expression naming `temporary_reference` is nevertheless an lvalue because it identifies that named object.
Move constructors commonly accept `T&&`; copy constructors commonly accept `const T&`.
#### std::move
```cpp
#include <memory>
#include <utility>
int main() {
    auto first{std::make_unique<int>(42)};
    auto second{std::move(first)};
}
```
`std::move(first)` casts the expression to an xvalue. It does not invoke cleanup, allocate memory or itself transfer ownership.
The selected `unique_ptr` move constructor transfers the managed pointer and leaves `first` empty.
If a suitable move is unavailable, an expression using `std::move` may still copy when copying is viable. Moving a const object often selects a copy because an ordinary `T&&` cannot bind to `const T`.
#### A movable owner
```cpp
#include <memory>
#include <utility>
class buffer {
public:
    explicit buffer(int count)
        : data_{std::make_unique<int[]>(count)} {}
    buffer(const buffer&) = delete;
    buffer& operator=(const buffer&) = delete;
    buffer(buffer&&) noexcept = default;
    buffer& operator=(buffer&&) noexcept = default;
private:
    std::unique_ptr<int[]> data_;
};
```
This illustrates explicit move-only policy. A class containing only appropriate RAII members can often follow the Rule of Zero and omit these declarations entirely.
Move assignment must account for any resource already owned by the destination. The member owner's assignment handles that cleanup here.
#### Moved-from state
A moved-from object still exists and must be safely destructible.
Standard-library moved-from objects generally have a valid but unspecified state unless a stronger guarantee is documented.
Do not assume every moved-from string is empty. `unique_ptr`, in contrast, has an explicit empty-source guarantee for ordinary moves.
User-defined types must document their own state and permitted subsequent operations.
#### noexcept
Containers may prefer copying elements over a potentially throwing move when preserving exception guarantees during reallocation.
Declare moves `noexcept` only when they truly cannot let exceptions escape. A false promise can lead to termination.
#### Copy elision
```cpp
buffer make_buffer() {
    return buffer{100};
}
```
C++17 directly constructs the result for this prvalue return. Returning a named local can also benefit from NRVO where permitted.
Adding `std::move` to a named local's return can prevent NRVO. Prefer `return local;` when returning that local normally.
#### Forwarding
In a deduced template parameter context, `T&&` can be a forwarding reference. `std::forward<T>` preserves the caller's relevant value category.
Use forwarding in generic wrappers only when preserving that category is the desired contract; it is not a replacement for ordinary reference parameters.
#### In Modest Clipper
Move owning buffers into queues when responsibility changes. Borrow them while inspecting. Share immutable data explicitly when overlapping exports require simultaneous ownership.
#### Related notes
- [[Value Categories unc]]
- [[Classes unf]]
- [[Smart Pointers]]
