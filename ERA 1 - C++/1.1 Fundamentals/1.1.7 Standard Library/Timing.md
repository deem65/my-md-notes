# Timing
`std::chrono` represents durations, clocks and points on a clock's timeline using distinct types.
This helps prevent accidentally treating seconds, milliseconds and clock ticks as interchangeable integers.
#### Durations
```cpp
#include <chrono>
using namespace std::chrono_literals;
auto buffer_duration{30s};
auto timeout{20ms};
```
A duration has a representation type and a compile-time tick period.
```cpp
auto milliseconds{
    std::chrono::duration_cast<std::chrono::milliseconds>(buffer_duration)
};
auto count{milliseconds.count()};
```
`count()` returns the numeric tick count in that duration's unit. Conversion to an integral coarser unit truncates; choose rounding deliberately.
#### Choosing a clock
| Clock | Suitable use |
|---|---|
| `steady_clock` | Elapsed time and internal deadlines |
| `system_clock` | Wall-clock dates and user-visible timestamps |
| `high_resolution_clock` | Implementation-dependent; inspect its guarantees |
`steady_clock` does not go backward. Wall time can change through clock adjustment and should not govern replay duration.
High resolution does not automatically mean monotonicity or accurate scheduling.
#### Measuring elapsed time
```cpp
#include <chrono>
int main() {
    auto start{std::chrono::steady_clock::now()};
    // operation being measured
    auto elapsed{std::chrono::steady_clock::now() - start};
    double seconds{std::chrono::duration<double>(elapsed).count()};
}
```
One measurement does not characterize a pipeline. Record many samples, warm up resources and distinguish CPU submission time from GPU completion time.
#### Deadlines and pacing
Advancing an absolute target deadline can reduce drift compared with sleeping a fixed interval after every frame's work.
If the application falls behind, skip obsolete deadlines rather than running an unbounded catch-up loop.
Sleeping until a deadline does not guarantee the operating system schedules the thread at that exact instant.
For cancellation-sensitive waits, use a suitable event, condition variable or API with a bounded timeout.
#### Different timebases
Media APIs may use 100-nanosecond units, audio device positions use sample frames, and QPC uses its reported counter frequency.
Convert by units and establish an explicit shared origin. Never assume that two clocks with similar numeric values share an epoch.
At fractional frame rates, repeatedly adding a rounded integer frame duration can accumulate error. Derive timestamps from a frame index and rational rate, with overflow-aware arithmetic.
#### In Modest Clipper
Use steady elapsed time for buffer retention and operation latency, media timestamps for exported playback, and wall time for filenames or log dates.
A timestamp records when a media event occurred; the time a worker finally processed it may be later.
#### Related notes
- [[Frame Timing and Pacing]]
- [[Timestamps and Frame Ordering]]
- [[Audio Timing and Synchronization]]
