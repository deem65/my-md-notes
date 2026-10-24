A *container* manages a collection of elements and provides operations for accessing or changing that collection.
Choose a container according to its storage layout, ownership and the operations the application performs most often.
#### Common choices
| Container | Typical use | Important property |
|---|---|---|
| `std::array<T, N>` | Fixed-size data | Contiguous; size belongs to the type |
| `std::vector<T>` | Dynamic arrays and bytes | Contiguous; inexpensive indexed access |
| `std::deque<T>` | Adding/removing at both ends | Segmented storage; not one contiguous buffer |
| `std::queue<T>` | FIFO work | Adapter exposing queue operations |
| `std::unordered_map<K, V>` | Lookup by key | Hash-based; iteration order is unspecified |
#### Vector size and capacity
```cpp
#include <vector>
int main() {
    std::vector<int> values;
    values.reserve(100);
    values.push_back(42);
    // size is 1; capacity is at least 100
}
```
- `size()` counts existing elements.
- `capacity()` is how many elements fit before a growth reallocation is required.
- `reserve()` allocates capacity but does not create elements.
- `resize()` changes the number of elements.
- Writing `values[50]` in this example is invalid despite the reserved capacity.
#### Storage invalidation
Vector reallocation invalidates pointers, references and iterators into its old element storage.
Erase and insertion have additional position-dependent invalidation rules even without reallocation.
Reserve a measured expected size to reduce allocations, but do not rely on a particular growth factor.
```cpp
std::vector<int> values{1, 2};
int* borrowed{&values[0]};
values.push_back(3);
// borrowed may now be dangling; reacquire it from values
```
#### Queue operations
```cpp
#include <queue>
#include <utility>
std::queue<int> pending;
pending.push(10);
if (!pending.empty()) {
    int next{std::move(pending.front())};
    pending.pop();
}
```
`pop()` removes an element but does not return it. Read or move from the front before popping.
An ordinary queue has neither a fixed capacity nor built-in thread synchronization. Add both when creating a recording work queue.
#### Hash lookup
```cpp
#include <string>
#include <unordered_map>
std::unordered_map<std::string, int> commands{{"status", 1}};
auto found{commands.find("status")};
if (found != commands.end()) {
    int id{found->second};
}
```
`operator[]` can insert a missing key. Use `find()` or `contains()` for non-inserting lookup.
Average lookup is constant-time with suitable hashing; worst-case lookup is linear. Rehashing invalidates iterators, while references/pointers to existing elements remain valid unless those elements are erased.
#### Owning elements
A container owns its elements. If an element is a raw pointer, destroying that element does not delete its target.
A `vector<unique_ptr<T>>` owns exclusive resource owners. A `vector<T*>` ordinarily records borrowed pointers and needs a separate lifetime contract.
Avoid `vector<bool>` when you expect an ordinary contiguous array of Boolean objects; it has a specialized packed representation.
#### In Modest Clipper
- Use contiguous byte storage for CPU buffers.
- A deque of encoded sample records can simplify front eviction.
- Use a hash map for command-name lookup if the registry warrants it.
- Bound queue and buffer memory independently of container choice.
#### Related notes
- [[Arrays]]
- [[Iterators]]
- [[Smart Pointers]]
- [[Replay Buffer Design]]
