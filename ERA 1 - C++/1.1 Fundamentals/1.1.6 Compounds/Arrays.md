An array is a fixed number of elements of the same type, stored contiguously in memory.
It is declared using `type name[size]`. The size must be known at compile time.
#### Initialization
```cpp
int a[5];                // uninitialized elements (ordinary local array)
int b[5]{};              // all elements initialized to 0
int c[5]{1, 2, 3, 4, 5}; // all elements initialized explicitly
int d[5]{1, 2};          // remaining elements initialized to 0
int e[]{1, 2, 3};        // size deduced
```
Elements are accessed through the subscript operator `arr[index]`, starting from 0.
#### Memory Layout
Array elements are stored contiguously, without padding between elements.
Advancing one element corresponds to `sizeof(T)` bytes.
The array's element count can be obtained using:
```cpp
std::size(arr); 
sizeof(arr) / sizeof(arr[0]);
```
#### Array-to-Pointer Decay
Array-to-pointer decay is the implicit conversion of an array into a pointer to its first element.
Arrays are not pointers.
```cpp
int arr[3]{10, 20, 30};
int* ptr = arr; // &arr[0]
```
When an array is passed to a function with an array parameter, the parameter is adjusted to a pointer, losing its size information.
```cpp
void process(int arr[], std::size_t count);
```
Arrays can also be passed by reference, preserving their type and size.
Array decay does not happen when taking the address of the entire array.
```cpp
int arr[3]{};
auto ptr = &arr; // T: int (*)[3]
```
#### Multidimensional Arrays
Multidimensional arrays are arrays of arrays, stored contiguously in row-major order.
```cpp
int arr[2][3]{
    {1, 2, 3},
    {4, 5, 6}
};
```
The type is `int[2][3]`.
Assuming four-byte integers:
```cpp
sizeof(arr);       // 24 bytes
sizeof(arr[0]);    // 12 bytes
sizeof(arr[0][0]); // 4 bytes
std::size(arr);    // 2 rows
std::size(arr[0]); // 3 columns
```
When a multidimensional array decays, it becomes a pointer to its first subarray.
```cpp
auto ptr = arr; // T: int (*)[3]
++ptr;          // advances by 3 * sizeof(int) bytes
```
#### Limitations
- No automatic runtime bounds checking. Out-of-bounds access causes undefined behavior.
- Arrays cannot be assigned directly.
- Arrays cannot be resized.
- Arrays cannot be returned directly by value.
- Arrays support range-based for loops.
```cpp
int arr[3]{10, 20, 30};
for (int value : arr) {
    std::cout << value << '\n';
}
```
Returning a pointer to a local array produces a dangling pointer because the array's lifetime ends when the function returns.
```cpp
int* get_array() {
    int arr[3]{1, 2, 3};
    return arr; // dangling 
}
```