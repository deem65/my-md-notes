# Iterators
An *iterator* identifies a position in a sequence and provides operations for navigating or accessing elements.
Iterators let algorithms work with different containers through a common interface.
#### Half-open ranges
A range is commonly represented as `[first, last)`: `first` is included and `last` is excluded.
```cpp
#include <vector>
int main() {
    std::vector<int> values{10, 20, 30};
    for (auto it{values.begin()}; it != values.end(); ++it) {
        *it += 1;
    }
}
```
- `begin()` points to the first element when the container is non-empty.
- `end()` marks the one-past-the-end position.
- An empty container has `begin() == end()`.
- `end()` must not be dereferenced.
#### Iterator capabilities
| Category | Representative capability |
|---|---|
| Input | Read a sequence; may be single-pass |
| Output | Write a sequence |
| Forward | Multiple passes and forward movement |
| Bidirectional | Move forward or backward |
| Random access | Jump by an offset and compare positions |
| Contiguous | Elements also occupy adjacent storage |
A vector provides contiguous iterators. A deque provides random-access iterators but does not provide contiguous storage.
An algorithm may require more than basic forward iteration. For example, ordinary `std::sort` requires random-access iterators.
#### Const iteration
```cpp
const std::vector<int> values{1, 2, 3};
auto it{values.begin()};
int value{*it};
// *it = 4; // invalid: the element is const through this iterator
```
`cbegin()` and `cend()` explicitly request read-only element access even from a mutable container.
Making the iterator variable itself `const` prevents changing that iterator's position; it is different from a const iterator type that restricts element mutation.
#### Invalidation
An iterator becomes invalid when an operation makes its recorded position unusable.
- Vector reallocation invalidates all iterators into the vector.
- Vector erase invalidates iterators at and after the erased position, including the previous end iterator.
- Hash-table rehashing invalidates iterators.
- Deque invalidation rules differ between end insertion and middle insertion; consult the operation's contract.
Do not compare, dereference or increment an invalidated iterator as though it still belonged to the sequence.
#### Erasing while iterating
```cpp
std::vector<int> values{1, 2, 3, 4};
for (auto it{values.begin()}; it != values.end();) {
    if (*it % 2 == 0) {
        it = values.erase(it);
    } else {
        ++it;
    }
}
```
`erase()` returns the next valid position. Incrementing unconditionally after erasing can skip elements or advance past the end.
For ordinary filtering, `std::erase_if` often expresses the operation more directly.
#### Borrowing and concurrency
An iterator does not own the container or keep it alive. Nor does holding one lock the container against modification on another thread.
If another thread can alter the sequence, the lifetime and synchronization rules must cover the whole iteration.
#### Related notes
- [[Containers]]
- [[Algorithms unf]]
- [[Mutexes and Deadlocks]]
