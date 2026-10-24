A *struct* is a user-defined class type that groups related data and functionality under a single type.
Structs are useful for representing objects or concepts that consist of multiple related values.
- A struct is defined using the `struct` keyword.
- The variables declared inside a struct are called *data members*.
- Functions declared inside a struct are called *member functions*.
- Each object of a struct type contains its own non-static data members.
- Structs can contain members of different types.
- In C++, structs and classes are almost identical. Their main differences are their default access and inheritance permissions.
#### Defining structs
A *struct definition* introduces a new user-defined type and specifies its members.
```cpp
struct Player {
    int health;
    double speed;
    bool alive;
};
```
- `Player` is the name of the struct type.
- `health`, `speed`, and `alive` are data members.
- The struct definition ends with a semicolon.
- Defining a struct type does not create an object of that type.
- Objects can be created after the struct has been defined.
```cpp
Player player{};
```
Here, `player` is an object of type `Player`.
#### Struct initialization
Struct objects can be initialized using list initialization.
```cpp
struct Player {
    int health;
    double speed;
    bool alive;
};
int main() {
    Player player{100, 5.5, true};
}
```
For an *aggregate struct*, the initializer values correspond to the members in declaration order.
- `100` initializes `health`.
- `5.5` initializes `speed`.
- `true` initializes `alive`.
- The initializer types must be compatible with their corresponding member types.
- List initialization rejects narrowing conversions.
If fewer initializers are provided, the remaining members are initialized according to the aggregate initialization rules.
```cpp
Player player{100}; // speed = 0.0, alive = false
```
An empty initializer list can be used to initialize all members:
```cpp
Player player{}; // health = 0, speed = 0.0, alive = false
```
**Important:** Not every struct is an aggregate. Adding certain features, such as a user-declared constructor, can prevent aggregate initialization.
#### Member access
The *member access operator* (`.`) is used to access members through an object.
```cpp
#include <iostream>
struct Player {
    int health;
    double speed;
};
int main() {
    Player player{100, 5.5};
    std::cout << player.health;
    player.health = 75;
}
```
- `player.health` accesses the `health` member of `player`.
- `player.speed` accesses the `speed` member.
- Members can be read or modified according to their type and access permissions.
- Each struct object maintains its own data members.
```cpp
Player first{100, 5.5};
Player second{50, 3.0};
first.health = 80; // does not modify second.health
```
#### Member access through pointers
The *arrow operator* (`->`) accesses a member through a pointer to an object.
```cpp
struct Player {
    int health;
};
int main() {
    Player player{100};
    Player* ptr{&player};
    ptr->health = 50;
}
```
- `ptr` points to `player`.
- `ptr->health` accesses the member of the pointed-to object.
- `ptr->health` is equivalent to `(*ptr).health` for an ordinary pointer.
- The pointer must refer to a valid object before its members are accessed.
```cpp
(*ptr).health = 75; // equivalent to ptr->health = 75
```
Parentheses are necessary because `.` has higher precedence than unary `*`.
#### Default member initializers
A *default member initializer* specifies a default value for a data member directly inside the struct definition.
```cpp
struct Player {
    int health{100};
    double speed{5.0};
    bool alive{true};
};
int main() {
    Player player{};
}
```
- `player.health` is initialized to `100`.
- `player.speed` is initialized to `5.0`.
- `player.alive` is initialized to `true`.
- Default member initializers are used when no explicit initializer overrides them.
For example:
```cpp
Player player{50, 7.5};
```
- `health` is initialized to `50`.
- `speed` is initialized to `7.5`.
- `alive` uses its default member initializer, `true`.
Default member initializers reduce repeated initialization code and help ensure objects begin in a meaningful state.
#### Designated initializers
C++20 introduced *designated initializers* for aggregate types.
They allow members to be initialized explicitly by name.
```cpp
struct Player {
    int health{};
    double speed{};
    bool alive{};
};
int main() {
    Player player{
        .health = 100,
        .speed = 5.5,
        .alive = true
    };
}
```
- Designated initializers identify the member being initialized.
- Designators must follow the order in which members are declared.
- Members without designated initializers are initialized using their default member initializer or the applicable aggregate initialization rules.
- Designated initializers can improve readability when structs contain many fields.
Unlike C, standard C++ does not allow designated initializers to appear in an arbitrary order.
#### Passing structs to functions
Structs can be passed to functions using the same parameter-passing methods as other types.
**Pass by value:**
```cpp
struct Player {
    int health{};
};
void damage(Player player) {
    player.health -= 20;
}
int main() {
    Player player{100};
    damage(player);
    // player.health is still 100
}
```
The value parameter is a separate object. Use `Player&` to modify the caller's object or `const Player&` to inspect it without copying.
```cpp
void damage_original(Player& player) { player.health -= 20; }
int read_health(const Player& player) { return player.health; }
```
#### Returning structs
```cpp
struct dimensions {
    int width{};
    int height{};
};
dimensions make_dimensions() {
    return dimensions{2560, 1440};
}
```
Returning an object by value gives the caller a result with its own lifetime. C++17 prvalue construction can avoid an intermediate copy entirely.
Do not return a reference to a local struct that will be destroyed when the function returns.
#### Composition and nested types
```cpp
struct capture_settings {
    dimensions resolution{2560, 1440};
    int fps{60};
    struct limits {
        int max_seconds{120};
    };
    limits maximum{};
};
```
`resolution` is a member object. `limits` is a nested type; `maximum` is an object of that type.
Nested type names use `capture_settings::limits`. Access nested member objects through expressions such as `settings.resolution.width`.
#### Member functions
```cpp
struct counter {
    int value{};
    void increment() { ++value; }
    int get() const { return value; }
};
```
Structs support member functions, constructors, destructors, static members and inheritance like classes. Shared language features are described in [[Classes unf]].
#### Aggregates
In C++20, a class aggregate has no user-declared or inherited constructors, no private/protected direct non-static data members, no virtual functions, and no virtual/private/protected bases.
Default member initializers and ordinary member functions do not by themselves prevent aggregate status.
Adding a user-declared constructor, including one declared `= default`, can therefore change initialization behavior.
#### Layout
Members occupy storage within the containing object. Alignment can introduce padding between members and after the last member.
```cpp
struct example {
    char kind;
    int value;
    char flag;
};
```
On a common platform this may occupy 12 bytes rather than the sum 6. The exact result is implementation-dependent.
Never serialize an arbitrary struct by assuming its in-memory layout is a portable file format. See [[Object Representation]].
#### Distinct types
```cpp
struct width { int value; };
struct height { int value; };
```
These are distinct types even though their members match. They can prevent mixing quantities accidentally, unlike two aliases of `int`.
#### Best practices
- Use a struct for a clear group of related data with intentionally accessible members.
- Initialize all members meaningfully.
- Use a class interface when operations must enforce invariants.
- Borrow larger objects for inspection and return newly produced values by value.
