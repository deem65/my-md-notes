An object's *representation* is the storage used to hold its value and any padding. Understanding representation matters when working with pixels, file formats and binary APIs.
#### Bytes and sizes
`sizeof(T)` returns the size of `T` in C++ bytes. A byte contains `CHAR_BIT` bits; Windows platforms targeted by Clipper use 8-bit bytes.
```cpp
#include <cstddef>
#include <cstdint>
std::uint32_t pixel{};
std::size_t bytes{sizeof(pixel)};
```
`std::uint32_t`, when provided, is exactly 32 bits. `int` has no universal four-byte guarantee across all C++ implementations.
#### Alignment and padding
```cpp
struct record {
    char type;
    int value;
};
```
The implementation can insert padding before `value` and after the last member. `sizeof(record)` therefore need not equal `sizeof(char) + sizeof(int)`.
`alignof(T)` describes the alignment requirement. An address pointing to enough bytes is not necessarily suitably aligned for a `T`.
Packing structs can create unaligned accesses and ABI problems. It does not solve endianness or serialization portability.
#### Endianness
Endianness describes the byte order used for multi-byte values.
```cpp
#include <bit>
constexpr bool little_endian{
    std::endian::native == std::endian::little
};
```
A file or network representation must specify byte order independently of the host. Pixel format names and channel order also have API-specific meanings; verify them instead of guessing from an integer's printed hexadecimal value.
#### Inspecting and copying bytes
`char`, `unsigned char` and `std::byte` have special roles in accessing object representations.
```cpp
#include <array>
#include <cstddef>
#include <cstdint>
#include <cstring>
int main() {
    std::uint32_t value{42};
    std::array<std::byte, sizeof(value)> bytes{};
    std::memcpy(bytes.data(), &value, sizeof(value));
}
```
`memcpy` copies bytes; it does not call constructors, copy assignment or ownership-transfer operations.
For trivially copyable types, representation copying has defined uses. Copying the bytes of `std::string` or a resource owner is not a valid substitute for copying the object.
#### Type punning
Casting a byte pointer to `T*` does not prove alignment, establish every necessary lifetime condition, validate its value, or permit arbitrary aliasing.
Use explicit field decoding for serialized data. For suitable equal-size trivially copyable types, `std::bit_cast` expresses a representation conversion more clearly, but its resulting representation must still be meaningful for the destination type.
#### Buffer arithmetic
For tightly packed BGRA8, visible row bytes are `width * 4`. The API may provide a larger actual row pitch.
Validate multiplication before allocating: a wrapped `width * height * bytes_per_pixel` can allocate too little storage.
Maintain a clear distinction between pixels, audio frames, samples, elements and bytes.

