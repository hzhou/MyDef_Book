## std_win32.def

Each output module in MyDef will automatically include a sandard macro def file. For output_win32, this is std_win32.def.

We will simply walk through this include file.

```
include: std_c.def
include: ext.def
include: win32/windows.def
include: win32/gdi.def
```

We are still writing C, so `std_c.def`. `ext.def` provides macros for writing second layer MyDef extensions (we'll cover that in the extension part of this book). `win32/windows.def` deals with macros for register and create windows. `win32/gdi.def` deals with macros to faciliate GDI routines.

```
subcode: win32_autoload
    $uselib user32
    $register_prefix(ui) UINT
    $register_prefix(lpstr) LPSTR

    $register_prefix(hwnd) HWND
    $register_prefix(hdc) HDC
    $register_prefix(hmenu) HMENU
    $register_prefix(rect) RECT
    $register_prefix(hfont) HFONT

    $register_prefix(bmph) BITMAPINFOHEADER
    $register_prefix(bmp) DWORD *

    $define(snprintf) sprintf_s
```

Some windows specific data types. By register_prefix, variable names such as `hwnd_main` will automatically having a type 'HWND'. 

```
subcode: basic_frame
    $call @global
    $list WinMain

fncode: WinMain
    $call main2
```

The basic_frame so the following would work:
```
page: test, basic_frame
    code lines
    ...
```
The code under the page will become `subcode: main2` (as the subcode main is taken by the basic_frame).

```
#---------------------------------------- 
subcode: win_main(mainname)
    $call register_wndclass, "$(mainname)", WndProc_main
    $call create_window, hwnd_main, "$(mainname)"
    $call win_show, hwnd_main

```
This macro is rather created for `hello world` demo :). Practically, it is often desirable to write register_wndclass, create_window and win_show directly as we often need customize the window class, window size, insert code in between, etc.

Unlike typical frameworks, we are not writing wrappers. We are in fact writing win32 code as in Microsoft's original `Hello World` example, except we have established some conventions so we do not have to repeat ourselves.

Let's note the name of window procedure `WndProc_main`. The convention we adopted here is always use the pattern `WndProc_name` and the name here will be used to connect all window message handlers. For example, here we use `WndProc_main`, so if we later define a subcode:

```
subcode: main_on_WM_PAINT
    code 1
    code 2
    ...
```
The code will essentially become a switch case inside `WndProc_main`:
```
case WM_PAINT:
    code 1
    code 2
    ...
```

With this convention, rather than writing a super long window procedrue functon, we can organize the code according to their context.

```
#---------------------------------------- 
subcode: win_show(hwnd)
    NEWLINE
    ShowWindow($(hwnd), n_cmdshow)
    UpdateWindow($(hwnd))

subcode: win_loop
    NEWLINE
    $local MSG msg
    $while GetMessage(&msg, NULL, 0, 0)>0
	TranslateMessage(&msg)
	DispatchMessage(&msg)

```
If you are familiar with windows programming, then these are straight forward. 

Remember, we are merely writing conventions here. When specific customzation is needed, either write new specific subcode or simply write these function calls directly.

The rest of `std_win32.def` is simply a collection of convenience routines. Feel free to explore on your own or even add in your own routines.
