# Mutexes and Deadlocks
A *mutex* allows cooperating threads to serialize access to shared state. The protected state and its rules matter more than the existence of a mutex variable.
#### Data races
A data race can occur when threads perform conflicting unsynchronized accesses to a memory location, with at least one non-atomic write. In C++, a data race causes undefined behavior.
A broader *race condition* is a result depending incorrectly on timing; it can exist even when individual accesses are atomic or locked.
#### Scoped locking
```cpp
#include <mutex>
class counter {
public:
    void increment() {
        std::lock_guard lock{mutex_};
        ++value_;
    }
    int get() const {
        std::lock_guard lock{mutex_};
        return value_;
    }
private:
    mutable std::mutex mutex_;
    int value_{};
};
```
Both reads and writes use the same mutex. `lock_guard` releases it when the scope exits, including ordinary exception unwinding.
Making the mutex mutable lets a logically const read operation synchronize access; it does not make the data public or freely mutable.
#### Protect an invariant
If queue size and queue contents must agree, update them under one consistent synchronization contract.
Locking each field separately may allow another thread to observe an impossible combination.
Returning a reference to protected state after releasing the lock loses that protection. Prefer an owned snapshot or an operation that completes while the required lock is held.
#### Lock types
- `lock_guard` owns a lock for a straightforward scope.
- `unique_lock` can unlock, relock, defer locking and participate in condition-variable waits.
- `scoped_lock` can acquire several mutexes using deadlock-avoidance machinery.
RAII prevents forgotten unlocks but cannot by itself prevent deadlock.
#### Deadlock
A common cycle is thread A holding lock 1 while waiting for lock 2, while thread B holds lock 2 and waits for lock 1.
Use a documented global lock order or an appropriate multi-lock operation. Also avoid joining a worker while holding a mutex that worker needs to exit.
Ordinary mutexes are not recursive: relocking the same mutex through a callback can deadlock or violate preconditions.
#### Keep critical sections small
Under a replay-buffer lock, choose and retain the sample owners needed for an export. Release the lock before writing the file.
Avoid invoking unknown callbacks, Lua code or slow I/O under core locks. They may block, reenter the application or try to acquire another lock.
Copy a small state snapshot under lock and format UI text afterward.
#### Related notes
- [[Condition Variables]]
- [[Producer-Consumer Queues]]
- [[Concurrent Clip Saving]]
