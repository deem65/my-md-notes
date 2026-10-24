# Filesystem
`std::filesystem` provides path manipulation and operations on files and directories. It is available in C++17.
A path describes a location; constructing a path does not create a file or prove that the location exists.
#### Paths
```cpp
#include <filesystem>
namespace fs = std::filesystem;
fs::path directory{"clips"};
fs::path output{directory / "clip.mp4"};
```
Use path operations instead of manually concatenating separators.
- A relative path depends on the process's current working directory.
- An absolute path identifies a location from the filesystem root.
- `filename()`, `extension()` and `parent_path()` manipulate path components.
- Lexical normalization does not verify the actual filesystem or resolve every link.
#### Directories and errors
```cpp
#include <filesystem>
#include <system_error>
int main() {
    std::error_code error;
    std::filesystem::create_directories("clips", error);
    if (error) {
        // report error.message() and the relevant path
        return 1;
    }
}
```
Many operations provide both a throwing overload and an overload accepting `std::error_code`.
Treat the error value as authoritative. Some Boolean results describe whether an action was necessary, rather than whether the operation failed.
#### Checks and races
Checking `exists()` or free space does not reserve a filename or guarantee a later write succeeds.
Another process can create the file, permissions can change, or the disk can fill between the check and the operation.
Perform the actual operation and handle its result. Use an API that guarantees exclusive creation when accidental overwriting must be prevented.
#### Unicode paths on Windows
Windows native paths use wide characters. Prefer `filesystem::path` and the appropriate native API representation at the boundary.
Converting a path to `std::string` does not universally establish a lossless UTF-8 representation.
Explicitly define the encoding used for Lua input and log output. Non-ASCII usernames and clip directories are ordinary cases to test.
#### Saving a clip
A useful application policy is:
1. Select a collision-resistant destination name.
2. Write to a temporary file in the destination directory.
3. Finalize the media container and close the file successfully.
4. Rename to the final name under an explicit collision policy.
5. Report success only after the required operations succeed.
Filesystem rename behavior and replacement rules depend on the platform. This sequence is a recovery design, not a universal guarantee of durability after power loss.
Some media writers choose the container from the extension; set the container explicitly or preserve a recognized extension when using temporary names.
#### Best practices
- Store settings in an appropriate user-writable application-data location.
- Do not depend on the installation directory being writable.
- Treat cleanup of failed temporary files as a checked best-effort operation.
- Preserve an existing user's file unless replacement is intentional.
#### Related notes
- [[Streams]]
- [[Windows Strings and Errors]]
- [[Sink Writer and File Finalization]]
