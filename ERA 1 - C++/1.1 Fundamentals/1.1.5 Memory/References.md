A *reference* is an alternate name for an existing object or function.
- `lvalue` references
	- An *lvalue reference* (`T&`) acts as an alias for an existing lvalue, as described before.
- `rvalue` references
	- An *rvalue reference* (`T&&`) is a reference that can bind to rvalues, including temporary objects. It becomes especially important for a later topic: move semantics.
	In this page, we will mostly discuss lvalues.
	
Lvalue reference types can be identified by using a single ampersand in the type specifier:
```cpp
	int   //normal int type
	int&  //an lvalue reference to an int object
```
**Term**: A type that specifies a reference is called a *reference type*. 
Types are represented as `T&`.
### Variables
- We can create an *lvalue reference variable* using a lvalue reference type. 
- From the compilers perspective, it doesn't matter whether the ampersand is attached to the reference type, but it's best practice to do so.
- We can also modify reference to modify the value of the object being referenced.
- All references must be initialized, using *reference initialization*. 
	- When a reference is initialized with an object, it is *bound* to that object.
	- The object being referenced is called the *referent*.
```cpp
	#include <iostream>
	
	int main() {
	    int x {5};    //x is a normal integer variable
	    int& ref {x}; //ref is an lvalue reference variable that can 
					  //now be used as an alias for variable x
		ref = 6;	  //modify the object via a reference		    
	    std::cout << x << std::endl;  //print the value of x (6)
	    std::cout << ref << std::endl; //print the value of x via ref (6)
	
	    return 0;
	}
```
- A non-const lvalue reference binds to a compatible, modifiable lvalue. It cannot bind to a temporary produced by an ordinary value conversion. A reference to a base class can also bind to a suitable derived object.
- References can't be changed to reference another object.
- References follow the same scope rules as normal variables.
- A reference and its referent normally have independent lifetimes. Some reference initializations extend a temporary object's lifetime; see [[Pointers]] and [[Value Categories unc]].
- **Term**: A *dangling reference* is a reference referencing an object which no longer exists. Accessing the destroyed object through it causes undefined behavior. 
- References are not objects, and so you can't have a reference to a reference, since an lvalue reference must reference an identifiable object.
### `const` and `constexpr` references
Since a non-const lvalue reference normally binds to a modifiable lvalue, if we want to have an lvalue reference to a const value we could use the `const` keyword to create a *const reference*.
- We can only use const references to refer to the referent but not modify it.
- Const references can also be bound to modifiable values. In that case the referent is treated as const when accessed through the reference. 
- It is best practice to favor lvalue references to const (const references) over lvalue references to non-const unless you need to modify the referent.
- When lvalue reference is found to a const with an rvalue, a temporary object is created and initialized with that value, and the const reference is bound to that temporary object.
- Const references can be bound to values of different types as long as they can be implicitly converted, however does the compiler does not covert it, it does it in these steps:
	1. The compiler creates a temporary object with the same type of the reference.
	2. Initializes it using the value.
	3. Binds the reference to the temporary.
- Note that trying to bind a non-const lvalue reference to a different type or to temporary object will result in compilation error. This is done to prevent hidden bugs, because any changes you make to the temporary object will be completely lost.
```cpp
	#include <iostream>
	
	int main() {
		char c {'a'}; 
		const int& x {c}; //temporary int made from c, x binds to that object
		
		std::cout << x << std::endl; //prints 97 on ASCII-compatible implementations 
	}
```
  - To create a const expression lvalue reference, we can use the `constexpr`. A constexpr lvalue reference can only be bound to object with a static duration, which means they can't be bound to non-static local variables. This is because the compiler needs to know where the objects are located. 
```cpp
	int global = 0;
	
	int main() {
		int local = 1;
		
		constexpr int& l_ref = local;  //error
		constexpr int& g_ref = global; //success
		
		return 0;
	}
```
- To define a constexpr reference to a const variable, we need to apply both `constexpr` and `const`. 
```cpp
	int main() {
		static const int x {6};
		constexpr const int& ref {x}; 
		
		return 0;
	}
```
### Pass by reference
To avoid copying an argument when calling a function we can use `pass by reference` instead of `pass by value`.  When using pass by reference we declare a function parameter as a reference type. 
*Pass by reference* allows us to pass a value to a function without copying it. 
- Additionally, pass by reference also allows us to change the value of an argument. Usually when an argument is passed by value, any changes will only affect the copied value. 
- Passing by non-const lvalue reference only accepts compatible, modifiable lvalue arguments.
```cpp
#include <iostream>
	void print_int(int& x) {
		std::cout << x << std::endl;
	}
	int main() {
		int x {5};
		print_int(x); //success
		
		const int y {5};
		print_int(y); //error: non-modifiable
		
		print_int(1); //error: rvalue
		
		return 0;
	}
```
- Unlike a reference to non-const (which can only bind to modifiable lvalues), a const reference can bind to modifiable lvalues, non-modifiable lvalues, and rvalues.
- A const reference lets a function inspect an object without copying it or modifying it through that reference. It can also accept a temporary, but must not retain a reference to that temporary beyond its lifetime.
- For read-only function parameters that only need to inspect string data and do not need ownership or null termination, `std::string_view` is often preferable over `const std::string&`.

`end on 24/09/2026`