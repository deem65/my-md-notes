An *expression* is a sequence of literals, variables, operators, and function calls that can be evaluated to produce a result:
```cpp
5                  // literal expression
x                  // variable expression
x + 5              // arithmetic expression
x > 10             // comparison expression
add(5, 3)          // function call expression
```
Every expression has two important properties:
- **Type:** Determines what kind of result the expression produces, such as `int`, `bool`, or `double`.
- **Value category:** Describes how the expression's result behaves, particularly whether it identifies an existing object or produces a value.
Expressions may also have **side effects**, such as modifying variables or performing input/output.
#### Operators and Operands
An *operator* is a symbol that performs an operation on one or more operands.
An *operand* is an expression that an operator operates on.
```cpp
int result{5 + 3};
```
- `+` is the operator.
- `5` and `3` are the operands.
- `5 + 3` is the expression.
- `8` is the resulting value.
Operators are classified by the number of operands they accept:
- **Unary:** One operand, such as `-x` or `!condition`.
- **Binary:** Two operands, such as `a + b` or `a == b`.
- **Ternary:** Three operands, such as `condition ? a : b`.
#### Evaluation
*Evaluation* is the process of determining the result of an expression and performing any associated side effects the expression has.
- The compiler translates the expression into machine instructions, but it may optimize them to make the program more efficient.
- Not every expression requires a runtime calculation. Some expressions can be evaluated during compilation.
#### Precedence & Associativity
When an expression contains multiple operators, C++ uses **precedence** and **associativity** to determine how the expression is grouped.
- Precedence
	- *Precedence* determines which operators bind more tightly.
Multiplication has higher precedence than addition:
	- The expression `5 + 3 * 2` is grouped as `5 + (3 * 2)`.
- Associativity
	- *Associativity* determines how operators of equal precedence are grouped.
Subtraction is left-associative:
    - The expression `5 - 2 - 1` is grouped as `(5 - 2) - 1`.
- It is important to note that precedence and associativity determine grouping, not necessarily the order in which operands are evaluated.
- Parentheses can be used to explicitly control grouping and improve readability.
#### Common operation types
Arithmetic operations perform mathematical operations:
```cpp
a + b
a - b
a * b
a / b
a % b
```
Relational and equality operations compare values and produce a `bool`:
```cpp
a > b
a < b
a >= b
a <= b
a == b
a != b
```
Logical Expressions combine or negate boolean conditions:
```cpp
a && b  // logical AND
a || b  // logical OR
!a      // logical NOT
```
`&&` and `||` use *short-circuit evaluation*: the second operand is not evaluated if the result is already determined by the first, for example:
- `bool result(true || foo())` the first expression is true, therefore the entire expression must be true. C++ does not therefore call `foo()`.
- `bool result(false && foo())` same concept, the first expression is false, therefore the entire expression must be false. Here too, C++ does not call `foo()`.
#### Assignment Expressions
Assign values to existing objects.
```cpp
x = 10;
x += 5;
x *= 2;
```
- Assignment is right-associative, so `x = y = 10` gets grouped as `x = (y = 10)`. 
- When using an assignment operator, the expression produces (or "returns") an `lvalue` referring to the left-operand.
  - `x = 10` changes `x` to `10` and produces an `lvalue` referring to `x`.
  - Because the result refers to the original object, it can be used in further assignments.
  - Example: `x = y = 10;` assigns `10` to both variables.

`end 09/10/2026`

