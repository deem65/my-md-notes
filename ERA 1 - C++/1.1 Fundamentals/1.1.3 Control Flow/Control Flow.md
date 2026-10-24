When the program is run, the CPU begins execution at the top of `main()`, and ends execution at the end of `main()`. 
The specific sequence of statements the CPU executes is called the *execution path*, or *path* for short. 
*Straight line programs* are programs who take the same path every time they are run. 
However if we ask the user for an input and the input is invalid for our case, the program may have a different execution path. 
We are provided with *control flow statements*, which are statements are that allow us to control and change the execution path of the program. 
#### Categories of flow control statements
- Condition statements cause a sequence of code to execute only if some condition is met. 
	- `if`
	- `else` 
	- `switch`
- Jumps tell the CPU to start executing the statements at some other location.
	- `goto`
	- `break`
	- `continue`
- Function calls jump to some other location and back. 
	- `foo()`
	- `return`
- Loops repeatedly execute some code in a loop until some condition is met.
	- `while`
	- `do-while`
	- `for`
	- `ranged-for`
- Halts terminate the program.
	- `std::exit()`
	- `std::abort()`
- Exceptions are a special kind of flow control structure designed for error handling.
	- `try`
	- `throw`
	- `catch`



