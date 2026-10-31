A *function* is a reusable sequence of statements designed to perform a specific task.
Functions allow programs to be divided into smaller, independent pieces of code.
- A function executes when it is *called*.
- A function can accept input through *parameters*.
- A function can return a value to its caller.
- Functions help reduce code duplication and improve organization.
- Every C++ program must have a `main()` function, which serves as its entry point in a standard hosted environment.
#### Function definitions
A *function definition* specifies the function's return type, name, parameters, and body.
The general syntax is:
```cpp
return_type function_name(parameters) {
    // function body
}
```
For example:
```cpp
int add(int x, int y) {
    return x + y;
}
```
- `int` is the return type.
- `add` is the function name.
- `int x` and `int y` are the parameters.
- The statements inside `{}` form the function body.
- The `return` statement returns a value to the caller.
A function is executed by calling it:
```cpp
int result{add(5, 3)};
```
#### Function calls
A *function call* is an expression that invokes a function.
When a function is called, execution transfers to that function. After the function finishes, execution resumes at the caller.
```cpp
#include <iostream>
void print_message() {
    std::cout << "hi\n";
}
int main() {
    print_message();
}
```
- `main()` calls `print_message()`.
- `print_message()` executes its statements.
- Once it finishes, execution returns to `main()`.
- Functions can call other functions, including themselves.
#### Parameters and arguments
A *parameter* is a variable declared in a function's parameter list.
An *argument* is a value or expression supplied to a function when it is called.
```cpp
#include <iostream>
void print_number(int x) {
    std::cout << x;
}
int main() {
    print_number(10);
}
```
- `x` is a parameter.
- `10` is an argument.
- The argument is used to initialize the parameter.
- Parameters are local to their function.
- Each call initializes its parameters independently.
**Term:** *Parameter passing* is the process of initializing a function's parameters using its arguments.
#### Void functions
A *void function* is a function whose return type is `void`, meaning it does not return a value.
```cpp
void greet() {
    std::cout << "hi";
}
```
- Void functions are commonly used to perform actions without producing a result.
- They do not require an explicit `return` statement.
- `return;` can be used to end a void function early.
```cpp
void print_positive(int x) {
    if (x <= 0) {
        return;
    }
    std::cout << x;
}
```
#### Function declarations
A *function declaration* introduces a function's name, return type, and parameter types without providing its implementation.
```cpp
int add(int x, int y);
```
A function declaration without a definition is also called a *function prototype*.
- A declaration allows the compiler to recognize a function before its definition appears.
- The compiler uses declarations to check function calls.
- A function can be declared multiple times, provided the declarations are compatible.
- A function used by the program must have an appropriate definition.
```cpp
#include <iostream>
int add(int x, int y);
int main() {
    std::cout << add(5, 3);
}
int add(int x, int y) {
    return x + y;
}
```
Function declarations are commonly placed in header files, while their definitions are placed in source files.
#### Default arguments
A *default argument* is a value used for a parameter when the caller does not provide an argument.
```cpp
#include <iostream>
#include <string>
void greet(const std::string& name = "guest") {
    std::cout << "hi, " << name;
}
int main() {
    greet();
    greet("deem");
}
```
- `greet()` uses `"guest"`.
- `greet("deem")` uses `"deem"`.
- Default arguments are specified using `=`.
- Once a parameter has a default argument, all following parameters in that parameter list must also have defaults.
- Default arguments are normally specified in a function's declaration, rather than repeated in its definition.
#### Function overloading
*Function overloading* allows multiple functions to share the same name, provided their parameter lists distinguish them.
```cpp
int add(int x, int y) {
    return x + y;
}
double add(double x, double y) {
    return x + y;
}
```
- Overloaded functions share a name but have different parameter types or parameter counts.
- The compiler selects an appropriate overload based on the supplied arguments.
- Functions cannot be overloaded based only on their return types.
- Overload resolution happens at compile time.
- If the compiler cannot determine a unique best match, the call is ambiguous.
#### Function scope and lifetime
Function parameters and local variables belong to the scope of their function.
```cpp
void example(int x) {
    int y{10};
}
```
- `x` and `y` cannot be accessed by name outside the function.
- Ordinary local variables are created when execution reaches their definitions and destroyed when their lifetime ends.
- Function parameters are initialized when the function is called.
- Each function call has its own parameter objects and ordinary local variables.
- Local variables with `static` storage duration persist between function calls.
```cpp
void counter() {
    static int count{0};
    ++count;
    std::cout << count;
}
```
#### Recursive functions
A *recursive function* is a function that calls itself, either directly or indirectly.
```cpp
int factorial(int n) {
    if (n <= 1) {
        return 1;
    }
    return n * factorial(n - 1);
}
```
- Every recursive call creates a new function invocation.
- A *base case* is a condition that stops further recursion.
- Without a reachable base case, recursion may continue until the program exhausts available stack space or encounters another failure.
- Recursion is useful for problems that can be divided into smaller instances of the same problem.
#### constexpr functions
A *constexpr function* is a function that can be evaluated at compile time when the necessary conditions are satisfied.
```cpp
constexpr int square(int x) {
    return x * x;
}
constexpr int result{square(5)};
```
- `constexpr` functions can participate in constant expressions.
- A `constexpr` function is not guaranteed to execute at compile time for every call.
- Such functions may also execute at runtime.
- `constexpr` functions are implicitly `inline`.
#### Inline functions
The `inline` keyword allows a function to have matching definitions in multiple translation units without violating ODR, provided the applicable requirements are satisfied.
```cpp
// math.h
#pragma once
inline int add(int x, int y) {
    return x + y;
}
```
- Normally, defining a non-inline function in a header included by multiple `.cpp` files can cause multiple-definition linker errors.
- `inline` allows the function definition to appear in multiple translation units.
- Originally, `inline` was intended to suggest replacing function calls with the function's body as an optimization.
- Modern compilers decide whether to perform this optimization independently of the `inline` keyword.
- Functions defined inside class definitions are generally implicitly inline.
- `#pragma once` prevents duplicate inclusion within a translation unit, whereas `inline` addresses the ODR requirements across translation units.
#### Best practices
- Give functions descriptive names that communicate their purpose.
- Keep functions focused on a single responsibility.
- Prefer returning values instead of modifying global variables.
- Use pass by value for inexpensive-to-copy types.
- Use `const` references for larger objects that should not be modified.
- Use non-const references or pointers when modifying an argument is intentional.
- Avoid unnecessary parameters and return values.
- Prefer function declarations in headers and ordinary function definitions in source files.
- Use function overloading when several operations represent the same concept.
- Avoid unnecessary recursion when a simple iterative solution is clearer.
- Use `constexpr` when a function is suitable for constant evaluation.