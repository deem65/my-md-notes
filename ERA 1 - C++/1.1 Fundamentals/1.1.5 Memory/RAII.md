*RAII* stands for *Resource Acquisition Is Initialization*.
It is a technique that ties responsibility for a resource to the lifetime of an owning object.

- The object acquires or takes ownership of a resource during initialization.
- Its destructor releases the resource when the owning object is destroyed.
- Code using the owner does not need to repeat cleanup at every return or exception path.
- RAII applies to more than memory.

#### Resources
A *resource* is something a program acquires and must eventually release.

| Resource | Typical cleanup |
|---|---|
| Dynamically allocated storage | Deallocate it |
| Open file | Close it |
| Held mutex lock | Unlock it |
| Open socket or operating-system handle | Release it using the matching API |

RAII puts the acquisition and cleanup responsibilities together in one owner.

#### Constructors and destructors
A *constructor* initializes a class object.
A *destructor* performs cleanup when that object's lifetime ends.
```cpp
#include <iostream>

class lifetime_example {
public:
    lifetime_example() {
        std::cout << "constructed\n";
    }

    ~lifetime_example() {
        std::cout << "destroyed\n";
    }
};

int main() {
    lifetime_example example;
    std::cout << "working\n";
}
```
The output is:
```text
constructed
working
destroyed
```
- The constructor has the class's name and no return type.
- The destructor's name starts with `~`, and it takes no arguments.
- The local `example` object is destroyed when execution leaves `main` normally.
- This example demonstrates lifetime events; a resource owner uses its destructor to perform actual cleanup.

#### RAII with a standard library type
`std::ofstream` owns the file connection it opens.
```cpp
#include <fstream>
#include <stdexcept>

void write_score(int score) {
    std::ofstream file{"score.txt"};
    if (!file) {
        throw std::runtime_error{"could not open score.txt"};
    }

    file << score << '\n';
    if (!file) {
        throw std::runtime_error{"could not write score"};
    }
} // the stream's file buffer closes the file
```
- The stream object exists even if opening fails, so the example checks its state.
- Normal return destroys the local stream and releases its file connection.
- If an exception propagates out of the function to a handler, the stream is also destroyed.
- RAII handles cleanup; it does not guarantee that writing or closing a file succeeds.
- When a caller must detect a close or final flush failure, explicitly close the stream and check its state before returning.

#### Early returns
Normal scope exit destroys completed local objects even when execution leaves through `return`.
```cpp
#include <fstream>

bool has_first_character(const char* path) {
    std::ifstream file{path};
    if (!file) {
        return false;
    }

    char character{};
    return static_cast<bool>(file.get(character));
}
```
Both return paths destroy `file`; neither needs a separate manual cleanup call.
The same principle applies when `break` or `continue` leaves a block containing an automatic owner.

#### Exceptions and stack unwinding
*Stack unwinding* destroys completed local objects as an exception travels outward to a matching handler.
```cpp
#include <fstream>
#include <iostream>
#include <stdexcept>

void process_file() {
    std::ifstream file{"data.txt"};
    if (!file) {
        throw std::runtime_error{"could not open data.txt"};
    }

    throw std::runtime_error{"processing failed"};
}

int main() {
    try {
        process_file();
    }
    catch (const std::exception& error) {
        std::cerr << error.what() << '\n';
    }
}
```
- If the file opened, it is closed while unwinding `process_file()`.
- Destruction happens before execution reaches the handler in `main()`.
- This removes the need to catch an exception merely to close the file and rethrow it.
- Cleanup is guaranteed for the relevant objects during ordinary unwinding to a handler, not for every possible way a program terminates.

#### Destruction order
Local objects in the same block are destroyed in the reverse order in which their construction completed.
```cpp
#include <fstream>

void read_files() {
    std::ifstream first{"first.txt"};
    std::ifstream second{"second.txt"};
} // second is destroyed, then first
```
- This also applies to completed local objects destroyed during stack unwinding.
- Within a class, non-static data members initialize in declaration order, not the written order of a constructor's initializer list.
- Members are destroyed in reverse order after the destructor's body finishes.
- Declare owners before members that depend on them so those owners can outlive their dependents.

#### Writing a small resource owner
A class can wrap an API that otherwise requires manual cleanup.
This example owns a C file opened with `std::fopen` and closes it with `std::fclose`.
```cpp
#include <cstdio>
#include <stdexcept>

class file_owner {
public:
    explicit file_owner(const char* path)
        : file_{std::fopen(path, "r")} {
        if (!file_) {
            throw std::runtime_error{"could not open file"};
        }
    }

    ~file_owner() noexcept {
        std::fclose(file_);
    }

    file_owner(const file_owner&) = delete;
    file_owner& operator=(const file_owner&) = delete;

    std::FILE* get() const noexcept {
        return file_;
    }

private:
    std::FILE* file_;
};

int first_character(const char* path) {
    file_owner file{path};
    return std::fgetc(file.get());
} // file's destructor closes the C file
```
- The constructor either obtains a valid file or throws.
- `: file_{...}` initializes the member before the constructor's body runs.
- `explicit` prevents unintended implicit construction from a path.
- The destructor closes the file exactly once for a successfully constructed owner.
- `= delete` forbids copying. The declarations also prevent an implicit move operation; this simple owner is neither copyable nor movable.
- `get()` returns a borrowed pointer. Its caller must not close the file or use the pointer after the owner is destroyed.
- `noexcept` states that the operation does not allow an exception to escape.
- This small read-only wrapper does not report close failures or distinguish the two reasons `std::fgetc` can return `EOF`: end-of-file and a read error.
- Prefer an existing suitable wrapper such as `std::ifstream` in ordinary code; this class illustrates how cleanup is implemented.

#### Why copying owners needs care
Copying a raw handle would give two owners access to the same resource.
If both released it, the second cleanup would be invalid.
- A resource-owning class must define what copying means or forbid copying.
- Possible designs include making an independent copy or allowing ownership to be transferred by a move operation.
- Moving an owner must leave the source unable to release the transferred resource a second time.
- Detailed copy and move behavior belongs with classes and move semantics.

#### Construction failure
If a constructor throws, the incomplete object's own destructor does not run.
However, its fully constructed members and base subobjects are destroyed.

- A member that is itself an RAII owner can therefore clean up even when a later construction step fails.
- A raw pointer member does not automatically delete its target.
- If you acquire a resource and then perform another operation that can throw, make sure a completed owner already protects the resource.
- In `file_owner`, the constructor throws only when opening failed, so there is no successfully opened file to release on that failure path.

#### When cleanup is not guaranteed
RAII depends on the owner being destroyed.

- Directly calling `std::exit()` does not destroy the calling function's automatic local objects. It does perform other normal-termination cleanup, including destruction of static-storage objects.
- `std::abort()` and abrupt process termination do not provide normal scope-exit cleanup.
- If no handler matches an exception, `std::terminate()` is called; full stack unwinding is not guaranteed.
- Do not allow exceptions to escape a destructor. An exception escaping a `noexcept` destructor calls `std::terminate()`; another exception escaping a destructor during unwinding also causes termination.
- RAII cannot clean up through an owner that itself is leaked.

#### RAII and garbage collection
RAII cleanup follows the owning object's lifetime.
For an automatic local owner, that normally means cleanup as execution leaves its scope.

- A garbage collector typically reclaims memory based on reachability, with collection timing controlled by the runtime.
- RAII can release files, locks, and other resources as well as memory.
- An owner can manage dynamically allocated storage even when the owner itself is local.
- `std::vector` is a familiar example: destroying the vector releases the element storage it owns.

#### Best practices
- Give each resource a clear owning object.
- Use existing standard library owners when they fit the resource.
- Put required cleanup in the owner's destructor.
- Keep borrowed access within the owner's lifetime.
- Decide explicitly whether a custom owner can be copied or moved.
- Do not throw from destructors; provide a separate checked operation when cleanup errors must be reported.
- Prefer classes composed of existing RAII members so automatic member destruction handles their cleanup.

