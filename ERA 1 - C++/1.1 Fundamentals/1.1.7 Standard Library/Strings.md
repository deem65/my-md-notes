# Strings
A *string* represents a sequence of character code units. Text encoding determines how those units represent characters.
C++ provides owning strings, borrowed views and compatibility with null-terminated C strings.
#### C strings
```cpp
const char* command{"status"};
char editable[]{'s', 't', 'o', 'p', '\0'};
```
A C string ends at the first null character, `\0`. A pointer to characters does not itself contain a length or prove that a terminator exists.
- String literals must not be modified.
- The array above owns its characters; the pointer borrows a literal with static storage duration.
- A buffer's allocated capacity is different from the length of the text stored in it.
#### Owning strings
```cpp
#include <string>
int main() {
    std::string command{"clip"};
    command += " 30";
    auto count{command.size()};
}
```
`std::string` owns a contiguous sequence of `char` values. Its size excludes the trailing terminator it maintains.
It can contain embedded null characters; a C API may nevertheless stop reading at the first one.
`size()` measures code units, not necessarily human-visible characters.
#### Access and lifetime
- `operator[]` performs unchecked element access for ordinary character positions; validate indices.
- `at()` checks bounds and throws if the index is invalid.
- `c_str()` provides a null-terminated borrowed pointer.
- Mutating the string may invalidate pointers, iterators and views into its storage.
- Destroying the string invalidates all borrowed access to that storage.
#### String views
`std::string_view` borrows a sequence without owning or copying it.
```cpp
#include <string_view>
bool is_status(std::string_view command) {
    return command == "status";
}
```
A view carries a pointer and length conceptually. It need not end at a null terminator.
```cpp
// incorrect: the returned view outlives its source
// std::string_view bad() { return std::string{"clip"}; }
```
Do not retain a view into temporary command input after that input is destroyed or modified. Use an owning string for stored registrations or queued command arguments.
#### Parsing numbers
`std::from_chars` parses numbers without requiring a null-terminated input range.
```cpp
#include <charconv>
#include <optional>
#include <string_view>
#include <system_error>
std::optional<int> parse_seconds(std::string_view text) {
    if (text.empty()) return std::nullopt;
    int value{};
    const char* end{text.data() + text.size()};
    auto result{std::from_chars(text.data(), end, value)};
    if (result.ec != std::errc{} || result.ptr != end || value <= 0) {
        return std::nullopt;
    }
    return value;
}
```
Checking the returned pointer rejects trailing junk such as `30abc`. Parsing successfully does not establish an application's maximum duration or memory budget.
#### Unicode
`std::string` does not automatically mean UTF-8, and `std::wstring` does not have one portable encoding.
Windows wide-character APIs use UTF-16 code units. Convert deliberately at the Windows boundary; do not convert by merely casting character pointers or copying each byte into a wide character.
For UTF-8, one character can occupy multiple bytes. Byte slicing may split an encoded character.
#### Related notes
- [[Windows Strings and Errors]]
- [[Console Input and Command Parsing]]
- [[Filesystem]]
