#### Definition
A *struct* is a custom defined type that grouped multiple members into a single type. 
```cpp
// declaration
struct player{
	int health;
	float speed;
	bool is_alive{true}; // default value
};
int main() {
	// initialization 
	player p{100, 4.5f, true};
	player* ptr = &p;

	// member access
	p.health = 80;
	std::cout << p.speed;

	ptr->is_alive = false;

	// aggregation 
	player zero_initializated_p{}; // 0, 0.0f, true
	player half_initializated_p{100}; // 100, 0.0f, true

	// designated initializers (C++20)
	// must be written in declaration order
	player p{
		.health = 100,
		.speed = 4.5f,
		.is_alive = true
	};

	// structs support copy initialization
	player p2 = p;
}
 
```
#### Padding
Structs members are not necessarily stored without gaps. There are multiple reasons for this:
1. Members require alignment based on their type. An `int` for example requires a 4 byte alignment (on most systems), meaning `x % 4 == 0` after beginning of the struct. 
2. Structs typically have x byte alignment requirement themselves, where x is their most strictly aligned member (highest `alignof(x)`), so their total size is rounded up to x.
A practical optimization is to group members with larger alignment requirements together, although it is often unnecessary and code readable is usually more important. 
- `alignof()` operator checks the alignment requirement of a type or value.
- `offsetof()` operator checks memory position offsets from struct to member. 
```cpp
struct data{
	char x; // byte 0
	// 1 - 7 padding
	double y; // bytes 8-15
	int z; // bytes 16-19
	// trailing padding 20-23
}
std::cout << sizeof(data); // print 24
```
#### Member Functions
A struct isn't limited to storing data. A struct can also have member functions.
```cpp
struct player{
	int health{100};
	
	void take_damage(int amount) { health -= amount; }
};
int main(){
	player p{};
	p.take_damage(25);
	
	std::cout << p.health; // print 75
}
```
- In C++ structs and classes have nearly identical capabilities. The main differences are their member access and default inheritance access.
	- `struct` public by default
	- `class` private by default
#### Nesting
```cpp
struct position {
    float x{};
    float y{};
};

struct player {
    int health{100};
    position pos{}; // nesting / composition
};

int main() {
    player p{};
    p.pos.x = 50.0f; // epic
}
```