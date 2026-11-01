An *error* is a problem in a program that prevents it from compiling, causes it to behave incorrectly, or produces an unexpected result.
Errors can occur at different stages of a program's development and execution.
- C++ errors can generally be categorized into:
  - Syntax errors
  - Semantic errors
  - Linker errors
  - Runtime errors
  - Logic errors
- Some errors are detected by the compiler or linker.
- Other errors only become apparent when the program executes.
- Not all errors produce diagnostic messages.
#### Syntax errors
A *syntax error* occurs when code violates the grammatical rules of the C++ language.
```cpp
int main() {
    int x{10}
    return 0;
}
```
- The declaration of `x` is missing a semicolon.
- The compiler detects the invalid syntax and produces a diagnostic.
- Syntax errors prevent successful compilation.
- Common examples include missing semicolons, unmatched braces, and malformed expressions.
#### Semantic errors
A *semantic error* occurs when code is syntactically valid but violates a rule of the language.
```cpp
int main() {
    int x{"Hello"};
}
```
- The syntax is valid, but a string literal cannot initialize an `int` this way.
- The compiler detects the invalid initialization.
- Semantic errors commonly involve incompatible types, invalid operations, and incorrect function calls.
- Many semantic errors are detected during compilation.
**Term:** A *diagnostic* is a message produced by the compiler or another tool to report a problem.
#### Linker errors
A *linker error* occurs when the linker cannot successfully combine compiled object files and libraries into an executable or library.
```cpp
// main.cpp
int add(int x, int y);
int main() {
    return add(5, 3);
}
```
If `add()` is declared but its definition is not provided to the linker, linking fails.
- The compiler accepts the call because the function declaration is visible.
- The object file contains an unresolved reference to `add()`.
- The linker searches the available object files and libraries for a matching symbol.
- If it cannot resolve the symbol, it reports an error.
- In MSVC, a common error is `LNK2019: unresolved external symbol`.
Other causes of linker errors include:
- Defining the same non-inline function in multiple translation units.
- Forgetting to compile or link a required source file.
- Forgetting to link a required library.
- Using incompatible function declarations and definitions.
#### Runtime errors
A *runtime error* occurs while a program is executing.
```cpp
#include <vector>
int main() {
    std::vector<int> numbers{1, 2, 3};
    int x{numbers.at(10)};
}
```
- The code compiles successfully.
- `at(10)` attempts to access an element outside the vector's valid range.
- `std::vector::at()` throws a `std::out_of_range` exception.
- If the exception remains uncaught, the program terminates.
Runtime errors can include:
- Failed file operations.
- Failed memory allocations.
- Exceptions that are not handled.
- Invalid user input.
- Operations that trigger undefined behavior.
- Runtime errors do not necessarily cause a program to crash.
#### Logic errors
A *logic error* occurs when a program executes but produces an incorrect result because its implementation does not match its intended behavior.
```cpp
int calculate_area(int width, int height) {
    return width + height;
}
```
- The code compiles and executes.
- The function incorrectly adds the dimensions instead of multiplying them.
- The compiler generally cannot determine the programmer's intention.
- Logic errors are commonly discovered through testing, debugging, or unexpected output.
#### Compiler warnings
A *compiler warning* is a diagnostic indicating code that may be incorrect, unsafe, or undesirable, even though compilation may still succeed.
```cpp
int main() {
    int x{10};
}
```
Depending on the compiler and warning settings, this may produce a warning because `x` is unused.
- Warnings do not necessarily prevent compilation.
- A warning does not always indicate an actual bug.
- Some warnings identify potentially dangerous implicit conversions or suspicious expressions.
- Compiler options can control which warnings are enabled.
- Many compilers support treating warnings as errors.
For example, MSVC supports:
```text
/W4    Enable a high warning level
/WX    Treat warnings as errors
```
#### Undefined behavior
*Undefined behavior* (UB) occurs when a program performs an operation for which the C++ standard imposes no requirements on the result.
```cpp
int main() {
    int x{10};
    int y{0};
    int result{x / y};
}
```
- Integer division by zero causes undefined behavior.
- The program might crash, produce unexpected results, or appear to work.
- The compiler is not required to diagnose undefined behavior.
- Compilers may optimize programs under the assumption that undefined behavior does not occur.
Common examples include:
- Signed integer overflow.
- Accessing an array outside its bounds.
- Dereferencing an invalid pointer.
- Reading an uninitialized automatic variable in cases where the language defines the behavior as undefined.
- Accessing an object after its lifetime has ended.
**Important:** Undefined behavior is not simply an unpredictable result. Once undefined behavior occurs, the C++ standard places no requirements on the program's behavior.
#### Implementation-defined behavior
*Implementation-defined behavior* occurs when the C++ standard permits multiple behaviors but requires the implementation to document which behavior it uses.
For example, the size of an `int` depends on the implementation, subject to the standard's minimum requirements.
```cpp
#include <iostream>
int main() {
    std::cout << sizeof(int);
}
```
- The result may differ between platforms or compilers.
- The implementation must document its choice.
- Implementation-defined behavior is not the same as undefined behavior.
#### Unspecified behavior
*Unspecified behavior* occurs when the C++ standard permits multiple possible behaviors without requiring the implementation to document which one is selected.
```cpp
#include <iostream>
int get_value(int x) {
    std::cout << x;
    return x;
}
int main() {
    int result{get_value(1) + get_value(2)};
}
```
- The order in which these two function calls are evaluated is not specified.
- Both calls are valid.
- Either `12` or `21` may be printed.
- The implementation is not required to document which permitted order it selects.
#### Assertions
An *assertion* is a condition checked during execution to detect incorrect assumptions in a program.
C++ provides the `assert` macro through `<cassert>`.
```cpp
#include <cassert>
int divide(int x, int y) {
    assert(y != 0);
    return x / y;
}
```
- If the condition is true, execution continues.
- If the condition is false, the assertion normally prints diagnostic information and terminates the program.
- Assertions are useful for detecting programming mistakes during development.
- Defining `NDEBUG` disables standard `assert` checks.
- Assertions should not be used as the only validation for conditions that can legitimately fail at runtime.
- Expressions inside assertions should not have required side effects because the assertion may be disabled.
#### Static assertions
A *static assertion* checks a condition at compile time.
```cpp
static_assert(sizeof(int) >= 4, "int must be at least 4 bytes");
```
- `static_assert` requires a condition that can be evaluated as a constant expression.
- If the condition is false, compilation fails.
- Static assertions are useful for enforcing compile-time requirements.
- Unlike ordinary assertions, they do not perform runtime checks.
#### Error handling
*Error handling* is the process of detecting and responding to failures in a controlled manner.
Common approaches include:
- Returning an error code.
- Returning a value representing success or failure.
- Using exceptions.
- Using types such as `std::optional` or `std::expected` when appropriate.
For example, a function can communicate failure using a Boolean return value:
```cpp
bool divide(int x, int y, int& result) {
    if (y == 0) {
        return false;
    }
    result = x / y;
    return true;
}
```
- The function checks whether division is valid.
- It returns `false` if division cannot be performed.
- It returns `true` after storing the result.
- The caller is responsible for checking the returned status.
#### Exceptions
An *exception* is a mechanism for reporting and handling certain errors by transferring control to an exception handler.
C++ uses `try`, `throw`, and `catch`:
```cpp
#include <iostream>
#include <stdexcept>
int divide(int x, int y) {
    if (y == 0) {
        throw std::invalid_argument{"Division by zero"};
    }
    return x / y;
}
int main() {
    try {
        int result{divide(10, 0)};
    }
    catch (const std::invalid_argument& error) {
        std::cout << error.what();
    }
}
```
- `throw` raises an exception.
- `try` identifies a region in which exceptions may be handled.
- `catch` handles an exception of a matching type.
- When an exception propagates, stack unwinding destroys appropriate local objects.
- If no matching handler is found, the program calls `std::terminate()`.
- Exceptions are generally intended for exceptional failures rather than ordinary control flow.
#### Debugging
*Debugging* is the process of identifying, understanding, and correcting errors in a program.
Common debugging techniques include:
- Reading compiler and linker diagnostics.
- Using breakpoints to pause execution.
- Stepping through code.
- Inspecting variables and memory.
- Examining the call stack.
- Adding logging or temporary diagnostic output.
- Writing tests to reproduce failures.
A *breakpoint* is a location where a debugger pauses program execution.
The *call stack* records active function calls and helps identify how execution reached a particular point.
#### Best practices
- Enable compiler warnings and investigate them.
- Fix compiler and linker errors before attempting to run the program.
- Read the first meaningful compiler error before investigating subsequent errors.
- Distinguish syntax, semantic, linker, runtime, and logic errors.
- Avoid undefined behavior, even when the program appears to work correctly.
- Validate external input and operations that can legitimately fail.
- Use assertions to verify internal assumptions, not as a replacement for runtime error handling.
- Use `static_assert` to enforce compile-time requirements.
- Prefer explicit error handling over silently ignoring failures.
- Use a debugger and automated tests to diagnose unexpected behavior.
#### Choosing an error interface
Choose an interface that communicates what a caller can actually do about failure.
| Form | Useful when |
|---|---|
| Boolean | Only success/failure matters |
| `std::optional<T>` | A value may legitimately be absent |
| Result object / error code | The caller needs a reason and ordinary control flow |
| Exception | Failure should propagate to a handling boundary |
`std::expected<T, E>` is a C++23 result type. In a C++20 project, use an explicit result structure or a suitable library type when a value-or-error interface is needed.
An empty optional does not explain whether an encoder was unavailable, the disk was full, or a configuration was invalid.
#### Exception guarantees
- **No-throw guarantee:** the operation does not let an exception escape.
- **Strong guarantee:** failure leaves the relevant state unchanged.
- **Basic guarantee:** invariants hold and resources are not leaked, although state may change.
RAII helps with cleanup but does not automatically restore logical state. Prepare a new configuration before committing it if failure must leave the previous configuration intact.
#### noexcept
```cpp
int read_counter() noexcept {
    return 0;
}
```
If an exception escapes a `noexcept` function, the program calls `std::terminate`.
`noexcept` is a contract, not an instruction to ignore failures. It matters to move operations, destructors and generic code choosing between moves and copies.
Do not promise it on operations that can allocate or invoke arbitrary user callbacks unless exceptions are handled inside.
#### Error boundaries
Catch and translate errors at a boundary that can decide what to do: a worker entry point, Lua binding, UI command handler or top-level application loop.
- A worker's exception does not automatically propagate to the thread that launched it.
- Never let an exception escape a C callback boundary that does not support it.
- Add context such as the operation, device and file path while preserving the original error.
- A destructor's cleanup cannot reliably report a successful media export; use an explicit checked finalization operation.
