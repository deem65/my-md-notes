Computers typically generate _pseudo-random_ numbers using a **pseudo-random number generator (PRNG)**, an algorithm that produces a sequence of seemingly random numbers.
- PRNGs are **stateful**: they retain internal state between calls and update it to produce the next number.
- PRNGs are **deterministic**: the same initial state produces the same sequence.
- A **seed** initializes that state. Changing the seed allows different sequences across program runs.
- **Underseeding** means providing too little quality seed data, which can reduce the quality of the generated sequences.
A good PRNG should produce approximately uniform, well-distributed results, have a long period before repeating, and be efficient. **Mersenne Twister** (`std::mt19937`) is a reasonable general-purpose PRNG, but is **not suitable for cryptography** because its outputs can be predicted.
#### Mersenne Twister
C++ provides random-number facilities through `<random>`:
- `std::mt19937` generates 32-bit unsigned integers; `std::mt19937_64` generates 64-bit unsigned integers.
- `mt()` produces the next value in the generator's sequence.
- `std::uniform_int_distribution` converts generator outputs into an evenly distributed integer range, **including both endpoints**.
```cpp
#include <random>

std::random_device rd{};
std::seed_seq seed{ rd(), rd(), rd(), rd(), rd(), rd(), rd(), rd() };
std::mt19937 mt{ seed };

std::uniform_int_distribution die{ 1, 6 };
int roll{ die(mt) }; // number from 1 through 6
```
`std::random_device` supplies seed data; `std::seed_seq` mixes that data to initialize the generator's state. **Seed a PRNG once**, then reuse it instead of creating or reseeding it for every random number. A fixed seed is useful during debugging because it reproduces the same sequence.
#### Global random numbers (`Random.h`)
If many functions or files need randomness, passing a generator everywhere becomes inconvenient. One approach is to share **one global generator inside a namespace**. This is a practical exception to the usual advice against mutable globals.
A simplified C++17 header-only implementation:
```cpp
// Random.h
#pragma once

#include <chrono>
#include <random>

namespace Random
{
    inline std::mt19937 generate()
    {
        std::random_device rd{};
        std::seed_seq seed{
            static_cast<std::seed_seq::result_type>
	            (
                std::chrono::steady_clock::now().
                time_since_epoch().count()
                ),
            rd(), rd(), rd(), rd(), rd(), rd(), rd()
        };
        return std::mt19937{ seed };
    }
    inline std::mt19937 mt{ generate() };
    inline int get(int min, int max)
    {
        return std::uniform_int_distribution
        { 
	        min, max 
        }
	        (mt);
    }
}
```
Include `"Random.h"` wherever needed and call `Random::get(1, 6)` to get a number between 1 and 6, inclusive. You can also use `Random::mt` directly with a custom distribution.
- `generate()` seeds and returns a `std::mt19937`; its result initializes `Random::mt` automatically.
- The global `mt` retains its state between calls and is shared across files.
- `inline` allows the variable and functions to be defined in a header without violating the **one-definition rule (ODR)**. The program still has one shared `Random::mt` instance.
- The original `Random.h` also provides **function templates** for other integral return types.
**Best practice:** Reuse a well-seeded generator, use a distribution for the range you need, and avoid reseeding on every call. This shared-global approach is convenient for ordinary single-threaded programs; concurrent access would require synchronization or separate generators.