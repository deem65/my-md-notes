A *loop* repeatedly executes a statement or block according to a condition or range.
Choose the form that makes the loop's progress and termination easiest to follow.
#### While statements
```cpp
int count{0};
while (count <= 10) {
    ++count;
}
// count is 11
```
The condition is checked before each iteration. An initially false condition means the body never runs.
The final increment changes 10 to 11; only then does the next condition check fail.
#### Do while statements
```cpp
int attempts{};
do {
    ++attempts;
} while (attempts < 3);
```
The body runs at least once because the condition is checked afterward. Use this form when that first execution is part of the intended logic.
Notice the required semicolon after the condition.
#### For statements
```cpp
for (int index{0}; index < 10; ++index) {
    // process indices 0 through 9
}
```
Execution order is initialization, condition, body, iteration expression, then back to the condition.
- Initialization happens once.
- The condition is checked before each body execution.
- `continue` transfers to the iteration expression before checking again.
- A variable declared in the initialization belongs to the loop's scope.
Avoid unsigned backward loops of the form `index >= 0`; that condition never becomes false for an unsigned index.
#### Range-based loops
```cpp
int values[]{1, 2, 3};
for (int& value : values) {
    value *= 2;
}
```
| Variable | Behavior |
|---|---|
| `auto value` | Copies each element |
| `auto& value` | Borrows a modifiable element |
| `const auto& value` | Borrows an element for reading |
Do not invalidate the underlying iterators by modifying the container in an unsupported way during iteration.
For erasing elements while iterating, use the container's returned iterator or an appropriate removal algorithm.
#### break and continue
`break` exits the nearest loop or switch. `continue` skips the remainder of the nearest loop's body.
They still destroy automatic objects whose scopes are exited normally.
`return` exits the function, including its active loops. An exception can also transfer execution out through stack unwinding.
#### Intentional indefinite loops
```cpp
while (true) {
    // wait for work or shutdown
    break;
}
```
A recorder worker often loops until cancellation. A loop that continuously checks a flag without blocking can waste an entire CPU core.
Use a suitable wait mechanism and ensure a shutdown request can wake or periodically release that wait.
`std::exit` is a library function, not a loop-control keyword; it bypasses destruction of automatic locals in the calling stack.

