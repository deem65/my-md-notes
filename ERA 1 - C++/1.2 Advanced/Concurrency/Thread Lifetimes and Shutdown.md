# Thread Lifetimes and Shutdown
A worker must stop using resources before those resources are destroyed. Correct shutdown is a dependency problem as well as a request to exit a loop.
#### Ownership graph
For each worker, identify its owning object, the state it borrows, the waits it can block in and the operations that can wake those waits.
A lambda capturing `this` borrows its containing object. The object must remain alive through the worker's last access.
#### Member order
```cpp
// declaration sketch; work_queue comes from its own note
class pipeline {
    work_queue queue_{8};
    std::jthread worker_;
};
```
Members are destroyed in reverse declaration order. Declaring the worker after the queue lets worker destruction occur first, but that alone is insufficient: the worker's wait must be released before joining can finish.
Write an explicit shutdown operation that closes the queue before joining when its consumer waits on queue closure.
#### Shutdown sequence
A useful sequence for a recorder is:
1. Stop accepting new external commands that create work.
2. Request capture/audio producers to stop and wake their waits.
3. Join producers so no new samples can arrive.
4. Drain or cancel downstream work according to policy.
5. Complete or cancel outstanding exports and report their outcomes.
6. Join remaining workers.
7. Release media, graphics, Lua and synchronization resources in dependency order.
This is a design pattern; exact ordering follows the chosen API contracts and pipeline connections.
#### Avoiding deadlocks
Do not join a worker while holding a mutex it needs to release its resources or publish its final result.
Do not ask a worker to synchronously call the UI while the UI is blocked joining that worker.
Do not destroy a condition variable while another thread is waiting on it.
Never join the current thread through its own owner.
#### Cancellation versus completion
Stopping background recording need not cancel a valid clip already being written. Choose whether the UI waits, shows progress or offers cancellation.
A canceled export should not be reported as saved. Close its resources and follow the temporary-file cleanup policy.
Use idempotent shutdown: repeated stop requests should be harmless and should not double-release resources.
#### Partial startup
A failure after creating the device but before starting the encoder still requires cleanup of the completed earlier stages.
RAII owners and explicit state transitions make this tractable. Avoid setting a public state to recording before required initialization has succeeded.
#### Verification
Test closing the app while idle, capturing, recovering, waiting for work and saving a clip. Repeat start/stop quickly.
For each blocking call, answer: what causes this call to return after shutdown begins?
#### Related notes
- [[Recording States and Lifecycle]]
- [[Threads and Cancellation]]
- [[Failure and Stress Testing]]
