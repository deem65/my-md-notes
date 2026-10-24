`std::span<T>` is a C++20 view over a contiguous sequence of `T` objects. It provides pointer-and-size access without owning the elements.
#### Passing a buffer
```cpp
#include <span>
#include <vector>
void clear_samples(std::span<int> samples) {
    for (int& sample : samples) sample = 0;
}
int main() {
    std::vector<int> values{1, 2, 3};
    clear_samples(values);
}
```
The span parameter is inexpensive to copy. Copying it does not copy the vector's elements.
This interface keeps the data pointer and element count together, reducing mismatched pointer/size arguments.
#### Mutable and const elements
```cpp
void inspect(std::span<const int> samples);
void modify(std::span<int> samples);
```
`span<const int>` prevents modifying elements through the view.
`const span<int>` prevents changing that span object but still permits modification of its referenced integers. Constness of the view and constness of the elements are separate.
#### Size and subranges
- `size()` counts elements.
- `size_bytes()` counts bytes occupied by the elements.
- `data()` borrows the first-element pointer.
- `first`, `last` and `subspan` produce views of portions of the sequence.
```cpp
std::span<int> whole{values};
if (whole.size() >= 2) {
    auto first_two{whole.first(2)};
}
```
This fragment continues the vector example while `values` is alive.
In C++20, indexed access and subrange operations have bounds preconditions rather than automatic exception-based checking. Validate untrusted offsets and counts before calling them.
#### Fixed extent
`std::span<int, 4>` represents exactly four elements. `std::span<int>` has a size determined at runtime.
The extent constrains construction; it does not grant ownership or extend the underlying object's lifetime.
#### Lifetime
A span becomes unusable when its owner is destroyed or its referenced storage is invalidated.
```cpp
// unsafe: returning a view into a local array
// std::span<int> bad() { int values[3]{}; return values; }
```
A vector growth operation may invalidate a previously created span. Storing that span in a worker queue does not keep the vector storage alive.
Queue an owning buffer, an explicitly shared immutable owner, or a lease whose lifetime covers processing.
#### Byte views
`std::as_bytes` gives a read-only byte view of suitable object storage; `std::as_writable_bytes` gives mutable representation access where permitted.
They do not turn arbitrary bytes into valid higher-level objects.
For mapped video, a span should describe the mapped accessible memory under the mapping's lifetime. It must not survive `Unmap`.
#### Related notes
- [[Containers]]
- [[Object Representation]]
- [[Smart Pointers]]
