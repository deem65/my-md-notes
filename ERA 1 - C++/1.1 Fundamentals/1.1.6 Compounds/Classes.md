#### Definition
A *class* is a custom defined type that can contain data members and member functions (yes, very similar to structs, with minor differences).
- The important distinction from `struct` is that class members are private by default:
```cpp
class player {
    int health{100}; // private
};

int main() {
    player p{};
    p.health = 50; // error
}
```
#### Access Specifiers 
There are three access modifiers:
- `public` accessible from outside the class
- `private` accessible from members and friends of the class
- `protected` accessible from members and friends, and certain derived class contexts
```cpp
class player {
private:
    int health{100};
public:
    void take_damage(int amount) {
        health -= amount;
    }
    int get_health() {
        return health;
    }
};
```
- An *invariant* is a condition that should remain true for an object whenever its public operations finish:
```cpp
class player {
private:
    unsigned int health{100};  // assuming health >= 0

public:
    void take_damage(int amount) {
        if (amount <= 0)
            return;

        if (amount >= health)
            health = 0;
        else
            health -= amount;
    }

    int get_health() {
        return health;
    }
};
```
You *could* create the exact same encapsulation using a `struct` with explicit `private:` and `public:`. However by convention:
- `struct` is used for data types with publicly accessible members
- `class` is used for encapsulated types with controlled interfaces ?? like i get what u mean but controlled interfaces sounds too complex of a term
#### Constructors
A *constructor* is a special member function used to initialize an object whenever it is created.
```cpp
class player {
private:
    int health;

public:
    player(int hp) {
        health = hp;
    }
};
int main() {
	player p{100}; // player() called
}
```
- A constructor have several important properties:
	- Its name matches the class name.
	- It has no return type, not even `void`.
	- It's called during object initialization.
	- It can accept parameters and be overloaded.
	- It cannot be declared `virtual`.
- There is a "better" way to initialize members called member initialization:
```cpp
class player {
private:
    int health;

public:
    player(int hp) : health{hp} {} // member initializer list
};
```
- The main distinction is not the syntax, but rather that not using member initialization initializes `health` before entering the constructor body and then assigns `hp` to it. Using member initializing directly initializes `health` with `hp`.
- The distinction becomes important for types which are expensive to default-construct, and for `const` members and references that must be initialized. 
- A *default constructor* is a constructor which can be called with no arguments: `player() : health{100} {}`
	- You can also explicitly request a compiler generated default constructor: `player() = defualt;`
	- When you declare a constructor yourself, the compiler no long implicitly generates a default constructor.
- It is important to note that members are initialized in their declaration order, not the order written in the initializer list. 
- Member objects are constructed before the containing class's constructor body executes.
- *Copy constructors* initialize a new object from an existing object of the same type:
	- They can be called like this: `player b = a;` 
	- If we haven't defined a copy constructor, the compiler implicitly declares one. 
	- You can also define it explicitly:
```cpp
player& operator=(const player& other) {
    health = other.health;
    return *this; // allows chained assignment (a = b = c)
}
```
- The compiler-generated copy constructor copies the pointer, not the allocated array:
```cpp
class buffer {
public:
    int* data{new int[10]{}};

    ~buffer() {
        delete[] data;
    }
};
class buffer_safe {
	std::array<int, 10> data{};
}
int main() {
	buffer a{};
	buffer b = a;

	// both point to &data 
	
	buffer_safe c{};
	buffer_safe d = c;
	
	// safe ?? why
}
```
#### Destructors 
A *destructor* is a special member function called whenever an object's lifetime ends. It is primarily used to release resources owned by that object:
```cpp
class player {
public:
    player() {
        std::cout << "constructed\n";
    }

    ~player() {
        std::cout << "destroyed\n";
    }
};

int main() {
    player p{}; // prints constructed
} // prints destroyed
```
- Destructors have several important properties:
	- Destructors have several properties:
	- Their name matches the class name, prefixed with `~`.
	- They have no return type.
	- They take no parameters and cannot be overloaded.
	- They are generally invoked automatically when the object's lifetime ends.
	- They can be `virtual`, unlike constructors.
- Destructors can be implicitly declared by the compiler. 
- The containing class's destructor body executes before its members are destroyed.
#### The Self Pointer
Every non-static member function has access to an implicit pointer named `this`, which points to the object on which the function was called.
```cpp
class player {
    int health{100};

public:
    void set_health(int health) {
        this->health = health;
    }
};
```
- The compiler understands that `health` refers to a member of the current object; you don't need to write `this` explicitly. 
#### Constant Member Functions
`const` member functions can't modify the object's non-`mutable` data members through `this`. 
```cpp
class Player {
    int health{100};

public:
    int get_health() const {
	    health = 50; // error
        return health;
    }
};
```
- A `const` member function treats `this` as a `const` pointer to `const`, whereas a non-`const` member function treats it as a `const` pointer to a mutable value:
	- `void foo()`            `this` type:      `example_class* const`
	- `void foo() const` `this` type:      `const example_class* const` 
- A `const` member function is useful because it is usable on `const` objects, because the compiler can't guarantee that calling a non-`const` function preserves the object's state:
```cpp
const player p{};

// this produces a comp error, however if
// assuming get_health is a const function, this works:
std::cout << p.get_health(); 
```
- Member functions that don't need to modify the object's state should usually be marked `const`.
#### Rule Of
*annoying* 
- **Rule of zero:** Prefer member types that manage resources themselves, avoiding custom special member functions.
- **Rule of three:** If a class needs a custom destructor, copy constructor, or copy assignment operator, it likely needs all three.
- **Rule of five:** Consider move construction and move assignment as well :-)
#### The `explicit` Keyword 
The constructor has some shenanigans: 
```cpp
class number {
    int value;

public:
    number(int n) : value{n} {}

    int get() const {
        return value;
    }
};

int main() {
	number a = 10; // works!
}
```
It works because the compiler implicitly converts `10` to `number` object using `number(int)`.
- This is called a *converting constructor*. 
- We can prevent this by marking the constructor as `explicit`:
```cpp
class number {
    int value;

public:
    explicit number(int n) : value{n} {}

    int get() const {
        return value;
    }
};
```
- When passing objects to function this becomes very important:
	- If a function is declared `void wait(seconds duration)`, without `explicit` this is legal: `wait(5)`. 
	- With `explicit seconds(int s) : value{s} {}`, you must write: `wait(seconds{5});`
- `explicit` also works for custom conversion operators: 
```cpp
class number {
    int value{};

public:
    explicit operator bool() const {
        return value != 0;
    }
};
int main() {
	number n{};
	bool a = n; // error
	bool b = static_cast<bool>(n); // works
	
	if (n) { // works
	}

}
```