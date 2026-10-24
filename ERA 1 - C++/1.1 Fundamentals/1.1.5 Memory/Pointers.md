A *pointer* is a value that can designate an object, function or permitted position in storage, or hold a null value.
An ordinary object pointer is often represented as an address, but pointer validity depends on more than the numeric address.
#### Address and indirection
```cpp
int value{10};
int* pointer{&value};
*pointer = 20; // changes value
```
`&value` obtains its address. `*pointer` accesses the pointed-to object as an lvalue.
Assigning `pointer = &other` changes the pointer; assigning `*pointer = other` changes its target.
#### Initialization and null
```cpp
int* empty{};
int* also_empty{nullptr};
```
An uninitialized automatic local pointer has no usable initialized value in C++20. Static-storage pointers receive zero initialization, yielding null pointers.
Prefer `nullptr` to `0` or the legacy `NULL` macro. Standard C++ has no built-in `null` keyword.
`if (pointer)` checks whether it is non-null, not whether it points to a live accessible object.
#### Constness
```cpp
int value{10};
const int* read_only{&value};
int* const fixed{&value};
const int* const fixed_read_only{&value};
```
- `const int*` restricts modification through the pointer.
- `int* const` prevents changing that pointer's stored value.
- Neither form independently owns the target.
#### Lifetime
A pointer can outlive its target and become dangling. Dereferencing such a pointer is invalid.
Reassigning the pointer does not need to read its previous value. Other uses of invalid pointer values have detailed language rules; do not assume that printing, copying or comparing an arbitrary invalid pointer makes it safe to keep using.
An address being nonzero or unchanged does not prove that the original object is alive.
#### Pointer arithmetic
```cpp
int values[]{10, 20, 30};
int* first{values};
int* end{values + 3};
```
`first + 1` advances by one integer element, not one byte. Arithmetic must remain within the permitted array range, including one-past-the-end.
The end pointer is a boundary and must not be dereferenced. Subtracting two pointers requires the appropriate same-array relationship.
#### void pointers
`void*` can represent an object pointer without exposing its pointed-to type.
```cpp
void* erased{&value};
int* restored{static_cast<int*>(erased)};
```
Recovering the original suitable pointer type is different from claiming arbitrary storage contains an int.
Standard C++ does not permit arithmetic on `void*` or dereferencing it to obtain an object value. Function pointers have separate rules.
#### Pointer-to-pointer output parameters
```cpp
void point_to(int** output, int& value) {
    *output = &value;
}
int main() {
    int value{42};
    int* result{};
    point_to(&result, value);
}
```
`output` points to the caller's pointer variable. Assigning through it changes that variable.
Windows and COM APIs frequently use output parameters; the API specifies whether the returned pointer is borrowed or owned and how to release it.
#### Representation and ownership
Do not assume every pointer type has the same size or that all C++ platforms use one universal representation. Windows x64 object pointers are normally 64-bit; pointer-to-member types can differ.
Raw pointers usually express borrowing in application interfaces. Put resource responsibility in RAII owners and document any exceptions.
#### Related notes
- [[RAII]]
- [[Smart Pointers]]
- [[Arrays]]
- [[COM Ownership]]
