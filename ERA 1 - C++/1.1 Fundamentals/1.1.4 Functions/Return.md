Returning by value gives the caller a value of its own. Depending on the expression and type, this can construct directly in the destination, move, or copy.
For a class type, the cost depends on its constructors and whether copy elision applies. Returning by value is usually the clearest choice when producing a new result.
#### By Reference
```cpp
	const std::string& get_name() {
		static const std::string name{"david"};
		return name;
	}
```
- If you return an object by reference, the object must still exist after the function ends, which is why `static` is used here.
- But don't return mutable static variables casually:
```cpp
#include <iostream>
	const int& get_next_id() {
		static int x{0};
		++x;
		return x;
	}
	int main() {
		const int& id1{get_next_id()}; //x = 1
		const int& id2{get_next_id()}; //x = 2
		
		///22 because both reference point at the same x
		std::cout << id1 << id2;
		return 0;
	}
```
- Returning non-const references lets the caller modify things:
```cpp
	int& max(int& x, int& y) {
		return (x > y) ? x : y;
	}
	int main() {
		int a{5};
		int b{6};
		//since max return b by ref, 
		//you essentially modify b to 7
		max(a, b) = 7; 	//b = 7
		return 0;
	}
```
#### By Address
- Returning by address is the same concept, except since a pointer is a value which represents an address, you can return `nullptr` which signifies "I didn't find anything" to the function caller:
```cpp
	Student* find_student(std::string_view name) {
		if (found)
			return &student;
		return nullptr;
	}
```
---
- Return by value: `T foo();`
	- Produces a value for the caller.
	- May construct directly, move, or copy; it does not always copy.
- Return by reference: `T& foo();`
	- Returns a reference to an existing object.
	- Must exist after the function ends.
- Return by address: `T* foo();`
	- Returns a pointer by value.
	- Can return `nullptr` if no object is found.
#### In and out parameters
A function can use a parameter in one of three ways:
- In: Parameters who are only used to receive input from the caller.
- Out: A function parameter who is only used to return information back to the caller. They have a few downsides:
	- The caller must instantiate the object and pass them as arguments, even when it doesn't intend to use them, these object must be made to be assigned to, which means they can't be const. 
	- The caller must pass in objects, these values can't be used as temporaries/rvalues. 
	- When passed by reference they don't make it obvious the argument will be modified.
- It is best practice to avoid out parameters unless necessary.
- When a function actually uses the value before overwriting its value, the parameter is called an in-out parameter. 
- When passing by reference in order to avoid making a copy of an argument, you should always prefer passing by const reference.
- The following code makes the change visible at the call site. Constructing `foo` from `in` copies it. Returning the local can use copy elision or a move, and assigning the result can use move assignment. There is no fixed rule that this requires two extra copies.
```cpp
	struct Foo {
		int value{};
	};
	Foo do_something(const Foo& in) {
		Foo foo {in}; //copy
		++foo.value;
		return foo;
	}
	int main() {
		Foo foo{};
		foo = do_something(foo); //make it obvious
		
		return 0;
	}
```
Final mental model: 
- In param: use 
- Out param: change for the caller
- In-out param: use, change for the caller
---
#### Type deduction drops references
In addition to type deduction dropping const, it also drops references:
```cpp
	#include <string>
	
	std::string& get_ref();
	
	int main() {
		auto fake_ref {get_ref()}; //type std::string
		auto& real_ref {get_ref()}; //type std::string&
		return 0;
	}
```
- `constexpr` is not part of an expression type, so it's not deducted by `auto`.
Unlike references, type deduction does not drop pointers:
```cpp
	#include <string>
	
	std::string* get_ptr();
	
	int main() {
		auto ptr{get_ptr()}; //type std::string*
		auto* explicit_ptr{get_ptr()}; //same pointer type, made clearer
		return 0;
	}
```
---
#### Top level const and low level const
A **top-level const** is a const qualifier that applies to an object itself. For example: 
1. `const int x{0};` Applies to `x`
2. `int* const ptr` Applies to `ptr`
A **low-level const** is a const qualifier that applies to the object being referenced or pointed to:
3. `const int& ref` Applies to object being referenced
4. `const int* ptr` Applies to object being pointed at