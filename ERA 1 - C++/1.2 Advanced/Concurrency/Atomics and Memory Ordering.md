An *atomic operation* accesses an atomic object indivisibly according to its specified memory-ordering rules.
Atomics are useful for simple shared counters and publication protocols, but do not automatically protect a whole data structure.
#### Atomic counters
```cpp
#include <atomic>
std::atomic<unsigned long long> dropped_frames{};
void count_drop() {
    dropped_frames.fetch_add(1, std::memory_order_relaxed);
}
```
Relaxed ordering is suitable here if the counter only records a statistic and does not publish other data.
`counter = counter + 1` performs separate load and store operations and can lose concurrent updates. Use a read-modify-write operation such as `fetch_add`.
#### Ordering
| Order | Intended role |
|---|---|
| Relaxed | Atomicity without publishing unrelated writes |
| Release | Publish preceding writes through an atomic operation |
| Acquire | Observe writes published by a matching release |
| Acquire-release | Both roles for appropriate read-modify-write operations |
| Sequentially consistent | Default stronger ordering, including a common order for these operations |
An acquire load must observe the relevant release or release sequence to establish the intended synchronization.
#### One-time publication
```cpp
#include <atomic>
#include <thread>
int main() {
    int result{};
    std::atomic<bool> ready{false};
    std::jthread producer{[&] {
        result = 42;
        ready.store(true, std::memory_order_release);
    }};
    while (!ready.load(std::memory_order_acquire)) {
        std::this_thread::yield();
    }
    return result == 42 ? 0 : 1;
}
```
This demonstrates one-time publication, not an efficient general work queue. The producer never modifies `result` again after publication.
Reusing the flag while continuing to mutate the data needs a new protocol. Replacing a mutex with this loop without reasoning about every access can introduce a data race.
#### Compare and exchange
Compare-exchange conditionally updates a value and reports whether the expected state matched. On failure it updates the expected argument with an observed value.
`compare_exchange_weak` may fail spuriously and is normally used in a retry loop. Memory-order arguments have separate success/failure constraints.
Use it only when the state transition and retry behavior are clear.
#### Limitations
- Atomic does not necessarily mean lock-free.
- Multiple atomic fields can still be observed in inconsistent combinations.
- Thread-safe shared ownership does not make the owned object thread-safe.
- `volatile` is not a substitute for atomic operations or locks.
Start with a mutex for compound state. Optimize only when measurement shows contention matters and a replacement can be justified.
#### Related notes
- [[Mutexes and Deadlocks]]
- [[Producer-Consumer Queues]]
#### References
- [C++ working draft: atomic ordering](https://eel.is/c++draft/atomics.order).
