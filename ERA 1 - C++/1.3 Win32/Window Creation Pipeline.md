A *window class* defines properties and behavior all windows created from that class will share.
#### Window Class Declaration
Windows has a structure called `WNDCLASSW`:
```cpp
	#include <windows.h>
	
	// don't worry about this function atm
	LRESULT CALLBACK window_proc(HWND hwnd, UINT m, WPARAM wp, LPARAM lp) {
		return DefWindowProcW(hwnd, m, wp, lp);
	} 
		
	int main() {
		// zero initialize window class
		WNDCLASSW wc{}; 
		
		// function pointer identifying the window procedure
	    wc.lpfnWndProc = window_proc;
	    
	    // handle identifying executable module registering the class
	    wc.hInstance = GetModuleHandleW(nullptr); ?? what is this?
	    
	    // class string identifier
	    wc.lpszClassName = L"mc_window";
	}
```
#### Window Class Registration
To register a class we use the `RegisterClassW()` function. It takes a pointer to our structure and registers the class with windows. On success it returns a nonzero`ATOM`, on failure it returns `0`:
```cpp
	ATOM res = RegisterClassW(&wc);
	
	if (res == 0) { 
		DWORD err = GetLastError();
	}
```
#### Window Creation
To create a window, we use the `CreateWindowExW()` function. It creates an actual window and returns its handle with `HWND` type on success, and on failure it returns `nullptr`.
The function takes 12 arguments:
1. Extended window styles
2. Registered window class name
3. Window title
4. Window style
5. Horizontal position
6. Vertical position
7. Initial width
8. Initial height
9. Owner/parent window
10. Menu
11. Handle to our executable module
12. Additional creation data
Here's an example:
```cpp
HWND window = CreateWindowExW(
    0,                           // no extended window style
    L"mc_window",                // mc_window is our class name
    L"Modest Clipper",           // Modest Clipper is our window title
    WS_OVERLAPPEDWINDOW,         // standard resizable application 
    CW_USEDEFAULT,               // windows decides horizontal position
    CW_USEDEFAULT,               // windows decides vertical position
    800,                         // 800 is the window width
    600,                         // 600 is the window height
    nullptr,                     // no parent or owner window
    nullptr,                     // no menu
    GetModuleHandleW(nullptr),   // handle to executable module
    nullptr                      // no additional creation data
);
```
- Creating a window isn't the same as displaying it. Normally, window created with the style shown above is initially hidden.
- To request display, we can use the `ShowWindow(window, SW_SHOW)` function.
#### Example Code

```cpp
#include <windows.h>
#include <iostream>

// include windows import lib for MSVC linker
#pragma comment(lib, "User32.lib") 

// callable function by windows whenever 
// the window needs to process certain events
LRESULT CALLBACK window_proc(HWND hwnd, UINT m, WPARAM wp, LPARAM lp) {
    // when user closes the window, respond like this:
    if (m == WM_DESTROY) {
	    // terminate thread's message loop
        PostQuitMessage(0); 
        return 0;
    }
    // let windows handle if we haven't
    return DefWindowProcW(hwnd, m, wp, lp); 
}

void init_wc(WNDCLASSW *wc, HINSTANCE *instance) {
    wc->lpfnWndProc = window_proc; // pass windows a function pointer
    wc->hInstance = *instance;
    wc->lpszClassName = L"mc_window";
}

int main() {
    HINSTANCE instance = GetModuleHandleW(nullptr);
    WNDCLASSW wc{};

	// create a window class
    init_wc(&wc, &instance); 
	
	// register the window class
    if (!RegisterClassW(&wc)) { 
        std::cerr << "registration failed: " << GetLastError() << '\n';
        return 1;
    }

	// create the window
    HWND window = CreateWindowExW(  
        0,
        wc.lpszClassName,
        L"Modest Clipper",
        WS_OVERLAPPEDWINDOW,
        CW_USEDEFAULT, CW_USEDEFAULT,
        800, 600,
        nullptr, nullptr,
        instance,
        nullptr
    );

    if (!window) {
        std::cerr << "window creation failed: " << GetLastError() << '\n';
        return 1;
    }
	
	 // display the window
    ShowWindow(window, SW_SHOW); 

	 // zero initialize a windows message
    MSG message{};

	// message loop
    while (true) { 
	    // waits for a message
        BOOL result = GetMessageW(&message, nullptr, 0, 0); 
        
        if (result == -1) // error
            return 1;
        if (result == 0)  // WM_QUIT
            break;
        
        // keyboard message translations
        TranslateMessage(&message); 
        
        // delivers the message to the appropiate window procedure
        DispatchMessageW(&message); 
    }

    return 0;
}
```

