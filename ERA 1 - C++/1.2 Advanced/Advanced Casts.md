# Advanced Casts
C++ casts request particular conversions. They do not provide a general way to bypass object lifetime, alignment, ownership or API contracts safely.
#### static_cast
Use `static_cast` for ordinary explicit conversions and certain related-type conversions.
```cpp
double seconds{1.5};
int whole_seconds{static_cast<int>(seconds)};
```
Floating-to-integer conversion discards the fractional part and must stay within the destination's representable range.
A base-to-derived pointer cast using `static_cast` does not check whether the actual object is the required derived type. Incorrect assumptions can lead to undefined behavior.
#### dynamic_cast
For appropriate polymorphic hierarchies, `dynamic_cast` can check a downcast or cross-cast at runtime.
```cpp
struct base { virtual ~base() = default; };
struct derived : base {};
void inspect(base* object) {
    if (auto specific{dynamic_cast<derived*>(object)}) {
        // object has a suitable derived relationship
    }
}
```
A failed pointer cast returns null; a failed reference cast throws `std::bad_cast`.
COM interface discovery uses `QueryInterface`, not a C++ RTTI downcast.
#### const_cast
`const_cast` can change cv-qualification in supported pointer/reference conversions.
```cpp
int value{10};
const int* view{&value};
int* writable{const_cast<int*>(view)};
*writable = 20; // underlying object was not const
```
If the underlying object was actually defined as const, modifying it through the cast is undefined behavior.
A cast also does not guarantee a borrowed object is otherwise writable or belongs to you.
#### reinterpret_cast
This cast supports selected low-level reinterpretations, including certain pointer/integer conversions.
```cpp
const auto* bytes{reinterpret_cast<const unsigned char*>(&value)};
```
Inspecting an object's representation through permitted byte-like types is different from reading arbitrary bytes as an unrelated `T` object.
Before typed access, separately establish suitable alignment, valid lifetime, accessible type and valid representation.
#### C-style casts
`(T)expression` can select among several kinds of conversion. The syntax hides whether it removes constness or reinterprets storage.
Prefer the named cast that states the actual operation. If you cannot explain why that operation is valid, changing syntax does not fix the underlying issue.
#### bit_cast and memcpy
`std::bit_cast` expresses representation conversion between suitable equal-size trivially copyable types. `memcpy` copies bytes into existing storage under its own requirements.
Neither should be used to clone strings, COM objects or resource owners by copying their internal bytes.
#### Windows boundary examples
Window message parameters and API output pointers sometimes require conversions specified by the API. Follow the exact signature and preserve pointer width.
For COM output pointers, prefer typed smart-pointer helpers and `IID_PPV_ARGS` where appropriate instead of manually constructing unchecked `void**` conversions.
#### Related notes
- [[Type Conversion]]
- [[Object Representation]]
- [[COM Interfaces]]
- [[Windows API Fundamentals]]
