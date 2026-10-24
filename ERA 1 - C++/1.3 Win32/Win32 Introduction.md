*Win32* is a windows API. It provides functions and data types used to interact with the windows OS. 
#### Windows Types
Win32 uses its own types instead of the standard C++ types for compatibility and explicitness. 
- `DWORD` u32
	- Commonly used for flags, error codes, sizes.
- `BOOL` i32 -> true is non-zero, false otherwise
- `UINT` u32
	- Commonly used for counts, identifiers, message codes.
- `HWND` handle representing a window  
- `HANDLE` generic handle type
- `HDC` handle to a device context
- `WPARAM` pointer sized unsigned message parameter 
- `LPARAM` pointer sized signed message parameter
- `LRESULT` pointer sized signed integer representing message result
#### Handles
A *handle* is a value used to identify a resource through an API.
For example, `HWND window` will be used to identify a windows window. 
- Different handle types improve type safety, preventing accidental misuse of unrelated Windows resources.
- The advantage is that you do not need to know the internal structure of what the handle points to. 
- Handle ownership and cleanup will be explored in later chapters.
- A handle is essentially an identifier. A handle value could be `0x00010042`. This is not where the window is stored in memory. A handle asks windows "find this window based on this identifier".
- Although `HWND` is represented as a pointer type, its value is not guaranteed to be a valid address of a C++ object. It is an identifier interpreted by Windows, so we must not dereference it.
- `HWND` is represented in C++ as a pointer type to a struct.
Simplified, looks like:
```cpp
struct HWND__ {
	int unused;
};
typedef HWND__* HWND;
```
- More accurately, a handle is an opaque pointer. 
	- Opaque simply means the internal details are hidden from you. 
#### Win32 Functions
- Functions in the Win32 API generally return a value representing whether it failed or not, and sometimes also some additional information. 
	- The type the functions return changes from function to function, and it seen in documentation. It could be `UINT`, `DWORD`, `HRESULT` and probably some others.
- The functions called are not implemented in the project's `.cpp` files, including `windows.h`. 
	- The compiler knows that the function exists from the header file, but the linker needs to resolve the function reference. 
		- To supply the linker with information, we use important libraries (`.lib`).
		- Import libraries do not contain the actual implementation either, though.
	- The implementation itself is provided through windows `.dll` files. 
		- Windows resolves the DLL import at runtime. 
- `WINAPI` is a macro specifying a calling convention. It is an additional compile time specifier, placed between the return t
    - On x86 Windows, it expands to `__stdcall`.
    - On x64 Windows, `__stdcall` is accepted but ignored.
    - Windows callbacks commonly use `CALLBACK`, which specifies the same convention.
- Many Win32 functions usually end with either `A` or `W`. They differ on how they represent text.
	- The `A` variant stands for ANSI, which is a historical windows naming convention, where the function accepts strings consistent of `char` elements. It generally interprets their types using a windows code page, which defines how byte characters correspond to characters. 
		- Old windows code pages have limited characters selections and therefore don't even use unicode encodings. 
		- Windows can also use utf-8 as an active code page, but it shouldn't be assumed.
	- The `W` variant stands for wide characters. Strings are prefixed with `L`, which creates a wide-character string literal, using `wchar_t` elements. 
		- On windows, `wchar_t` is 2 bytes, and windows uses utf-16 encoding. 
	- If the suffix is omitted, sometimes windows defines text functions unsuffixed names as preprocessor macros, defined with the correct suffix depending on whether a specific unicode is defined (for example, `#define UNICODE`).
	- Functions which don't deal with encoded text, such as `GetLastError()`, generally don't use these suffixes. 
#### Error Handling
- Win32 functions have documented return values indicating success or failure.
    - Failure indicators differ depending on the function.
- `GetLastError()` retrieves the calling thread's stored "last error" code.
    - Each thread stores its own "last error" value.
    - Successful functions do not necessarily reset the previous error.
    - Therefore, a nonzero last-error value doesn't necessarily mean the previous function failed.
- When a function fails and its documentation supports `GetLastError()`, retrieve the error immediately.
- Not every Win32 function uses `GetLastError()`; some use other mechanisms, such as returning an `HRESULT`.