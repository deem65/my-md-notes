Unconditional jumps are implemented via `goto` statements. The spot to switch the jump will go to is marked with a *statement label*.
- Statement labels are conventionally not intended. 
```cpp
	#include <iostream>
	
	int main() {
		int x{};
	tryAgain: //statement label
		std::cout << "enter the number 5";
		std::cin >> x;
		if (x != 5) {
			goto tryAgain; //goto statement
		}
		
		return 0;
	}
```
- Statement labels have function scope. The `goto` statement and its corresponding statement label must appear in the same function. 
- `goto` statements can also jump forward in the code.
- `goto` statements are usually not used in programming languages. The primary problem is that they allow the programmer to jump around the code arbitrarily. This can create spaghetti code, making it extremely hard to follow. 
- Almost every situation where `goto` is used can be clearly written with other statements in C++.
- A notable exception, however, is when you need to exit a nested loop but not the entire function. In such case, `goto` might be the cleanest solution. 
#### Related statements
###### Break
The `break` statement immediately terminates the nearest closing loop or `switch`.
```cpp
	for (int i{0}; i < 10; ++i) {
	    if (i == 5)
	        break;
	    std::cout << i << ' ';
	} //output 0 1 2 3 4
```
- Works with `for`, `while`, `do-while`, `switch`.
- Useful for exiting loops early.
###### Continue
The `continue` statement skips the remainder of the current iteration and proceeds to the next iteration. 
```cpp 
	for (int i{ 0 }; i < 5; ++i) {
	    if (i == 2)
	        continue;
	    std::cout << i << ' ';
	} //output 0 1 3 4
```
- Unlike `break`, it doesn't terminate the loop.
- In `for` loops, the end expression (`++i`) still executes.
- In `while` and `do-while` loops, be careful not to skip counter updates, as this can cause infinite loops.
###### Return
An early return is a `return` statement that exits a function before reaching its end.
```cpp
	int div(int x, int y) {
		if (y == 0)
			return 0;
		return x / y;
	}
```
#### Halts
A *halt* terminates the entire program. 
- `std::exit()` terminates the program manually and returns a status code to the operating system.
	- Cleans up static objects.
	- Does not clean local variables.
- `std::quick_exit()` terminates the same as `std::exit()` except it does not destroy static objects.
- `std::abort()` aborts the program abnormally without cleanup. 
- `std::terminate()` usually triggered by unhandled exceptions, also calls `std::abort()` by default.
- `std::atexit()` registers a function to execute automatically during normal program termination.
	- Registered functions must take no parameters and return `void`.
	- Multiple functions can be registered.
	- They're executed in reverse registration order.
- `std::at_quick_exit()` registers cleanup functions specifically for `std::quick_exit()`.