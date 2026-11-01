*Initialization* establishes an object's initial state. *Assignment* changes an object that already exists.
C++ has several initialization forms whose behavior depends on the type being initialized.
#### Common syntax
| Declaration    | Form                                                                      |
| -------------- | ------------------------------------------------------------------------- |
| `int a;`       | Default initialization                                                    |
| `int b = 5;`   | Copy initialization                                                       |
| `int c(5);`    | Direct initialization                                                     |
| `int d{5};`    | Direct-list initialization                                                |
| `int e = {5};` | Copy-list initialization                                                  |
| `int f{};`     | Empty-list initialization, resulting in value initialization for this int |
#### Default initialization
```cpp
int global_count; // static storage: zero-initialized
int main() {
    int local_count; // no initialized value to read in C++20
}
```
Do not generalize `int local;` to every object and storage duration. A class type may run a default constructor; static-storage objects receive zero initialization before applicable later initialization.
For an ordinary uninitialized local integer in C++20, reading its value is undefined behavior.
#### Value and aggregate initialization
```cpp
int count{}; // zero
struct settings {
    int fps{60};
    int seconds{};
};
settings config{}; // aggregate initialization: fps 60, seconds 0
```
Empty braces do not always select the same initialization mechanism. For an aggregate, its elements follow aggregate rules. A user-provided default constructor can leave a fundamental member uninitialized if it does not initialize that member.
Use default member initializers when an object should have a clear default state.
#### Copy versus direct initialization
```cpp
class seconds {
public:
    explicit seconds(int value) : value_{value} {}
private:
    int value_;
};
seconds a{30};
seconds b(30);
// seconds c = 30; // explicit constructor cannot provide this conversion
```
Copy initialization does not necessarily perform a copy operation. Overload resolution and copy-elision rules determine the construction that actually occurs.
#### Narrowing
```cpp
int truncated(3.5); // conversion to 3
// int rejected{3.5}; // ill-formed narrowing in list initialization
```
List initialization rejects defined categories of narrowing conversion. This does not validate every semantic condition, such as whether a frame rate is supported by an encoder.
#### Braces and initializer lists
```cpp
#include <vector>
std::vector<int> repeated(3, 7); // three elements: 7, 7, 7
std::vector<int> listed{3, 7};   // two elements: 3, 7
```
Constructors taking `std::initializer_list` receive special preference during list initialization. Choose syntax that expresses the intended constructor.
#### Initialization order
Members initialize in declaration order, independently of their order in the constructor's initializer list. The constructor body executes afterward.
Initialize const members and references appropriately at construction; they cannot be repaired by ordinary assignment in the body.
`[[maybe_unused]]` can suppress an unused-entity diagnostic, but it does not initialize an object or make an invalid read safe.
