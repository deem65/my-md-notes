*Scope* determines where a name can be accessed within a program.
A name must be visible at the point where it is used for the compiler to recognize it.
- Scope applies to names such as variables, functions, types, and namespaces.
- Different names can have different scopes.
- A name may be accessible in one part of a program but inaccessible in another.
- Scope is determined at compile time.
- Scope and *lifetime* are different concepts:
	- Scope determines where a name can be used.
	- Lifetime determines when an object exists.
#### Local scope
A name declared inside a block generally has *local scope*, meaning it is accessible from its declaration until the end of the enclosing block, subject to name hiding.
```cpp
#include <iostream>
int main() {
    int x{10};
    std::cout << x;
}
```
- `x` can be accessed after its declaration within the block.
- `x` cannot be accessed outside the block.
- Function parameters also have local scope.
- Local variables in nested blocks are not accessible from enclosing blocks.
```cpp
int main() {
    if (true) {
        int x{10};
    }
    // x is not accessible here
}
```
#### Block scope
A *block* is a sequence of statements enclosed in braces `{}`.
Names declared inside a block generally have *block scope*, a form of local scope.
```cpp
#include <iostream>
int main() {
    int x{10};
    {
        int y{20};
        std::cout << x;
        std::cout << y;
    }
    std::cout << x;
    // y is not accessible here
}
```
- Inner blocks can access names declared in enclosing blocks.
- Outer blocks cannot access names declared only in inner blocks.
- Each block introduces a new scope.
- Variables declared in a block are generally destroyed when execution leaves that block, unless they have a different storage duration.
#### Function parameters
Function parameters have their own scope associated with the function.
```cpp
#include <iostream>
void print_number(int x) {
    std::cout << x;
}
int main() {
    print_number(10);
    // x is not accessible here
}
```
- Parameters can be accessed inside the function body.
- Parameters cannot be accessed by name from the caller.
- Parameters of different functions can have the same name without conflicting.
- Parameters and local variables in the same function body cannot be redeclared with the same name in that outermost function block.
#### Global scope
A name declared outside all functions, classes, and namespaces belongs to the *global namespace scope*.
```cpp
int count{0};
void increment() {
    ++count;
}
int main() {
    increment();
}
```
- Names declared at global namespace scope can be accessed from functions when visible.
- Global variables have static storage duration.
- Global functions can be called from different functions when their declarations are visible.
- Global namespace members can be qualified using `::`.
```cpp
#include <iostream>
int x{10};
int main() {
    int x{20};
    std::cout << x;   // 20
    std::cout << ::x; // 10
}
```
The `::` operator is called the *scope resolution operator*.
- `::x` refers to `x` in the global namespace.
- Unqualified `x` refers to the local variable.
- Prefer avoiding unnecessary non-constant global variables because they introduce shared state.
#### Name hiding
*Name hiding* occurs when a declaration in an inner scope hides a declaration with the same name in an enclosing scope.
```cpp
#include <iostream>
int x{10};
int main() {
    int x{20};
    {
        int x{30};
        std::cout << x; // 30
    }
    std::cout << x; // 20
}
```
- The innermost matching declaration generally takes precedence during unqualified name lookup.
- The outer declaration still exists but is hidden.
- The hidden global variable can be accessed using `::x`.
- Avoid reusing names across nested scopes when doing so makes code harder to understand.
#### Namespaces
A *namespace* is a declarative region used to organize names and prevent naming conflicts.
```cpp
namespace math {
    int add(int x, int y) {
        return x + y;
    }
}
int main() {
    int result{math::add(5, 3)};
}
```
- Namespaces group related functions, types, and variables.
- Different namespaces can contain entities with the same name.
- The scope resolution operator `::` is used to access namespace members.
- Namespaces can be nested.
```cpp
#include <iostream>
namespace game {
    namespace player {
        int health{100};
    }
}
int main() {
    std::cout << game::player::health;
}
```
Since C++17, nested namespaces can be declared more concisely:
```cpp
namespace game::player {
    int health{100};
}
```
#### Using declarations
A *using declaration* introduces a name from another namespace into the current declarative region for unqualified lookup.
```cpp
#include <iostream>
int main() {
    using std::cout;
    cout << "Hello";
}
```
- `using std::cout;` allows `cout` to be used without `std::`.
- It introduces a specific name rather than every name from the namespace.
- Its effect on unqualified lookup depends on the scope where it is declared.
#### Using directives
A *using directive* makes names from a namespace available for unqualified name lookup.
```cpp
#include <iostream>
using namespace std;
int main() {
    cout << "Hello";
}
```
- `using namespace std;` allows unqualified lookup to consider names from `std`.
- It can introduce naming ambiguities.
- Avoid using directives in header files because they affect files that include those headers.
- Prefer explicit qualification (`std::cout`) or selective using declarations.
#### Class scope
Names declared inside a class or struct have *class scope*.
```cpp
#include <iostream>
struct player {
    int health{100};
    void take_damage(int amount) {
        health -= amount;
    }
};
int main() {
    player player{};
    player.take_damage(20);
    std::cout << player.health;
}
```
- `health` and `take_damage()` are members of `Player`.
- Member functions can access the class's members according to access rules.
- Members can be accessed through objects using `.` or pointers using `->`.
- The scope resolution operator can also identify class members.
```cpp
struct player {
    void print_health();
    int health{100};
};
void player::print_health() {
    std::cout << health;
}
```
- `player::print_health()` defines a member function outside the class definition.
- Class scope is distinct from an object's lifetime.
- Access specifiers such as `public` and `private` control accessibility, not the scope of the member name.
#### Scope and lifetime
*Scope* and *lifetime* are related but independent concepts.
```cpp
void counter() {
    static int count{0};
    ++count;
    std::cout << count;
}
```
- `count` has local scope.
- `count` has static storage duration.
- Its name is accessible only inside `counter()`.
- Its object persists throughout the program after initialization.
Another example:
```cpp
int* create_number() {
    return new int{10};
}
```
- The dynamically allocated `int` continues to exist after the function returns.
- Its lifetime is not controlled by the scope of the local pointer or the calling function.
- The allocated object must eventually be destroyed, typically through an owning RAII type rather than manual memory management.
#### Best practices
- Declare variables in the smallest scope that needs them.
- Prefer local variables over global variables.
- Keep blocks and functions reasonably small to make name visibility easier to understand.
- Avoid hiding variables with the same name in nested scopes.
- Use namespaces to organize related declarations and prevent naming collisions.
- Prefer `std::name` over `using namespace std;`.
- Avoid using directives in headers.
- Remember that scope determines name visibility, while lifetime determines object existence.