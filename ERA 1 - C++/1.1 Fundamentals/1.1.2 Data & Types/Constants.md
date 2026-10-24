A *constant* is a value that cannot be changed. C++ provides several ways to represent constants, including constant variables, literals, and compile-time constants.
#### Constant Variables
A *constant variable* is a variable whose value cannot be modified after initialization.
- The `const` keyword is used to declare constant variables.
- Constant variables must be initialized when defined.
- Attempting to modify a constant variable results in a compilation error.
```cpp
int x{ 10 };
x = 20; // valid

const int y{ 10 };
y = 20; // error: y is const
```
There are two important kinds of constant variables:
- **Compile-time constants:** Their values are known during compilation and can be used in certain contexts requiring constant expressions.
- **Runtime constants:** Their values are determined during program execution, but cannot be modified afterward.
```cpp
const int x{ 10 }; // compile-time constant

int input{};
std::cin >> input;

const int y{ input }; // runtime constant
```
- `x` is initialized using a constant expression.
- `y` is initialized using a value obtained at runtime.
- Both variables are immutable after initialization.
It is important to distinguish between **constant** and **compile-time constant**. A variable being `const` does not necessarily mean its value is known during compilation.
#### Literals
A *literal* is a fixed value written directly into the source code.
```cpp
10          // integer literal
3.14        // floating-point literal
'A'         // character literal
"Hello"     // string literal
true        // boolean literal
```
Literals have types determined by their syntax and optional suffixes:
```cpp
10      // int
10u     // unsigned int
10L     // long
3.14    // double
3.14f   // float
'A'     // char
```
- Integer literals are generally of type `int` when their value fits, unless a suffix or value range determines otherwise.
- Floating-point literals are `double` by default.
- Suffixes can specify different literal types.
- String literals are arrays of `const char` in ordinary C++ code.
**Literals are fixed values**, unlike variables, which represent objects that store values.
#### As-if Rule and Compile-Time Optimization
The *as-if rule* allows the compiler to transform a program in any way, provided the program's required observable behavior remains unchanged.
This gives the compiler considerable freedom to optimize code.
- For example, with the statement `int x{5 * 4};`, the compiler may replace it with `int x{20};`
- Since both produce the same result, the compiler does not necessarily need to perform the multiplication at runtime.
Common compile-time optimizations include:
- **Constant folding:** Evaluating expressions with known values during compilation.
  - Example: `5 * 4` becomes `20`.
- **Constant propagation:** Replacing uses of variables with their known values when valid.
  - Example: If `x` is known to equal `10`, an expression using `x` may be simplified using `10`.
- **Dead code elimination:** Removing computations or code that cannot affect the program's observable behavior.
The compiler may also reorder or eliminate operations when doing so preserves the required behavior.
- It is important to note that an expression being optimized at compile time does not necessarily mean it is a constant expression according to C++ language rules.
#### Constant Expressions
A *constant expression* is an expression that satisfies the C++ requirements for evaluation at compile time:
```cpp
5 + 3             // constant expression
10 * 2            // constant expression
true && false     // constant expression
```
However, expressions involving ordinary variables whose values are determined at runtime generally aren't constant expressions:
```cpp
int x{10};
x + 5; // not a constant expression
```
Even though `x` was initialized with `10`, it is a non-constant variable that could be modified.
A constant expression can also reference certain constant variables:
```cpp
constexpr int x{10};

constexpr int y{x + 5}; // valid
```
- `x` is a compile-time constant.
- `x + 5` is a constant expression.
- `y` can therefore be initialized using that expression.
Constant expressions are especially useful in situations where the C++ language requires values to be known at compile time:
```cpp
constexpr int size{5};
int values[size]{}; // valid
```
The size of a built-in array must be a constant expression.
- It is important to note that a constant expression is defined by C++ language rules, not simply by whether the compiler happens to optimize an expression.
#### `constexpr` Variables
The `constexpr` keyword declares a variable that must be initialized using a constant expression.
Unlike `const`, `constexpr` guarantees that the variable is usable in constant expressions.
```cpp
constexpr int x{10};
constexpr int y{x * 2};
```
Both variables have values known during compilation.
Attempting to initialize a `constexpr` variable using a runtime value results in a compilation error:
```cpp
int x{ 10 };
constexpr int y{ x }; // error: x is not a constant expression
```
The compiler cannot treat `x` as a constant expression because `x` is an ordinary modifiable variable.
A `constexpr` variable is also implicitly `const`:
```cpp
constexpr int x{ 10 };

x = 20; // error
```
However, the opposite is not necessarily true:
```cpp
int input{};
std::cin >> input;

const int x{ input };      // valid: runtime constant
constexpr int y{ input }; // error: requires constant expression
```
- `const` prevents modification after initialization.
- `constexpr` requires constant-expression initialization and also prevents modification of the variable.
For integral types, a `const` variable initialized with a constant expression can also be usable in constant expressions:
```cpp
const int x{ 10 };

constexpr int y{ x + 5 }; // valid
```
Therefore, `const` does not always imply runtime evaluation, and `constexpr` is not the only way to create a compile-time constant.
As a general guideline:
- Use `const` when a variable should not be modified after initialization.
- Use `constexpr` when a value is intended to be a compile-time constant.

`end 09/10/2026`