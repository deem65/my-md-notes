#### 1. Define the Problem
Before writing code, determine exactly what the program should accomplish.
- Identify the expected **inputs** and **outputs**.
- Define the requirements and constraints.
- Consider possible edge cases and failure conditions
#### 2. Design a Solution
Plan how the program will solve the problem.
#### 3. Write the Source Code
Implement the solution using C++.
C++ projects commonly contain two types of files:
- **Source files (`.cpp`)** : Contain function definitions, implementations, and other code.
- **Header files (`.h` / `.hpp`)**: Commonly contain declarations, class definitions, and interfaces shared between source files.
Programs may also depend on the C++ standard library and third-party libraries.
#### 4. Build the Program
Building converts source code into a program that can be executed.
The process consists of several stages.
###### 4.1. Preprocessing
Before compilation, the *preprocessor* processes directives beginning with `#`.
Common directives include:
- `#include` — Includes the contents of another file.
- `#define` — Defines a macro.
- `#ifdef` / `#ifndef` — Conditionally includes code.
- `#pragma once` — Commonly prevents a header from being included multiple times within a translation unit.
A translation unit consists of the source file and the contents of included files after preprocessing.
###### 4.2. Compilation
The *compiler* processes each translation unit independently.
Its responsibilities include:
1. **Lexical analysis**: Converts source code into tokens.
2. **Syntax analysis**: Checks whether the tokens follow C++ grammar.
3. **Semantic analysis**: Checks types, declarations, and other language rules.
4. **Optimization**: May transform code to improve performance or reduce size.
5. **Code generation**: Produces machine code, typically stored in an object file:
	- `.obj` on Windows.
	- `.o` on Unix.
```text
main.cpp    > main.obj
capture.cpp > capture.obj
config.cpp  > config.obj
```
Each `.cpp` file is normally compiled separately.
This is called **separate compilation**.
One important advantage is that changing one source file does not necessarily require recompiling every other source file.
###### 4.3. Linking
After compilation, the *linker* combines object files and required libraries into the final executable.
- **Symbol resolution** determines which function or variable a reference refers to.
    - Example: `main.obj` calls `add()`, and the linker finds its definition in `math.obj`.
- **Relocation** adjusts compiled references so they point to the correct memory locations from the machine code.
    - Example: An instruction calling `add()` is adjusted to reach its location at `0x2000`.
- **Library linking** incorporates or references code from libraries.
- **Executable generation** produces the final executable file.
##### Compiler Errors vs. Linker Errors
*Compiler error* occur when a translation unit cannot be compiled successfully.
- Invalid syntax.
- Incompatible types.
- Undeclared identifiers.
*Linker errors* occur when the compiled object files cannot be linked successfully.
- A function was declared and used but never defined.
- Multiple conflicting definitions of the same symbol.
- A required library is missing.

`end 07/10/2026`
#### Symbols and linkage
A *symbol* identifies an entity, such as a function, that object files and libraries may need to refer to during linking.
- A declaration tells a translation unit that an entity exists and describes its type.
- A definition supplies the function body or object definition required by the program.
- External linkage permits corresponding declarations in different translation units to refer to the same entity.
- Internal linkage limits that relationship to a translation unit.
- Scope controls where a name can be used; linkage controls which declarations identify the same entity.
C++ implementations commonly encode type/signature information into symbol names. This is called *name mangling*.
Calling conventions, namespace/class membership and parameter types can change the expected symbol. A function accidentally declared as a class member does not refer to an unrelated Windows API function with the same spelling.
#### Libraries
A static library contains object code that can be incorporated into the executable. A Windows import library commonly provides the information needed to call functions from a DLL loaded at runtime.
A header declaring a function does not supply its linked implementation. Include the header and link the library specified by the API documentation.
Do not mix x86 and x64 object code. Also check compiler/runtime compatibility when incorporating prebuilt C++ libraries.
#### Debug and Release
Debug and Release are build configurations, not different languages.
- Optimization, debug information, assertions and runtime-library choices can differ.
- Optimized programs can still contain debug symbols and be debugged.
- Undefined behavior can appear to change when optimization changes.
- Compare recorder performance using a representative optimized build.
