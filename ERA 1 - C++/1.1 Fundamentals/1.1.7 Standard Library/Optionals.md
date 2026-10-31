C++ 17 introduces `std::optional`, which is a class template type that implements an optional value. 
A `std::optional<T>` can either have a value of `T`, or not. 
Here's a code example:
```cpp
	#include <optional>
	#include <iostream>
	
	std::optional<int> do_int_div(int x, int y) {
		if (y == 0)
			return std::nullopt; 
		return x / y;
	}
	int main() {
		std::optional<int> result {do_int_div(20, 5)};
		if (result)
			std::cout << "result: " << *result << std::endl;
		else
			std::cout << "result failed";
	}
```
- You can also use `result.value()`, which checks if the value exists, while `*result` assumes it does.  When it doesn't it returns `std::bad_optional_access`.
- You can use `result.value_or(x)` which means return the contained value if it exists, otherwise return `x`:
```cpp
	std::optional<int> result{};
	std::cout << result.value_or(0); //prints 0
```
- You can also use `result.has_value()` if you only want to check if the value exists.
- We can also use `std::optional` to pass in an optional parameter to a function: 
```cpp
	void print_id(std::optional<const int> id = std::nullopt);
```
There are two advantages to this approach:
- It documents that the parameter is optional.
- We can pass in an rvalue.
However, because `std::optional` makes a copy of its argument, this because problematic when `T` is an expensive to copy type, like `std::string`.
With normal function parameters, you can work around this by passing a `const lvalue reference`, so that the copy will not be made. As of C++ 23, `std::optional` remains without support for references. 
Therefore, using `std::optional<T>` as an optional parameter is only recommended when `T` would normally be passed by value, otherwise, use `const T*`.

---
Sometimes function overloading provides a superior solution, so:
- Prefer `std::optional` for optional return types.
- Prefer function overloading for optional function parameters whenever possible, otherwise, `std::optional<T>` for optional arguments when `T` would normally be passed by value. 
- Prefer `const T*` over `std::optional<T>` for function parameters when `T` is expensive to copy.