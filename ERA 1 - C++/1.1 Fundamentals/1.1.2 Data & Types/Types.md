Memory consists of bits, typically grouped into 8-bit bytes.  
A data _type_ tells the compiler how to interpret the bits stored in memory.
C++ types are classified into two main categories:
1. Fundamental data types
2. Compound data types
The C++ standard library also provides additional types, most of which are compound types.
#### Fundamental data types
_Fundamental data types_ (also called _built-in types_ or _primitive types_) are types provided directly by the C++ language, rather than being defined in terms of other types
- Fundamental types include:
    - **Void type:**  
        - `void` -- represents the absence of a value.      
    - **Integral types:**   
        - `bool` -- represents `true` or `false`.    
        - `char` -- represents a character or small integer value.        
        - `wchar_t`, `char8_t`, `char16_t`, `char32_t` -- character types used for different character representations.    
        - `short`, `int`, `long`, `long long` -- integer types.    
        - Signed and unsigned integer variants.    
    - **Floating-point types:** 
        - `float` -- single-precision floating-point type.     
        - `double` -- double-precision floating-point type.    
        - `long double` — extended floating-point type, whose precision depends on the implementation.    
    - **Null pointer type:**
        - `std::nullptr_t` -- the type of `nullptr`.    
- Fundamental types are built into the language and do not require user-defined declarations.
- The size and range of many fundamental types depend on the implementation.
- `sizeof` can be used to determine the size of a type in bytes.
```cpp
int age{20};
double pi{3.14159};
bool is_ready{true};
char letter{'A'};
```
#### Compound data types
_Compound data types_ (also called *composite data types*) are types that are defined in terms of other existing data types.  
They have additional properties or behavior that make them useful for solving more complicated problems.
- Every data type is either a fundamental data type or a compound data type.
- In C++ there are the following compound types:
    - Functions 
    - C-style arrays   
    - Pointer types:
        - Pointers to objects    
        - Pointers to functions    
    - Pointer-to-member types:
        - Pointers to data members    
        - Pointers to member functions    
    - Reference types:
        - Lvalue references    
        - Rvalue references    
    - Enumerated types:
        - Unscoped enumerations    
        - Scoped enumerations    
    - Class types:
        - Structs    
        - Classes    
        - Unions    
- The type of the function `void do_something(int x, double y)` is `void(int, double)`.
- This function type is composed using other types, making it a compound type.
- **Term:** A _class type_ is a type defined as a `struct`, `class`, or `union`.
#### Standard library types
_Standard library types_ are types provided by the C++ standard library to simplify common programming tasks.
- Unlike fundamental types, standard library types generally require including the appropriate header.
- Most standard library types are class types and therefore compound types.
- Common examples include:
    - `std::string` — represents a sequence of characters.
    - `std::vector` — dynamically sized array.
    - `std::array` — fixed-size array.
    - `std::optional` — represents a value that may or may not exist.
    - `std::pair` — stores two values together.
    - `std::unique_ptr` — smart pointer with exclusive ownership.
- Standard library types often provide additional functionality through member functions and operators.
- Some standard library names, such as `std::size_t`, are aliases for fundamental types rather than new class types.
```cpp
#include <string>
#include <vector>

std::string name{"David"};
std::vector<int> numbers{1, 2, 3};
```
- `std::string` and `std::vector<int>` are standard library class types.
- Standard library types are not a separate fundamental category of the C++ type system.
#### Fixed-width and size types
```cpp
#include <cstddef>
#include <cstdint>
std::uint32_t frame_count{};
std::uint64_t byte_count{};
std::size_t element_count{};
```
Fixed-width integer aliases exist when the implementation supports the exact widths. `std::size_t` is the unsigned type returned by `sizeof` and used for object-size-related counts.
An unsigned type does not make subtraction safe for negative results. Check relationships before subtracting sizes or converting a signed API value.
Use explicit units in names: `frame_count`, `byte_count`, `duration_ms` and `row_pitch_bytes` describe different quantities.
#### Type aliases
```cpp
using byte_count = std::size_t;
```
An alias gives an existing type another name. It does not create a distinct type that prevents mixing bytes and elements.
Use a small struct when those quantities must be different types.
#### Type deduction
```cpp
const int original{10};
auto copy{original};             // int
auto& reference{original};       // const int&
const auto& view{original};      // const int&
decltype(original) same{20};     // const int
```
By-value `auto` commonly drops top-level const and references. `auto&` expresses borrowing.
For an unparenthesized name, `decltype` has a special rule that yields the entity's declared type. In other expressions, value category affects whether its result is a reference type; `decltype((original))` is `const int&` here.
Use deduction when the initializer makes the type clear. At binary/API boundaries, explicit types and units can improve reviewability.
