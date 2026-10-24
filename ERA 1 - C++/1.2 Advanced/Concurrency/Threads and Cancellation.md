# Threads and Cancellation
A *thread* is a sequence of execution within a process. Threads in one process share its address space, so accessing shared objects requires deliberate lifetime and synchronization rules.
Concurrency means work can make progress independently; parallelism means some work executes simultaneously.
#### Starting and joining
```cpp
#include <thread>
int main() {
    int result{};
    std::thread worker{[&result] { result = 42; }};
    worker.join();
    return result == 42 ? 0 : 1;
}
```
`join()` waits for the worker to finish. Successful joining also establishes the synchronization needed to observe the worker's completed writes here.
Reading `result` before the join without another synchronization mechanism would create a data race if the worker writes concurrently.
#### joinable
A thread object can remain joinable after its underlying execution has finished. `joinable()` describes the object's association, not whether the work is currently running.
Destroying a joinable `std::thread` calls `std::terminate`. Either join it or deliberately detach it before destruction.
Detaching removes the convenient join relationship; it does not make captured references or application shutdown safe.
#### jthread
C++20 `std::jthread` requests stop and joins in its destructor when joinable.
```cpp
#include <chrono>
#include <thread>
int main() {
    std::jthread worker{[](std::stop_token stop) {
        while (!stop.stop_requested()) {
            std::this_thread::sleep_for(std::chrono::milliseconds{1});
        }
    }};
    worker.request_stop();
}
```
The short sleep is only a small cancellation demonstration. A recorder should wait for actual work or an API event rather than poll continuously.
#### Cooperative cancellation
A stop request is a request, not forced thread termination. The worker must observe it and return.
- A blocked call that never wakes cannot check a token.
- Stop-aware waits or bounded API timeouts are required where appropriate.
- Cancellation should leave invariants intact and release acquired resources.
- Distinguish stopping capture from abandoning an export already writing a file.
Using a flag without a synchronization rule does not create safe cancellation.
#### Exceptions
An exception escaping the thread entry function calls `std::terminate`. Catch errors inside the worker and publish a result through a synchronized channel, promise/future or application event.
Do not let a cleanup path throw while trying to report another error.
#### Thread count
More workers can increase contention, memory usage and scheduling overhead. Some APIs also own internal worker threads.
Choose threads around blocking operations and component contracts, then measure. Keep the UI responsive without creating a thread for every captured frame or hotkey press.
COM initialization and graphics-context access remain separate obligations on the threads using those APIs.
#### Related notes
- [[Thread Lifetimes and Shutdown]]
- [[Condition Variables]]
- [[COM Initialization and Threading]]
