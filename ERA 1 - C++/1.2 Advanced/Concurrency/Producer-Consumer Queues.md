# Producer-Consumer Queues
A *producer-consumer queue* transfers work from producers to consumers while separating their execution rates.
For a recorder, it also defines who owns a buffer after submission and what happens when processing falls behind.
#### Bounded capacity
An unbounded queue converts sustained overload into growing memory use and latency.
A bounded queue needs an explicit full-queue policy:
- Block the producer when delaying it is acceptable.
- Reject new work and report that decision.
- Discard selected old work only when its dependencies permit it.
Dropping a raw captured frame differs from dropping an encoded reference frame on which later pictures depend.
#### A small queue
The following complete class stores integer work IDs. It rejects pushes when full and drains accepted work after closing.
```cpp
#include <condition_variable>
#include <cstddef>
#include <mutex>
#include <optional>
#include <queue>
#include <stdexcept>
class work_queue {
public:
    explicit work_queue(std::size_t capacity) : capacity_{capacity} {
        if (capacity == 0) throw std::invalid_argument{"zero capacity"};
    }
    bool try_push(int item) {
        {
            std::lock_guard lock{mutex_};
            if (closed_ || items_.size() >= capacity_) return false;
            items_.push(item);
        }
        changed_.notify_one();
        return true;
    }
    std::optional<int> pop() {
        std::unique_lock lock{mutex_};
        changed_.wait(lock, [this] { return closed_ || !items_.empty(); });
        if (items_.empty()) return std::nullopt;
        int item{items_.front()};
        items_.pop();
        return item;
    }
    void close() {
        {
            std::lock_guard lock{mutex_};
            closed_ = true;
        }
        changed_.notify_all();
    }
private:
    std::size_t capacity_;
    std::mutex mutex_;
    std::condition_variable changed_;
    std::queue<int> items_;
    bool closed_{};
};
```
The queue must outlive all threads using it. Its owner closes it and joins the consumers before destruction.
#### Ownership
With real sample objects, specify whether rejection leaves ownership with the caller or destroys a moved-in argument.
Passing an owner by value can transfer ownership before the queue discovers it is full. Choose an interface that makes that behavior intentional.
Successful transfer lets the producer stop modifying the submitted buffer. Reusing the same texture immediately can corrupt data still being processed.
#### Capacity units
A limit of 100 items does not bound memory if item sizes vary widely. Consider both sample count and total bytes.
Measure queue age as well as depth: a short queue can still contain old work if a consumer stalls.
#### Shutdown and errors
The example has a persistent closed state, wakes waiters and rejects future submissions. A production queue may additionally support cancellation, failure reporting and a discard policy.
Keep slow processing and callbacks outside the queue's mutex.
#### Related notes
- [[Condition Variables]]
- [[Smart Pointers]]
- [[Replay Buffer Design]]
