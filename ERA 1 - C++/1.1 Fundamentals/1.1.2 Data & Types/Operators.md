An *operator* is a symbol or keyword that performs an operation on one or more values, called *operands*.
An *expression* is a sequence of operators and operands that can be evaluated to produce a result.
```cpp
int x{10 + 5};
```
- `+` is the operator.
- `10` and `5` are the operands.
- `10 + 5` is an expression.
- Operators can perform arithmetic, comparisons, assignments, logical operations, and other operations.
- The behavior of an operator depends on the types of its operands.
#### Operator arity
*Operator arity* refers to the number of operands an operator accepts.
C++ operators can be classified into:
- **Unary operators:** Operate on one operand.
- **Binary operators:** Operate on two operands.
- **Ternary operators:** Operate on three operands.
```cpp
int x{5};
int a{-x};             // unary
int b{x + 10};         // binary
int c{x > 0 ? x : 0};  // ternary
```
- Most C++ operators are unary or binary.
- The conditional operator `?:` is the only ternary operator in C++.
- Some symbols have different meanings depending on their context.
#### Arithmetic operators
*Arithmetic operators* perform mathematical operations on numeric values.
| Operator | Operation | Example |
|---|---|---|
| `+` | Addition | `5 + 3` |
| `-` | Subtraction | `5 - 3` |
| `*` | Multiplication | `5 * 3` |
| `/` | Division | `10 / 2` |
| `%` | Remainder | `10 % 3` |
| `+` | Unary plus | `+5` |
| `-` | Unary minus | `-5` |
```cpp
int a{10};
int b{3};
int sum{a + b};        // 13
int difference{a - b}; // 7
int product{a * b};    // 30
int quotient{a / b};   // 3
int remainder{a % b};  // 1
```
- Division between two integers produces an integer result.
- Integer division discards the fractional part, truncating toward zero.
- If either operand is floating-point, the usual arithmetic conversions determine a floating-point common type.
- The remainder operator `%` only works with integral types.
- Division or remainder by zero using integral operands causes undefined behavior.
```cpp
double a{10.0 / 3}; // approximately 3.333333
int b{10 / 3};      // 3
int c{-10 / 3};     // -3
int d{-10 % 3};     // -1
```
#### Increment and decrement operators
The *increment* (`++`) and *decrement* (`--`) operators increase or decrease a value by one.
```cpp
int x{5};
++x; // x becomes 6
--x; // x becomes 5
```
There are two forms:
- **Prefix:** `++x` or `--x`.
- **Postfix:** `x++` or `x--`.
```cpp
int x{5};
int a{++x}; // x = 6, a = 6
int b{x++}; // b = 6, x = 7
```
- Prefix modifies the operand and produces the updated value.
- Postfix modifies the operand but produces the previous value.
- Prefix is generally preferable when the previous value is not needed.
- Avoid modifying the same variable multiple times in a single expression when evaluation order is unclear.
```cpp
int x{5};
int y{x++ + 2}; // y = 7, x = 6
```
#### Assignment operators
An *assignment operator* assigns a value to an existing object.
```cpp
int x{10};
x = 20;
```
- `=` is the assignment operator.
- The left operand identifies the object being assigned to.
- The right operand provides the value.
- Assignment is different from initialization.
```cpp
int x{10}; // initialization
x = 20;    // assignment
```
C++ also provides *compound assignment operators*:
| `+=` | `x = x + y` |
| `-=` | `x = x - y` |
| `*=` | `x = x * y` |
| `/=` | `x = x / y` |
| `%=` | `x = x % y` |
| `&=` | `x = x & y` |
| `\|=` | `x = x \| y` |
| `^=` | `x = x ^ y` |
| `<<=` | `x = x << y` |
| `>>=` | `x = x >> y` |
```cpp
int x{10};
x += 5; // 15
x *= 2; // 30
x -= 10; // 20
```
- Compound assignment performs an operation and assigns the result to the left operand.
- Unlike the expanded expression, the left operand of a compound assignment is evaluated only once.
- Assignment operators generally associate from right to left.
```cpp
int x{};
int y{};
x = y = 10;
```
#### Relational operators
*Relational operators* compare two values and produce a Boolean result.
| `==` | Equal to |
| `!=` | Not equal to |
| `<` | Less than |
| `>` | Greater than |
| `<=` | Less than or equal to |
| `>=` | Greater than or equal to |
```cpp
int x{10};
int y{20};
bool a{x == y}; // false
bool b{x != y}; // true
bool c{x < y};  // true
bool d{x >= y}; // false
```
- Relational and equality operators commonly produce `bool`.
- `==` compares values, whereas `=` performs assignment.
- Comparing floating-point values using `==` can be unreliable when rounding errors are involved.
- Comparison operators can be overloaded for user-defined types.
#### Logical operators
*Logical operators* combine or negate Boolean expressions.
| `!` | Logical NOT |
| `&&` | Logical AND |
| `||` | Logical OR |
```cpp
bool a{true};
bool b{false};
bool x{!a};     // false
bool y{a && b}; // false
bool z{a || b}; // true
```
- `!` produces the opposite Boolean value.
- `&&` produces `true` only when both operands evaluate to `true`.
- `||` produces `true` when at least one operand evaluates to `true`.
- Logical operators convert their operands to `bool` when necessary.
#### Short-circuit evaluation
*Short-circuit evaluation* occurs when the result of a logical expression can be determined without evaluating every operand.
```cpp
int x{0};
if (x != 0 && 10 / x > 2) {
    // ...
}
```
- `&&` evaluates its right operand only if its left operand evaluates to `true`.
- `||` evaluates its right operand only if its left operand evaluates to `false`.
- Short-circuit evaluation can prevent unnecessary work or invalid operations.
- These guarantees apply to the built-in logical operators, not overloaded versions.
```cpp
bool is_ready{true};
if (is_ready || check_status()) {
    // check_status() is not called
}
```
#### Conditional operator
The *conditional operator* (`?:`) evaluates a condition and selects one of two expressions.
```cpp
condition ? expression_if_true : expression_if_false
```
For example:
```cpp
int x{10};
int result{x > 5 ? 100 : 0};
```
- The first operand is converted to `bool`.
- If the condition is `true`, the second operand is evaluated.
- Otherwise, the third operand is evaluated.
- Only the selected branch is evaluated.
- The result type depends on the operand types.
```cpp
int a{10};
int b{20};
int max{a > b ? a : b};
```
The conditional operator is useful for simple conditional expressions, but more complicated logic is usually clearer with an `if` statement.
#### Bitwise operators
*Bitwise operators* operate on the individual bits of integral values.
| `&` | Bitwise AND |
| `|` | Bitwise OR |
| `^` | Bitwise XOR |
| `~` | Bitwise NOT |
| `<<` | Left shift |
| `>>` | Right shift |
```cpp
unsigned int a{12}; // 1100
unsigned int b{10}; // 1010
unsigned int x{a & b}; // 1000 = 8
unsigned int y{a | b}; // 1110 = 14
unsigned int z{a ^ b}; // 0110 = 6
```
- Bitwise AND sets a bit when both corresponding bits are set.
- Bitwise OR sets a bit when at least one corresponding bit is set.
- Bitwise XOR sets a bit when exactly one corresponding bit is set.
- Bitwise NOT inverts bits.
- Shift operators move bits left or right.
```cpp
unsigned int x{1};
unsigned int a{x << 3}; // 8
unsigned int b{a >> 2}; // 2
```
- Left-shifting an unsigned integer by one position corresponds to multiplication by two, modulo the type's range.
- Right-shifting an unsigned integer by one position corresponds to integer division by two.
- Shifting by a negative amount or an amount greater than or equal to the promoted left operand's bit width causes undefined behavior.
- Unsigned integer types are generally preferable for bit manipulation.
#### Bit masks
A *bit mask* is a value used to select, set, clear, or toggle specific bits.
```cpp
constexpr unsigned int read{1u << 0};
constexpr unsigned int write{1u << 1};
constexpr unsigned int execute{1u << 2};
unsigned int permissions{read | write};
```
- `|` combines bit flags.
- `&` checks whether specific bits are set.
- `~` is commonly used to construct masks for clearing bits.
- `^` toggles selected bits.
```cpp
permissions |= execute;  // set
permissions &= ~write;   // clear
permissions ^= read;     // toggle
bool can_read{(permissions & read) != 0};
```
Bit masks are commonly used for permissions, configuration flags, graphics APIs, and operating system interfaces.
#### sizeof operator
The `sizeof` operator returns the size of a type or object in bytes.
```cpp
int x{10};
std::size_t a{sizeof(x)};
std::size_t b{sizeof(double)};
```
- `sizeof` produces a value of type `std::size_t`.
- The size is determined at compile time for ordinary C++ types.
- The size of a type may depend on the platform and implementation.
- `sizeof(char)` is always `1`.
- A C++ byte contains at least 8 bits, but is not required to contain exactly 8 bits.
- The operand of `sizeof` is generally not evaluated.
#### Address-of and dereference operators
The *address-of operator* (`&`) obtains the address of an object.
The *dereference operator* (`*`) accesses the object pointed to by a pointer.
```cpp
int x{10};
int* ptr{&x};
std::cout << *ptr; // 10
```
- `&x` produces a pointer to `x`.
- `*ptr` accesses the object pointed to by `ptr`.
- `*ptr = 20;` modifies `x`.
- Dereferencing a null or otherwise invalid pointer causes undefined behavior.
- The symbols `&` and `*` also have other meanings depending on context.
#### Member access operators
*Member access operators* allow access to members of classes, structs, and unions.
```cpp
struct Player {
    int health{100};
};
Player player{};
Player* ptr{&player};
std::cout << player.health;
std::cout << ptr->health;
```
- `.` accesses a member through an object.
- `->` accesses a member through a pointer to an object.
- `ptr->health` is equivalent to `(*ptr).health` for ordinary built-in pointer access.
- Member access is affected by access specifiers such as `public` and `private`.
#### Subscript operator
The *subscript operator* (`[]`) accesses an element of an array or another type supporting indexed access.
```cpp
int numbers[]{10, 20, 30};
int x{numbers[1]}; // 20
```
- Array indices start at `0`.
- The built-in subscript operator does not perform bounds checking.
- Accessing outside an array's valid bounds causes undefined behavior.
- Standard library containers may provide checked alternatives such as `std::vector::at()`.
#### Function call operator
The *function call operator* (`()`) is used to invoke functions and callable objects.
```cpp
int square(int x) {
    return x * x;
}
int main() {
    int result{square(5)};
}
```
- The parentheses contain the arguments supplied to the function.
- The compiler checks the call against the available function declarations.
- User-defined types can overload `operator()` to create callable objects.
#### Comma operator
The *comma operator* evaluates its left operand, discards its result, then evaluates its right operand.
```cpp
int x{};
int y{};
int result{(x = 5, y = 10, x + y)};
```
- The result is `15`.
- The left operand is sequenced before the right operand.
- The result and value category come from the right operand.
- The comma operator has very low precedence.
- Commas separating function arguments, declarations, or initializer elements are not comma operators.
- Avoid the comma operator when separate statements would be clearer.
#### Scope resolution operator
The *scope resolution operator* (`::`) identifies a name within a namespace, class, or global scope.
```cpp
#include <iostream>
int value{30};
namespace math {
    int value{10};
}
int main() {
    int value{20};
    std::cout << math::value; // 10
    std::cout << ::value;    // 30, the global value
}
```
- `math::value` refers to a member of the `math` namespace.
- `Player::function()` can refer to a class member function.
- `::name` explicitly performs lookup starting from global scope.
- The scope resolution operator is used for qualified name lookup.
#### Type conversion operators
A *cast* explicitly requests a conversion between types.
The most commonly used C++ cast is `static_cast`.
```cpp
double x{3.9};
int y{static_cast<int>(x)}; // 3
```
C++ provides four named casts:
- `static_cast` — performs supported compile-time conversions.
- `dynamic_cast` — performs certain runtime-checked conversions involving polymorphic types.
- `const_cast` — adds or removes `const` or `volatile` qualification.
- `reinterpret_cast` — performs certain low-level reinterpretations.
- C-style casts are also supported but are generally less explicit.
Prefer `static_cast` for ordinary explicit conversions.
#### Operator precedence
*Operator precedence* determines how operators are grouped when an expression contains multiple operators.
```cpp
int result{2 + 3 * 4}; // 14
```
Multiplication has higher precedence than addition, so the expression groups as:
```cpp
int result{2 + (3 * 4)};
```
Common precedence levels, from higher to lower:
| Operators | Category |
|---|---|
| `::` | Scope resolution |
| `()`, `[]`, `.`, `->`, postfix `++` and `--` | Postfix and member access |
| Unary `+`, `-`, `!`, `~`, prefix `++` and `--`, `sizeof`, unary `*` and `&` | Unary |
| `*`, `/`, `%` | Multiplicative |
| `+`, `-` | Additive |
| `<<`, `>>` | Shift |
| `<`, `<=`, `>`, `>=` | Relational |
| `==`, `!=` | Equality |
| `&` | Bitwise AND |
| `^` | Bitwise XOR |
| `|` | Bitwise OR |
| `&&` | Logical AND |
| `||` | Logical OR |
| `?:`, `=`, `+=`, `-=` and other assignments | Conditional and assignment |
| `,` | Comma |
- Operators with higher precedence bind more tightly than operators with lower precedence.
- Parentheses can override the default grouping.
- Precedence does not determine the order in which operands are evaluated.
- This table is a practical summary rather than a complete C++ precedence table.
#### Operator associativity
*Operator associativity* determines how operators with the same precedence are grouped.
```cpp
int x{10 - 5 - 2};
```
Subtraction associates from left to right:
```cpp
int x{(10 - 5) - 2}; // 3
```
Assignment associates from right to left:
```cpp
int a{};
int b{};
a = b = 10;
```
This groups as:
```cpp
a = (b = 10);
```
- Most arithmetic operators associate from left to right.
- Assignment operators associate from right to left.
- Associativity determines grouping, not necessarily evaluation order.
#### Evaluation order
*Evaluation order* determines when individual expressions and their side effects are evaluated.
```cpp
#include <iostream>
int first() {
    std::cout << "A";
    return 1;
}
int second() {
    std::cout << "B";
    return 2;
}
int main() {
    int result{first() + second()};
}
```
- The `+` operator does not specify which function call is evaluated first.
- The program may print `AB` or `BA`.
- Both function calls are evaluated before the addition is completed.
- Some operators, including built-in `&&`, `||`, and the comma operator, guarantee sequencing between operands.
- Avoid expressions that rely on an unspecified evaluation order.
#### Operator overloading
*Operator overloading* allows user-defined types to provide behavior for existing operators.
```cpp
struct Vector2 {
    int x{};
    int y{};
};
Vector2 operator+(const Vector2& a, const Vector2& b) {
    return {a.x + b.x, a.y + b.y};
}
int main() {
    Vector2 a{1, 2};
    Vector2 b{3, 4};
    Vector2 result{a + b};
}
```
- Operator overloading allows custom types to behave naturally with operators.
- At least one operand must have a class or enumeration type for a user-defined operator overload.
- Existing operator precedence, associativity, and arity cannot be changed.
- New operator symbols cannot be created.
- Some operators, such as `::`, `.`, `?:`, and `sizeof`, cannot be overloaded.
- Overloaded operators should behave consistently with their conventional meaning.
#### Best practices
- Use parentheses when they improve clarity, even when precedence already produces the intended grouping.
- Avoid writing complicated expressions with multiple side effects.
- Prefer prefix increment when the previous value is not needed.
- Use `==` for equality comparisons and `=` for assignments.
- Avoid direct equality comparisons between calculated floating-point values when rounding errors are possible.
- Prefer logical operators for Boolean conditions and bitwise operators for bit manipulation.
- Use unsigned integer types when working with bit masks.
- Use `static_cast` for ordinary explicit type conversions.
- Avoid undefined behavior involving arithmetic, shifts, pointers, and array access.
- Remember that precedence and associativity determine grouping, not necessarily evaluation order.
- Overload operators only when their behavior is intuitive and improves readability.
#### Reading a flag combination
Suppose `read = 1`, `write = 2` and `execute = 4`. Each value selects a different binary position.
- `read | write` is `3`: the read and write positions are both set.
- `(permissions & read) != 0` asks whether the read position is set.
- `(permissions & required) == required` asks whether **all** bits in a multi-bit requirement are set.
- `(permissions & required) != 0` asks whether **any** required bit is set.
For `permissions = 5`, read and execute are enabled; write is disabled.
When the required mask is zero, the all-bits test is true and the any-bit test is false.
Prefer unsigned operands for bit manipulation. A shift count must be nonnegative and less than the width of the promoted left operand. Ensure that the literal being shifted has the intended width before the shift occurs.
Windows and graphics flags should be combined only where the specific API permits those flags together.
