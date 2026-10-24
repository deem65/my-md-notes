*Dynamic memory allocation* obtains storage while a program is running.
It allows a program to create objects whose storage is not tied to the end of the block in which the allocation occurs.
```cpp
int* ptr{new int{10}};

// use the allocated object

delete ptr;
ptr = nullptr;
```

- `new int{10}` creates an `int` initialized to `10` in dynamically allocated storage.
- The expression returns a pointer to that object.
- `delete ptr` destroys the object and releases its storage.
	- Keep in mind, it doesn't destroy the pointer variable itself but the dynamically allocated object.
- The pointer variable and the allocated object are separate objects.

#### Storage duration
*Storage duration* describes how long an object's storage is available.

| Duration  | Common example                                 | Storage availability                           |
| --------- | ---------------------------------------------- | ---------------------------------------------- |
| Automatic | Ordinary block-local variable                  | Associated with entering and leaving its block |
| Static    | Global variable or local `static` variable     | For the program's duration                     |
| Thread    | Variable declared `thread_local`               | For its thread's duration                      |
| Dynamic   | Object created by an ordinary `new` expression | Until the storage is explicitly released       |
- *Scope* concerns where a name can be used; *lifetime* concerns when an object exists.
- Object lifetime and storage duration are related but are not interchangeable terms.
- A local pointer can have automatic storage duration while pointing to an object with dynamic storage duration.

#### Stack and heap
The *stack* and *heap* are common implementation terms.

- Ordinary automatic variables are commonly stored on a thread's stack.
- Dynamic allocations commonly use an area called the heap or free store.
- C++ specifies storage-duration and lifetime rules, rather than requiring every local variable to occupy a physical stack slot.
- Compiler optimization can change the actual storage used while preserving the required behavior.

#### Allocating a single object
Use a `new` expression to allocate and initialize an object:
```cpp
int* value{new int{42}};

*value = 50;

delete value;
value = nullptr;
```

- `value` stores the address; `*value` accesses the allocated `int`.
- Ordinary `new` provides storage suitable for the requested type.
- For a class type, initialization may call a constructor.
- Matching `delete` calls the object's destructor when applicable, then releases its storage.

Initialization still matters:
```cpp
int* a{new int};      // the int is not initialized; do not read its value
int* b{new int{}};    // initialized to 0
int* c{new int{25}};  // initialized to 25

delete a;
delete b;
delete c;
```
Prefer initializing an allocated object immediately.

#### Pointer lifetime and object lifetime
Destroying a raw pointer does not delete the object it points to.
```cpp
int* ptr{};

{
    int* local{new int{10}};
    ptr = local;
} // local disappears, but the allocated int still exists

*ptr = 20;
delete ptr;
ptr = nullptr;
```
Here, `ptr` keeps access to the allocation after `local` leaves scope.

#### Ownership and borrowing
An *owner* is responsible for eventually releasing a resource.
A *borrower* can access the resource without taking responsibility for releasing it.
```cpp
int* owner{new int{10}};
int* borrowed{owner}; // copies the address, not the allocated int

*borrowed = 20;

delete owner; // the allocation is released once
owner = nullptr;
borrowed = nullptr;
```

- Both pointers initially refer to the same object.
- Copying a raw pointer does not create a second allocation.
- The type `int*` does not itself express whether it owns or borrows.
- These names describe the intended responsibility; the language does not enforce it for raw pointers.

#### Dynamic arrays
The array form of `new` allocates an array whose element count can be determined at runtime.
```cpp
#include <cstddef>

int main() {
    std::size_t count{5};
    int* values{new int[count]{}}; // all five elements become 0

    values[0] = 10;
    values[1] = 20;

    delete[] values;
    values = nullptr;
}
```

- Use `new T[count]` for an array and `delete[]` for its matching cleanup.
- `new int[count]` leaves the integer elements uninitialized; the empty braces in `new int[count]{}` initialize them to zero.
- A raw pointer does not carry the array's element count. Keep the count separately.
- Valid element indices run from `0` through `count - 1` when `count` is positive.
- A zero-element allocation is permitted, but it has no element you can access and still needs matching cleanup.
- `sizeof(values)` measures the pointer, not the allocation's size.
- The allocated array does not resize itself. A different size generally requires another allocation.

#### Matching allocation and deallocation
Each allocation mechanism needs its matching release mechanism.

| Allocation | Matching cleanup |
|---|---|
| `new T{...}` | `delete ptr;` |
| `new T[count]{}` | `delete[] ptr;` |

- Mixing `new` with `delete[]`, or `new[]` with `delete`, causes undefined behavior.
- Do not delete a pointer to an automatic variable or an interior element of an allocation.
- Deleting a null pointer is permitted and does not destroy an object.
- `delete` does not automatically assign `nullptr` to your pointer variable.

#### Memory leaks
A *memory leak* occurs when allocated storage is no longer released as required.
Losing the last usable pointer to a manual allocation is a common cause.
This example is intentionally incorrect:
```cpp
void leak_memory() {
    int* ptr{new int{10}};
} // ptr disappears without releasing the allocation
```
Other common causes include overwriting the only pointer, returning early, or throwing an exception before cleanup.
```cpp
int* ptr{new int{10}};
// ptr = new int{20}; // would lose access to the first allocation
delete ptr;
```
Adding manual cleanup to every exit path becomes difficult as functions grow. [[RAII]] ties cleanup to an owning object's lifetime.

#### Dangling pointers and double deletion
A pointer becomes *dangling* when its target object's lifetime has ended.
```cpp
int* ptr{new int{10}};
int* alias{ptr};

delete ptr;
ptr = nullptr;

// *alias = 20; // undefined behavior: the object no longer exists
// delete alias; // undefined behavior: the allocation was already released

alias = nullptr;
```

- Setting `ptr` to `nullptr` does not change any copies of its old address.
- A null check cannot detect every dangling pointer.
- Releasing the same allocation twice is called *double deletion*.
- See [[Pointers]] for more about null and dangling pointers.

#### Allocation failure
Ordinary throwing `new` reports allocation failure by throwing `std::bad_alloc`.
```cpp
#include <iostream>
#include <new>

int main() {
    try {
        int* ptr{new int{10}};
        delete ptr;
    }
    catch (const std::bad_alloc&) {
        std::cerr << "allocation failed\n";
        return 1;
    }
}
```
The `std::nothrow` form reports allocation failure with a null pointer instead:
```cpp
#include <new>

int main() {
    int* ptr{new (std::nothrow) int{10}};
    if (!ptr) {
        return 1;
    }

    delete ptr;
}
```

- Include `<new>` for `std::bad_alloc` and `std::nothrow`.
- `std::nothrow` does not suppress exceptions from a class object's constructor.
- With ordinary matching allocation functions, if construction throws after storage was allocated, the `new` expression releases that storage automatically.
- This does not clean up unrelated allocations that the constructor stored only in raw pointers.

#### Using an owning container
For a dynamically sized sequence, `std::vector` manages allocation and cleanup for you.
```cpp
#include <cstddef>
#include <vector>

int main() {
    std::size_t count{5};
    std::vector<int> values(count); // five integers initialized to 0

    values[0] = 10;
    values.push_back(20);
} // values destroys its elements and releases its storage
```

- The vector object is local, but it manages dynamically allocated element storage.
- It remembers its element count and can grow as needed.
- `std::vector<int> values{5};` instead creates one element whose value is `5`; the parentheses above select the size constructor.
- A vector owning pointer elements does not automatically delete the pointed-to objects.

#### Best practices

- Prefer ordinary local objects when their lifetime is sufficient.
- Prefer an owning container for a dynamically sized collection.
- Understand manual allocation, but put resource ownership inside RAII types in application code.
- Initialize allocated objects before reading them.
- Keep each allocation's owner and matching cleanup clear.
- Never access released objects or delete the same allocation twice.
- Account for early returns and exceptions, not only the normal execution path.

#### Related notes

- [[Data]]
- [[Scope]]
- [[Pointers]]
- [[References]]
- [[RAII]]

#### References

- C++ working draft: [storage duration](https://eel.is/c++draft/basic.stc) and [object lifetime](https://eel.is/c++draft/basic.life).
- C++ working draft: [new expressions](https://eel.is/c++draft/expr.new), [delete expressions](https://eel.is/c++draft/expr.delete), and [allocation functions](https://eel.is/c++draft/new.delete.single).
- C++ working draft: [vector constructors and destructor](https://eel.is/c++draft/vector.cons).
- C++ Core Guidelines: [resource management](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-resource).
