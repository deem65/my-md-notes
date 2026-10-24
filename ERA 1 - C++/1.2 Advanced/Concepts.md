# Concepts
A *concept* is a named compile-time constraint used to describe which template arguments are acceptable.
Concepts were introduced in C++20. They are useful for generic interfaces, but are optional for Clipper's first implementation.
#### Constraining a type
```cpp
#include <concepts>
template <std::integral T>
T twice(T value) {
    return value + value;
}
int main() {
    auto result{twice(5)};
    // twice(2.5); // rejected: double is not integral
}
```
Constraints participate in selecting viable templates. They can make an unsupported type fail at the interface instead of producing a long error from inside the implementation.
#### requires clauses
```cpp
template <typename T>
    requires std::integral<T>
T twice(T value) {
    return value + value;
}
```
This is an alternative declaration of the previous template, not an additional definition to put beside it.
Constrained `auto` parameters provide another compact syntax for function templates.
#### Defining a concept
```cpp
#include <concepts>
#include <cstddef>
template <typename T>
concept sized = requires(const T& value) {
    { value.size() } -> std::convertible_to<std::size_t>;
};
```
The `requires` expression checks whether the described expressions and type relationships are valid. It does not execute `size()` while checking the constraint.
Requirement expressions can test operations, nested types and result types.
#### Syntax versus meaning
A concept can check that `size()` exists and its result can convert to `size_t`. That alone cannot prove the value is correct, that the method is fast, or that concurrent calls are safe.
Semantic requirements still need documentation and appropriate testing.
Even the integral `twice` function can overflow a signed integer. A type constraint is not a runtime range check.
#### Standard concepts
- `std::integral` and `std::floating_point` describe numeric type categories.
- `std::same_as` checks a type relationship.
- `std::invocable` describes whether a callable can be invoked with given argument types.
- Range concepts describe capabilities such as iteration and contiguity.
Prefer an existing standard concept when it accurately describes the needed contract.
#### When to use one
Use concepts when designing a reusable generic helper with meaningful requirements. Ordinary functions accepting concrete types are often easier to understand for project-specific operations.
Avoid a broad generic capture abstraction before you understand the concrete DXGI and Media Foundation contracts it must preserve.
#### Related notes
- [[Templates unf]]
- [[Function Pointers and Callbacks]]
- [[Algorithms unf]]
