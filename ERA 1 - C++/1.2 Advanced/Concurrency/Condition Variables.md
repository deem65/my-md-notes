# Condition Variables
A *condition variable* allows a thread to sleep until shared state may satisfy a condition.
The condition is stored in ordinary shared data protected by a mutex; the condition variable is the waiting and notification mechanism.
#### Predicate-based waiting
```cpp
#include <condition_variable>
#include <mutex>
#include <queue>
std::mutex mutex;
std::condition_variable changed;
std::queue<int> work;
bool closed{};
```
Consumer fragment:
```cpp
std::unique_lock lock{mutex};
changed.wait(lock, [] { return closed || !work.empty(); });
if (!work.empty()) {
    int item{work.front()};
    work.pop();
    lock.unlock();
    // process item outside the lock
}
```
The wait atomically releases the mutex while blocking and reacquires it before returning.
The predicate overload rechecks the condition after waking. Spurious wakeups are permitted, and another consumer may take the work before this one reacquires the lock.
#### Publishing work
```cpp
{
    std::lock_guard lock{mutex};
    work.push(42);
}
changed.notify_one();
```
The producer changes the state under the same mutex used by the consumer. Notification then prompts a waiter to check it.
A notification is not a stored queue item. If nobody is waiting, it is not remembered as a future wakeup credit. The predicate's persistent state prevents lost-work logic.
#### notify_one and notify_all
`notify_one()` wakes one waiter; `notify_all()` wakes all current waiters.
Waking all workers for one ordinary item can create unnecessary contention. Closing a queue generally needs to wake every consumer so each can exit.
#### Shutdown
```cpp
{
    std::lock_guard lock{mutex};
    closed = true;
}
changed.notify_all();
```
The wait predicate must include the shutdown condition. A stop flag that never wakes a blocked consumer can make joining it hang.
Choose whether already queued work is drained or discarded and implement that policy consistently.
#### Timed and stop-aware waits
`wait_for` and `wait_until` support time limits. A timeout does not prove no event occurred concurrently; inspect state under the lock.
Repeatedly restarting a full relative timeout after wakeups can extend an overall deadline. Use one absolute deadline for a fixed operation budget.
C++20 `condition_variable_any` offers stop-token-aware waiting overloads. Ordinary `condition_variable` does not acquire that feature merely because the thread is a `jthread`.
#### Lifetime
Keep the condition variable, mutex and predicate data alive until all waiting threads have exited. Destruction order is part of the owner design.
#### Related notes
- [[Mutexes and Deadlocks]]
- [[Threads and Cancellation]]
- [[Thread Lifetimes and Shutdown]]
