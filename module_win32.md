# Win32

Unlike the other output modules, the output_win32 module is not working with a programing language (unlike Perl, C, etc.). Rather, it is merely an application in C. In fact, it inherits all C extensions. Every tricks we developed in the C module can be used to write for windows. 

It is not a separate language, however, Win32 programming contains many windows specific boilerplates and conventions. With a specific output module, we can automatically import these conventions as well as having a better organizations in the standard def library. 

In fact, if you use output_c module and include "std_win32.def", for most part it is identical as using the output_win32 module.

This chapter will serve as an example of how to create custom conventions for domain specific applications. We'll simply walk through the standard include files: `std_win32.def`, `win32/windows.def`, and `win32/gdi.def`. Before we start, let's review our `hello world`:

```
page: test, basic_frame
    $global hwnd_main
    $call wc_background, COLOR_BACKGROUND
    $call register_wndclass, "test", WndProc_main
    $call create_window, hwnd_main, "test"
    $call win_show, hwnd_main
    $call win_loop

# -------------------------------------------------
subcode: main_on_WM_PAINT
    &call on_paint
        $call quick_font, "Times New Roman", 72
        TextOut(hdc, 10, 10, "Hello World!", 12)
    return 0
```
