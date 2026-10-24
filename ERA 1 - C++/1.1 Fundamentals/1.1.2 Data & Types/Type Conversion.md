A *type conversion* produces a value or expression of one type from an expression of another type.
For example, converting an `int` to a `double` allows its value to be used as a floating-point number.
```cpp
int count{5};
double result = count; // converts 5 to 5.0
```

- `count` remains an `int`.
- `result` is a separate `double` object.
- A conversion does not change the declared type of an existing variable.
- Conversions may preserve the original value, lose information, or be invalid.
- The examples use C++17 or later; version-specific differences are identified where relevant.
#### Implicit conversions
An *implicit conversion* is a conversion performed without an explicit cast.
```cpp
double get_value() {
    return 5; // int to double
}

void accept_value(double value) {
    // use value
}

int main() {
    double value = 10; // initialization: int to double
    value = 20;       // assignment: int to double
    accept_value(30); // argument: int to double
}
```

- The required type can come from a variable, parameter, return type, or operator.
- A conversion being implicit does not guarantee that it preserves the value.
- An unsupported implicit conversion causes a compilation error.

#### Numeric promotions
A *numeric promotion* is one of a specific set of conversions that preserve the value while converting to another numeric type.
There are two groups: integral promotions and floating-point promotion.
```cpp
short small{10};
int number{small}; // integral promotion

bool enabled{true};
int flag{enabled}; // integral promotion: true becomes 1

float fraction{1.5f};
double precise{fraction}; // floating-point promotion
```

- Smaller integer types commonly promote to `int`.
- For types covered by the ordinary integral promotion rule, if `int` cannot represent every source value, the promotion is to `unsigned int` instead.
- `bool` promotes to `int`: `false` becomes `0`, and `true` becomes `1`.
- `float` promotes to `double` without changing its value.
- Not every conversion to a larger type is a promotion. For example, `int` to `long long` is an integral conversion.

Promotions also occur inside expressions:
```cpp
short a{10};
short b{20};
auto result{a + b}; // int, not short
```

#### Numeric conversions
Other conversions between numeric types are *numeric conversions* rather than promotions.

| Conversion                                    | Example                 | Possible effect                                      |
| --------------------------------------------- | ----------------------- | ---------------------------------------------------- |
| Integer to another integer type               | `int` to `unsigned int` | Value may change if it does not fit                  |
| Integer to floating-point                     | `int` to `float`        | May lose precision because less exponent bits        |
| Floating-point to integer                     | `double` to `int`       | Discards the fractional part                         |
| Floating-point to another floating-point type | `double` to `float`     | May lose precision or exceed the destination's range |

```cpp
double value{8.75};
int whole = value; // 8; a compiler may warn about this conversion
```

- For an integer destination other than `bool`, conversion from floating-point truncates toward zero; it does not round to the nearest integer.
- `8.75` becomes `8`, and `-8.75` becomes `-8`.
- If the truncated floating-point value cannot be represented by the destination integer type, the behavior is undefined.
- Converting NaN or infinity to an ordinary integer type also causes undefined behavior.
- Converting an integer to floating-point can lose low-order digits even when the magnitude fits.
	- For example, if `int x{16777217}` is converted to `float`, `x` will equal `16777216` because the last bits are excluded due to `float` having less exponent bits.

#### Explicit conversions and casts
An *explicit conversion* is a conversion that the programmer requests directly.
A *cast* is syntax used to request such a conversion.
For ordinary numeric conversions, prefer `static_cast`:
```cpp
double value{8.75};
int whole{static_cast<int>(value)}; // 8
```
- The destination type goes inside `< >`.
- The expression being converted goes inside `( )`.
- The cast produces a converted result; it does not modify `value`.
- `static_cast` checks whether the requested kind of conversion is permitted.
- It does not generally check whether a runtime value fits the destination type.
- A cast makes the conversion explicit; it does not automatically make it safe.

`static_cast` does not mean that the value must be converted during compilation:
```cpp
#include <iostream>

int main() {
    int count{};
    if (std::cin >> count) {
        double value{static_cast<double>(count)};
        std::cout << value << '\n';
    }
}
```
Here, the input value is obtained at runtime.

#### Narrowing conversions
A *narrowing conversion* is a conversion classified by the language as potentially losing information under the list-initialization rules.
List initialization using `{ }` rejects narrowing conversions.
```cpp
int a = 3.5; // allowed: becomes 3, possibly with a warning
// int b{3.5}; // error: narrowing
// int c{3.0}; // error: floating-point to integer is still narrowing
```
Some integer conversions are allowed when the source is a constant expression whose value fits:
```cpp
unsigned int a{10}; // valid: constant expression 10 fits

int value{10};
// unsigned int b{value}; // error: potentially narrowing

constexpr int known{10};
unsigned int c{known}; // valid: known value fits
```
An explicit cast can perform the conversion before list initialization:
```cpp
double value{3.5};
int result{static_cast<int>(value)}; // 3
```

- The initializer is now an `int`, so the braces have no narrowing conversion to reject.
- The programmer remains responsible for the conversion inside the cast.
- See [[Initialization]] for the different initialization forms.

#### Usual arithmetic conversions
Many arithmetic and comparison operators convert their operands to a common type before performing the operation.
These rules are called the *usual arithmetic conversions*.
```cpp
int whole{5};
double fraction{2.5};
auto result{whole + fraction}; // double: 7.5
```
- For the ordinary numeric types here, an integer operand is converted to the floating-point operand's type.
- With two integer operands, integral promotions happen first, followed by rules involving rank and signedness.
- The common type is not simply whichever type uses more bytes.

The destination variable does not change the arithmetic already performed:
```cpp
int numerator{5};
int denominator{2};

double a = numerator / denominator; // 2.0: integer division happened first
double b = static_cast<double>(numerator) / denominator; // 2.5
double c = static_cast<double>(numerator / denominator); // still 2.0
```
Convert an operand before division when a floating-point result is required.

#### Signed and unsigned conversions
Converting a negative integer to an unsigned type does not preserve its negative value.
```cpp
int value{-1};
unsigned int converted{static_cast<unsigned int>(value)};
// converted is the maximum value of unsigned int
```

- Conversion to an unsigned integer produces the corresponding value modulo the destination type's range size.
- For a 32-bit `unsigned int`, converting `-1` produces `4294967295`.
- Since C++20, out-of-range conversions to signed integer types also follow the specified modulo rule. Before C++20, their results are implementation-defined.
- These conversion rules do not make signed arithmetic overflow valid.

Mixed signed and unsigned comparisons can therefore be surprising:
```cpp
int value{-1};
unsigned int limit{10};
bool is_less{value < limit}; // false: value converts to unsigned int
```
Prefer compatible types, and check the range before intentionally changing signedness.

#### Boolean conversions
Numeric values and ordinary pointers can be converted to `bool`.
```cpp
bool a = 0;  // false
bool b = 5;  // true
bool c = -2; // true

int* ptr{nullptr};
if (ptr) {
    // entered only when ptr is non-null
}
```

- Numeric zero converts to `false`; other numeric values convert to `true`.
- A null pointer converts to `false`; a non-null pointer converts to `true`.
- A non-null pointer is not necessarily valid to dereference.
- An explicit comparison often communicates the intended condition more clearly: `count != 0` or `ptr != nullptr`.
- List-initialization narrowing rules still apply when initializing a `bool` with braces.

#### Pointer qualification conversions
A pointer to a non-const object can implicitly convert to a pointer to const.
```cpp
int value{10};
int* writable{&value};
const int* readable{writable}; // valid

// int* writable_again{readable}; // error: would discard const
```

- This restricts what can be done through `readable`.
- It does not make the original `value` object const.
- See [[Pointers]] and [[References]] for access through pointers and references.

#### Other C++ casts
C++ provides four named casts with different purposes:

| Cast | Purpose |
|---|---|
| `static_cast<T>(value)` | Ordinary explicit conversions and certain related-type conversions |
| `dynamic_cast<T>(value)` | Class-hierarchy conversions, including runtime-checked downcasts and cross-casts for polymorphic objects |
| `const_cast<T>(value)` | Changes `const` or `volatile` qualifications in supported pointer and reference conversions |
| `reinterpret_cast<T>(value)` | Certain low-level conversions involving pointers, references, and integers |

- A failed runtime-checked `dynamic_cast` to a pointer returns `nullptr`; one to a reference throws `std::bad_cast`.
- Removing `const` does not permit modifying an object that was originally defined as const; doing so is undefined behavior.
- `reinterpret_cast` does not generally convert the pointed-to data into a new type or make accessing it through an unrelated type valid.
- Detailed class-hierarchy casts belong with classes and inheritance.

C-style casts are also supported:
```cpp
double value{3.5};
int a = (int)value;               // C-style cast
int b{static_cast<int>(value)};   // named C++ cast
```
A C-style cast can perform several different kinds of conversion. A named cast makes the requested operation clearer.

#### Best practices

- Prefer types that avoid unnecessary conversions.
- Use promotions and other value-preserving implicit conversions where appropriate.
- Prefer list initialization to catch unintended narrowing.
- Use `static_cast` when an ordinary explicit conversion is intentional.
- Check the value's range before a conversion that could lose or invalidate data.
- Investigate conversion warnings instead of adding casts only to hide them.
- Remember that a numeric cast does not parse a string or change a variable's declared type.

#### Related notes

- [[Types]]
- [[Initialization]]
- [[Operators]]
- [[Value Categories unc]]

#### References

- C++ working draft: [standard conversions](https://eel.is/c++draft/conv), [integral promotions](https://eel.is/c++draft/conv.prom), and [floating-point promotion](https://eel.is/c++draft/conv.fpprom).
- C++ working draft: [integral conversions](https://eel.is/c++draft/conv.integral), [floating-integral conversions](https://eel.is/c++draft/conv.fpint), and [floating-point conversions](https://eel.is/c++draft/conv.double).
- C++17 working draft: [the earlier signed-integer conversion rule](https://timsong-cpp.github.io/cppwp/n4659/conv.integral).
- C++ working draft: [list initialization](https://eel.is/c++draft/dcl.init.list), [usual arithmetic conversions](https://eel.is/c++draft/expr.arith.conv), [Boolean conversions](https://eel.is/c++draft/conv.bool), and [qualification conversions](https://eel.is/c++draft/conv.qual).
- C++ working draft: [static cast](https://eel.is/c++draft/expr.static.cast), [dynamic cast](https://eel.is/c++draft/expr.dynamic.cast), [const cast](https://eel.is/c++draft/expr.const.cast), [reinterpret cast](https://eel.is/c++draft/expr.reinterpret.cast), and [C-style casts](https://eel.is/c++draft/expr.cast).
