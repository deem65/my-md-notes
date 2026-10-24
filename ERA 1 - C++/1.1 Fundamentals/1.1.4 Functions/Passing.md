There are three main ways to pass a parameter to a function:
### By Value
When passing *by value*, the function receives a **copy** of the argument.
Changes made to the parameter inside the function do not affect the original variable.

```cpp
#include <iostream>

void change_value(int x) {
    x = 10;
}
int main() {
    int number = 5;
    change_value(number);
    std::cout << number; // 5
    return 0;
}
```
#### Advantages
- Simple and safe.
- The function cannot accidentally modify the original argument.
- Good for small types such as `int`, `bool`, `char`, and `float`.
#### Disadvantages
- A copy must be created.
- Copying large objects can be expensive
---
### By Reference
When passing *by reference*, the parameter becomes an **alias** for the original variable.

Changes made to the parameter affect the original variable.
```cpp
#include <iostream>

void change_value(int& x) {
    x = 10;
}
int main() {
    int number = 5;
    change_value(number);
    std::cout << number; // 10
    return 0;
}
```
#### Advantages
- No copy is required.
- The function can modify the original object.
- Useful when passing large objects.
#### Disadvantages
- The function may modify the argument, which can make code harder to reason about if this is not expected.
---
### By Address
When passing *by address*, the function receives a **pointer containing the address of the original variable**.
In modern C++, pass by address is usually described as passing a pointer by value.

```cpp
#include <iostream>

void change_value(int* x) {
    *x = 10;
}

int main() {
    int number = 5;
    change_value(&number);
    std::cout << number; // 10

    return 0;
}
```
#### Advantages
- No copy of the original object is required.
- The function can modify the original object.
- A pointer can represent the absence of an object using `nullptr`.
#### Disadvantages
- Pointers can be `nullptr`.
- Dereferencing an invalid pointer causes undefined behavior.
- Pointer syntax is usually less convenient than references.
---
## When to Use Each
- Use **pass by value** when:
	- The type is small.
	- You do not need to modify the original argument.
	- Copying is inexpensive.
- Use **pass by reference** when:
	- The function intentionally needs to modify the original object.
- Use **pass by address** when:
	- A pointer naturally represents the object.
	- `nullptr` is a meaningful possible value.
	- You are working with pointer-based APIs or data structures.
---
You can also use the `const` keyword to set your reference to read only.
Note that pass by address is essentially pass by address by value, if needed, you can use pass by address by reference with passing a pointer by reference.
### Summary

```cpp
void by_value(int x);      // receives a copy
void by_reference(int& x); // refers to original
void by_address(int* x);   // receives original address
```

`end 26/09/2026`