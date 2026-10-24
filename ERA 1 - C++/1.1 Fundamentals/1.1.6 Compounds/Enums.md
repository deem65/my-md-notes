An *enumeration* is a user-defined compound type that consists of a fixed set of named integral constants, called *enumerators*.
Enumerations are useful when an object represents one of several related states, categories, or options.
- C++ provides two kinds of enumerations:
  - Unscoped enumerations (`enum`).
  - Scoped enumerations (`enum class` or `enum struct`).
- Each enumerator has an associated integral value.
- Every enumeration has an *underlying type* used to represent its values.
- Enumerations improve readability by replacing otherwise unexplained numeric constants with meaningful names.
#### Unscoped enumerations
An *unscoped enumeration* is declared using the `enum` keyword.
```cpp
enum Color {
    red,
    green,
    blue
};
int main() {
    Color color{green};
}
```
- `Color` is the enumeration type.
- `red`, `green`, and `blue` are enumerators.
- By default, the first enumerator has the value `0`, and each subsequent enumerator has a value one greater than the previous one.
- The enumerators are introduced into the scope containing the enumeration.
- Unscoped enumerators can implicitly convert to integral types.
Enumerator values can also be explicitly specified:
```cpp
enum Status {
    idle = 0,
    running = 5,
    paused,
    stopped = 10
};
```
- `idle` has the value `0`.
- `running` has the value `5`.
- `paused` has the value `6`.
- `stopped` has the value `10`.
- When an enumerator has no explicit initializer, its value is one greater than the preceding enumerator.
#### Scoped enumerations
A *scoped enumeration* is declared using `enum class` or `enum struct`.
Scoped enumerations were introduced in C++11 to provide stronger type safety and avoid naming conflicts.
```cpp
enum class Color {
    red,
    green,
    blue
};
int main() {
    Color color{Color::green};
}
```
- Enumerators belong to the scope of the enumeration.
- They are accessed using the scope resolution operator `::`.
- Scoped enumerations do not implicitly convert to integers.
- Scoped enumerations do not implicitly convert to unrelated enumeration types.
- Prefer scoped enumerations for most new C++ code.
For example, two scoped enumerations can contain enumerators with identical names:
```cpp
enum class PlayerState {
    idle,
    running
};
enum class EnemyState {
    idle,
    attacking
};
int main() {
    PlayerState player{PlayerState::idle};
    EnemyState enemy{EnemyState::idle};
}
```
With unscoped enumerations declared in the same scope, the duplicate `idle` name would cause a naming conflict.
#### Unscoped vs scoped enumerations
The main differences between `enum` and `enum class` are their scoping and conversion rules.
For example:
```cpp
enum Direction {
    north,
    south
};
enum class State {
    idle,
    active
};
int main() {
    int a{north}; // valid
    // int b{State::idle}; // error
    int c{static_cast<int>(State::idle)}; // valid
}
```
- Unscoped enumerators can implicitly convert to integers.
- Scoped enumerators require an explicit cast when converting to an integer.
- Neither enumeration type is interchangeable with an unrelated enumeration type without an appropriate explicit conversion.
#### Underlying types
Every enumeration has an *underlying type*, which is an integral type used to represent its enumerator values.
By default:
- Unscoped enumerations without a fixed underlying type use an implementation-selected integral type capable of representing their enumerator values.
- Scoped enumerations use `int` unless another underlying type is specified.
An underlying type can be explicitly declared using a colon:
```cpp
#include <cstdint>
enum class PacketType : std::uint8_t {
    connect,
    disconnect,
    message
};
```
- `std::uint8_t` specifies an unsigned integer type with exactly 8 bits, where supported.
- Explicit underlying types are useful when an API or binary representation requires a particular integer size.
- The underlying type determines the enumeration's size and range of representable values.
- Specifying an underlying type does not automatically make serialization or network communication portable across platforms.
Since C++23, `std::to_underlying()` can convert an enumeration value to its underlying integer type:
```cpp
#include <cstdint>
#include <utility>
enum class PacketType : std::uint8_t {
    connect = 1,
    disconnect = 2
};
int main() {
    auto value{std::to_underlying(PacketType::connect)};
}
```
Before C++23, `static_cast` can be used instead.
#### Enumerator values
Enumerators are named constant values belonging to an enumeration type.
```cpp
enum class Priority {
    low = 1,
    normal = 5,
    high = 10
};
```
- Enumerator values must be constant expressions representable by the underlying type.
- Enumerators do not need to have consecutive values.
- Multiple enumerators may have the same underlying value.
```cpp
enum class Result {
    success = 0,
    ok = 0,
    failure = 1
};
```
Here, `success` and `ok` represent the same underlying value.
- Enumerators are constants, not variables.
- Enumerator values cannot be modified.
- Variables of an enumeration type can be reassigned to another enumeration value.
```cpp
Result result{Result::success};
result = Result::failure;
```
#### Enumerations and switch statements
Enumerations are commonly used with `switch` statements to handle different states or categories.
```cpp
#include <iostream>
enum class CaptureState {
    stopped,
    recording,
    paused
};
void print_state(CaptureState state) {
    switch (state) {
    case CaptureState::stopped:
        std::cout << "Stopped\n";
        break;
    case CaptureState::recording:
        std::cout << "Recording\n";
        break;
    case CaptureState::paused:
        std::cout << "Paused\n";
        break;
    }
}
```
- Each `case` label matches an enumeration value.
- The compiler can sometimes warn when an enumerator is not handled.
- Avoid an unnecessary `default` branch when you want compiler warnings about newly added enumerators, provided your compiler supports such warnings.
- Enumeration values can be compared using equality and inequality operators.
#### Enumeration conversions
An unscoped enumeration can implicitly convert to an integral type, whereas a scoped enumeration generally requires an explicit conversion.
```cpp
enum class Mode {
    automatic = 0,
    manual = 1
};
int main() {
    Mode mode{Mode::manual};
    int value{static_cast<int>(mode)};
}
```
It is also possible to explicitly convert an integer to an enumeration type:
```cpp
Mode mode{static_cast<Mode>(1)};
```
However, such conversions require care.
- A cast does not verify that the resulting value corresponds to a named enumerator.
- For an enumeration with a fixed underlying type, values representable by that underlying type can be converted to the enumeration type.
- Out-of-range conversions to enumerations without a fixed underlying type can cause undefined behavior.
- Validate external integer values before converting them to an enumeration when only certain named values are acceptable.
For example:
```cpp
Mode mode{static_cast<Mode>(42)}; // valid conversion, but no named enumerator has this value
```
**Important:** An enumeration defines named constants, but it does not necessarily restrict a variable to those named values.
#### Enumerations as bit flags
Enumerations can also represent independent flags rather than mutually exclusive states.
For example:
```cpp
enum class Permission : unsigned int {
    read = 1u << 0,
    write = 1u << 1,
    execute = 1u << 2
};
```
Each enumerator represents a different bit.
Unlike unscoped enumerations, scoped enumerations do not provide implicit integer conversions, so bitwise combinations require explicit conversions or overloaded operators.
```cpp
unsigned int permissions{
    static_cast<unsigned int>(Permission::read) |
    static_cast<unsigned int>(Permission::write)
};
```
- Bit flags allow multiple options to be represented simultaneously.
- Flags are usually assigned distinct powers of two.
- The bitwise OR operator (`|`) combines flags.
- The bitwise AND operator (`&`) checks whether selected bits are set.
- For mutually exclusive states, ordinary enumeration values are generally clearer than bit flags.
#### Best practices
- Prefer `enum class` over unscoped `enum` for new C++ code.
- Use descriptive type and enumerator names.
- Use enumerations for related states, options, and categories.
- Do not use enumerations for arbitrary numeric quantities such as health, frame count, or bitrate.
- Explicitly specify the underlying type when binary representation or API compatibility requires it.
- Avoid unnecessary conversions between enumeration types and integers.
- Validate external numeric values before converting them to an enumeration.
- Prefer `switch` statements when handling multiple distinct enumeration values.
- Use enumerations for bit flags only when representing combinations of independent options is intentional.