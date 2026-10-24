A *header file* is a file that typically contains declarations intended to be shared between multiple source files.
- Header files usually have the `.h` or `.hpp` extension, while source files typically use `.cpp`.
- Header files allow declarations to be reused across multiple source files.
- They help separate the *interface* of a program from its *implementation*.
- Header files are included using the `#include` preprocessor directive.
- Header files generally contain declarations, while source files contain definitions.
- The compiler processes each source file independently, along with its included headers.
For example, consider a function defined in a separate source file.
**math.h**
```cpp
#pragma once

int add(int x, int y);
```
**math.cpp**
```cpp
#include "math.h"

int add(int x, int y) {
    return x + y;
}
```
**main.cpp**
```cpp
#include <iostream>
#include "math.h"

int main() {
    std::cout << add(5, 3);
}
```
- `math.h` declares the function `add()`.
- `math.cpp` defines the function `add()`.
- `main.cpp` includes the header to make the declaration available.
- The linker connects the function call in `main.cpp` to its definition in `math.cpp`.
- Header files do not need to have a corresponding source file.
**Term:** A *declaration* introduces a name and its type to the compiler, while a *definition* provides the entity itself.
#### The `#include` directive
The `#include` directive instructs the preprocessor to include the contents of another file.
```cpp
#include <iostream>
#include "math.h"
```
There are two common forms:
- `#include <header>` — typically used for standard library and external library headers.
- `#include "header"` — typically used for project-specific headers.
The preprocessor replaces each `#include` directive with the contents of the specified file before compilation.
- Including a header does not automatically compile its corresponding `.cpp` file.
- Each `.cpp` file must be compiled and linked as part of the project.
- The same header may be included by multiple source files.
- The compiler must see the necessary declarations before they are used.
- The reason for when we include for example `windows.h` and use the function and it compiles, it's because the compiler does not need the function's definition to call it. The linker is responsible, for connecting the function call to its implementation. How does the linker find it?
	 - The linker searches the symbol tables of compiled object files (`.obj`) and specified libraries (`.lib`) for a matching function symbol.
	 - A *symbol* is a name that identifies an entity, such as a function or variable, in compiled code.
	 - If the function is defined in another object file or static library, the linker resolves the reference to that definition.
	 - For Windows API functions, such as `MessageBoxW()`, the linker uses an *import library* (e.g. `User32.lib`) to establish a reference to the DLL containing the implementation (`User32.dll`).
	  - At runtime, the Windows loader loads the DLL and resolves the function's actual memory address.
	  - If the linker cannot resolve a required symbol, it produces an *unresolved external symbol* error (`LNK2019`).
#### Header guards
A *header guard* prevents the contents of a header from being included more than once within the same translation unit.
Without header guards, including the same header multiple times can cause redefinition errors.

```cpp
#ifndef MATH_H
#define MATH_H

int add(int x, int y);

#endif
```
- `#ifndef` checks whether a macro has not been defined.
- `#define` defines the macro.
- `#endif` ends the conditional preprocessing block.
- If the header is included again, its contents are skipped.
- Header guard identifiers should be unique across the project.
An alternative is `#pragma once`:
```cpp
#pragma once

int add(int x, int y);
```
- `#pragma once` instructs the compiler to process the header only once per translation unit.
- It is supported by major C++ compilers.
- Unlike traditional header guards, `#pragma once` is not formally standardized.
- Both approaches serve the same general purpose.
#### Translation units
A *translation unit* consists of a source file after preprocessing, including the contents of all included headers.
```cpp
// main.cpp
#include "math.h"

int main() {
    return add(2, 3);
}
```
After preprocessing, the translation unit effectively contains:
```cpp
int add(int x, int y);

int main() {
    return add(2, 3);
}
```
- Each source file produces its own translation unit.
- Translation units are compiled independently.
- The linker combines the resulting object files into an executable or library.
- Including a header in multiple source files does not automatically create multiple definitions.
#### The One Definition Rule (ODR)
The *One Definition Rule* specifies restrictions on how many definitions of an entity may appear in a program.
Ordinary non-inline functions and variables with external linkage should not be defined in headers included by multiple source files.
**Incorrect:**
```cpp
// math.h
#pragma once

int add(int x, int y) {
    return x + y;
}
```
If multiple source files include this header, the program may contain multiple definitions of `add()`.
**Correct:**
```cpp
// math.h
#pragma once

int add(int x, int y);
```
```cpp
// math.cpp
#include "math.h"

int add(int x, int y) {
    return x + y;
}
```
Some definitions are allowed in headers under specific rules:
- `inline` functions.
- Function templates.
- Class and struct definitions.
- `inline` variables (C++17).
- `constexpr` functions, which are implicitly inline.\
These definitions must satisfy the applicable ODR requirements.
#### Header dependencies
A *header dependency* occurs when one header requires declarations or definitions from another header.
```cpp
#pragma once

#include <string>

struct Player {
    std::string name;
    int health;
};
```
- Include the headers required by the declarations and definitions in the current header.
- Do not rely on another file to include dependencies first.
- Avoid unnecessary includes because they increase compilation dependencies.
- Forward declarations can sometimes replace includes when a complete type is not required.
For example:
```cpp
// player.h
#pragma once

class Weapon;

void equip_weapon(Weapon& weapon);
```
A *forward declaration* introduces a type without providing its complete definition.
- References and pointers to an incomplete class type can often be declared without including its full definition.
- Creating an object by value generally requires the complete type.
- Accessing class members generally requires the complete definition.
#### Best practices
- Use headers to share declarations between source files.
- Keep ordinary function definitions in `.cpp` files unless they are intended to be defined in headers.
- Add header guards or `#pragma once` to project headers.
- Include the corresponding header first in its `.cpp` file to help detect missing dependencies.
- Include only the headers that are needed.
- Prefer including a header directly rather than relying on transitive includes.
- Avoid `using namespace std;` in header files because it affects every file that includes them.
- Avoid defining non-inline global variables in headers.
- Keep headers self-contained so they can be included independently.