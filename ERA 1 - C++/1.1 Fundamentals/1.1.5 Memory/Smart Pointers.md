A *smart pointer* is a class that manages pointer-like access and, for owning smart pointers, the lifetime of a resource.
The owner is the smart-pointer object. The managed object may live elsewhere, usually in dynamically allocated storage.
#### Exclusive ownership
`std::unique_ptr<T>` represents one owner of a `T` object.
```cpp
#include <memory>
int main() {
    auto value{std::make_unique<int>(42)};
    *value = 50;
} // destroys the integer and releases its storage
```
- `std::make_unique` creates the object and returns its owner.
- `*value` accesses the object; `value.get()` returns a borrowed raw pointer.
- Destroying a non-empty owner invokes its deleter.
- A `unique_ptr` cannot be copied. Two exclusive owners cannot independently own the same allocation.
#### Transferring ownership
```cpp
#include <memory>
#include <utility>
int main() {
    auto first{std::make_unique<int>(42)};
    auto second{std::move(first)};
    // first is empty; second owns the integer
}
```
`std::move` enables move construction; the `unique_ptr` move constructor performs the transfer.
The allocated integer stays at the same address. Moving its owner does not move the integer itself.
```cpp
void consume(std::unique_ptr<int> value); // accepts ownership
void inspect(const int& value);          // borrows an object
```
Choose the parameter type according to the function's ownership requirements.
#### Releasing and resetting
- `reset()` destroys the currently managed object and makes the owner empty.
- `release()` returns the pointer and makes the owner empty **without destroying the object**.
- `get()` borrows the pointer without changing ownership.
- Calling `delete` on a pointer obtained from `get()` would leave the smart pointer responsible for an already destroyed object.
Use `release()` only when another owner immediately assumes responsibility.
#### Shared ownership
`std::shared_ptr<T>` permits several owners of one managed object.
```cpp
#include <memory>
int main() {
    auto first{std::make_shared<int>(42)};
    auto second{first};
    first.reset();
    // second still owns the integer
}
```
A control block tracks ownership. The managed object's lifetime normally ends when the last strong owner releases it.
- Constructing two unrelated `shared_ptr` objects from the same raw pointer creates two ownership groups and can cause double deletion.
- Copy an existing owner to share its ownership group.
- Reference-count coordination does not make concurrent accesses to the managed object's data safe.
- Concurrent modification of the same ordinary `shared_ptr` object also requires synchronization.
#### Weak references
`std::weak_ptr` observes a shared ownership group without keeping the managed object alive.
```cpp
std::weak_ptr<int> observer{second};
if (auto owner{observer.lock()}) {
    // owner keeps the object alive while it is used here
}
```
The snippet continues the shared-ownership example before its scope ends.
`lock()` either obtains a strong owner or returns an empty one. Checking `expired()` alone does not reserve the object's lifetime.
Weak references help break ownership cycles.
#### Custom deleters
A resource may require cleanup other than `delete`.
```cpp
#include <cstdio>
#include <memory>
struct file_closer {
    void operator()(std::FILE* file) const noexcept {
        if (file) std::fclose(file);
    }
};
using file_ptr = std::unique_ptr<std::FILE, file_closer>;
```
The deleter must match the acquisition API. Use a COM smart pointer for COM interface ownership; it calls `Release`, not `delete`.
#### In Modest Clipper
A component may have exclusive ownership of its worker state. Several clip exports may share immutable encoded sample data, while the replay buffer evicts its own references.
Sharing is a design choice with memory costs. Prefer exclusive ownership when sharing is unnecessary.