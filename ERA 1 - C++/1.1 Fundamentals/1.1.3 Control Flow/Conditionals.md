#### If statements
An `if` statement executes code when its condition evaluates to true:
- By default, `if` or `else` control only one statement. Use `{}` to group multiple statements into a single compound statement AKA block. 
```cpp
	if (x > 10)
		std::cout << "greater than 10"
	else if (x == 10)
		std::cout << "equal to 10"
	else {
		x = 10;
		std::cout << "10 assigned to x"
	}
```
C++ 17 introduces `constexpr if` statements. 
- The conditional of these statements are evaluated at compile time. 
- To use a constexpr-if-statement, we add `constexpr` keyword after the `if`:
```cpp
	constexpr double g{9.8};
	
	if constexpr (g == 9.8) {
		std::cout << "earth";
	}
	else {
		std::cout << "not earth";
	}
```
- The compile will evaluate the conditional at compile time, so it will compile this:
```cpp
	constexpr double g{9.8};
	std::cout << "earth";
```
- It is best practice to favor `constexpr if` statements when the conditional is a constant expression. 
C++ 17 additionally provides us with `if` with an initializer. An `if` statement can declare and initialize a variable before evaluating its condition.
```cpp
	if (int x{10}; x > 5) {
	    std::cout << x;
	}
```
- The initializer executes before evaluating the condition.
- The variable is accessible in both `if` and `else` branches.
- The variable goes out of scope when the entire `if-else` statement ends.
- Useful for initializing a temporary value that is only needed for a condition and its branches.
- A common use case is searching for something and immediately checking whether it was found:
```cpp
	Player* find_player(int id)`;

	void print_player(int id) {
	    if (
		    Player* player{ find_player(id) }; 
		    player != nullptr
		    ) {
		        std::cout << player->name;
		    }
	    else {
	        std::cout << "Player not found";
	    } 
	    //player is destroyed after its no longer needed
	}
```
C++ 23 provides us with `if consteval` statements which check whether execution occurs in a compile-time constant-evaluated context.
```cpp
	constexpr int get_value() {
	    if consteval {
	        return 10; // compile-time evaluation
	    }
	    else {
	        return 20; // runtime evaluation
	    }
	}
	
	constexpr int a{ get_value() }; // 10
	int b{ get_value() };           // 20
```
- Unlike `if constexpr`, it doesn't evaluate a Boolean condition.
- It determines whether the current evaluation is happening at compile time.
- Both branches require explicit `{}` blocks.
- Useful when a `constexpr` function needs different implementations for compile-time and runtime evaluation.
#### Switch statements 
A `switch` is a control-flow mechanism based on labels which compares a single expression against multiple `const` values.
- Compilers can sometimes implement it using a jump table, which maps values directly to code locations, this can be more efficient than checking conditions sequentially.
```cpp
	switch (x) {
	case 1:
		std::cout << "one";
		break;
	case 2:
		std::cout << "two";
		break;
	default:
		std::cout << "unknown";
		break;
	}
```
- `switch (expression)` evaluates the expression once.
- `case` defines a possible matching value, which must be a constant expression.
- `default` executes when no case matches (optional, conventionally placed last).
- `break` exits the switch without exiting the function.
- Without `break` or `return`, execution continues into subsequent cases:
	- If `x = 2`, `switch` evaluates `x` once.
	- Jumps to `case 2:`. 
	- Executes `std::cout << "two";`
	- Continues executing `case 3:` and `default:` without checking the values.
	- This behavior is called *fallthrough*.
		- Commenting intentional fallthrough is a common convention, however this does not remove the warnings. In C++ a new attribute was added: `[[fallthrough]]` to combat this.
		- Attributes are a modern feature to allow the programmer to talk to the compiler with some additional data regarding the code. 
		- The `[[fallthrough]]` attribute modifies a `null statement` to indicate that fallthrough is intentional (and no warnings should be triggered):
```cpp
	switch (1) {
	case 1:
		std::cout << 1 << std::endl;
		[[fallthrough]]; // intentional fallthrough 
		// the semicolon to indicate the nullstatement which is being
		// modified by the attribute.
	case 2:
		std::cout << 2 << std::endl; // also executed
		break;
	}
	 
```
- If no case matches and no `default` exists, the switch is skipped.
- Multiple `case` labels can share the same statements by stacking them together.
```cpp
	bool isVowel(char c) {
	    switch (c) {
	    case 'a':
	    case 'e':
	    case 'i':
	    case 'o':
	    case 'u':
	        return true;
	    default:
	        return false;
	    }
	}
```
- If any case matches, execution begins at `return true`.
- Stacking case labels is not considered fallthrough, so `[[fallthrough]]` is unnecessary.
- Unlike `if` statements, `case` labels do not create implicit blocks.
- All variables declared directly inside a `switch` share the same scope.
```cpp
	switch (x) {
	case 1:
	    int y; // declaration allowed
	    y = 4;
	    break;
	case 2:
	    y = 5; // accessible because y shares the switch scope
	    break;
	}
```
 - Variables can be declared inside a `switch`, but initialization is restricted because execution can jump past initializers:
```cpp
	switch (x) {
	case 1:
	    int y{ 5 }; // error: initialization can be bypassed
	    break;
	case 2:
	    break;
	}
```
- We can use `{}` to go around this:
```cpp
	switch (x) {
	case 1: {
	    int y{ 5 }; // valid
	    std::cout << y;
	    break;
	}
	case 2:
	    break;
	}
```