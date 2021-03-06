## win32/windows.def

### register_wndclass
```
# --------------------------------------
#  register_wndclass, create_window, wndproc
# ---- Register Window Class ----
subcode: register_wndclass(name, proc)
    $local WNDCLASSEX wc
    wc.cbSize = sizeof(WNDCLASSEX);
    wc.hInstance = cur_instance
    wc.cbClsExtra = 0;

    $(if:wc_style!=1)
        wc.style = 0

    $(if:wc_icon!=1)
        wc.hIcon = NULL
        wc.hIconSm = NULL

    $(if:wc_cursor!=1)
        wc.hCursor = LoadCursor(NULL, IDC_ARROW);

    $(if:wc_background!=1)
        wc.hbrBackground = NULL
    $(if:wc_menu!=1)
        wc.lpszMenuName = NULL

    wc.lpszClassName = $(name)

    $(if:proc=DIALOG)
        wc.lpfnWndProc = DefDlgProc
        wc.cbWndExtra = DLGWINDOWEXTRA
    $(elif:proc:8=WndProc_)
        $(block:global_init)
            $call wndproc, $(proc)
        wc.lpfnWndProc = $(proc)
        wc.cbWndExtra = 0

    RegisterClassEx(&wc);
```

`$call register_wndclass, test, WndProc_main` will be simply expanded into populating a `WNDCLASSEX` structure and calling `RegisterClassEx`. We have a bunch of preprocessing switches here to define the default. Optionally, we can set the cursor, style, icon, menu, and background by calling following subcodes:

```
subcode: wc_cursor(p)
    $local WNDCLASSEX wc
    $(if:p:4=IDC_)
        wc.hCursor = LoadCursor(NULL, $(p))
        $(export:wc_cursor=1)

subcode: wc_style(p)
    $(if:wc_style!=1)
        wc.style=$(p)
    $(else)
        wc.style |= $(p)
    $(export:wc_style=1)

subcode: wc_icon(p)
    $(if:p:4=IDI_)
        wc.hIcon = LoadImage(cur_instance, MAKEINTRESOURCE($(p)), IMAGE_ICON, GetSystemMetrics(SM_CXICON), GetSystemMetrics(SM_CYICON), LR_DEFAULTCOLOR|LR_SHARED);
        wc.hIconSm = LoadImage(cur_instance, MAKEINTRESOURCE($(p)), IMAGE_ICON, GetSystemMetrics(SM_CXSMICON), GetSystemMetrics(SM_CYSMICON), LR_DEFAULTCOLOR|LR_SHARED);
        $(export:wc_icon=1)

subcode: wc_menu(p)
    wc.lpszMenuName=(LPCTSTR)$(p)
    $(export:wc_menu=1)

subcode: wc_background(p)
    wc.hbrBackground=(HBRUSH)$(p)
    $(export:wc_background=1)

```

For example, the following code:
```
$call wc_background, COLOR_BACKGROUND
$call register_wndclass, "test", WndProc_main
```
will register the window class with `COLOR_BACKGROUND` background color.

### create_window

The bulky `CreateWindowEx` routine is similarly dealt with:
```
# ---- Create Window ----
subcode: create_window(hwnd, name)
    $(if:!cw_style_ex)
        $(set:cw_style_ex=0)
    $(if:!cw_style)
        $(set:cw_style=WS_OVERLAPPEDWINDOW)
    $(if:!cw_x)
        $(set:cw_x=CW_USEDEFAULT)
    $(if:!cw_y)
        $(set:cw_y=CW_USEDEFAULT)
    $(if:!cw_w)
        $(set:cw_w=CW_USEDEFAULT)
    $(if:!cw_h)
        $(set:cw_h=CW_USEDEFAULT)

    $(hwnd)=CreateWindowEx($(cw_style_ex), $(name), $(name), $(cw_style), $(cw_x), $(cw_y), $(cw_w), $(cw_h), NULL, NULL, cur_instance, NULL)
    
subcode: cw_style_ex(p)
    $(if:!cw_style_ex)
        $(export:cw_style_ex=$(p))
    $(else)
        $(export:cw_style_ex=$(cw_style_ex)|$(p))

subcode: cw_style(p)
    $(if:!cw_style)
        $(export:cw_style=$(p))
    $(else)
        $(export:cw_style=$(cw_style)|$(p))

subcode: cw_size(x, y, w, h)
    $(export:cw_x=$(x))
    $(export:cw_y=$(y))
    $(export:cw_w=$(w))
    $(export:cw_h=$(h))

subcode: cw_dim(x, v)
    $(export:cw_$(x)=$(v))

```
### Window Proc

The window proc is writen as an extension (in Perl and directly working with MyDef internals). I will have explain how MyDef internal works before explaining this part, but nevertheless, I'll list the code here (nobody is going to prevent you to venture):
```
# ---- WNDPROC -----------------------------------------
#  $(block:global_init)
#      $call wndproc, WndProc_main
#
perlcode: wndproc
    $(set:C=MyDef::output_c::)
    $if $param=~/(WndProc|DlgProc)_(\w+)/
        my ($type, $name)=($1, $2)
        my $funcname="$type\_$name"
        my $context=$(C)start_function_block($funcname, "HWND hwnd, UINT msg, WPARAM wparam, LPARAM lparam")
        $if $context
            $$(C)cur_function->{ret_type}="LRESULT CALLBACK";
            $call window_proc_core
            $(C)finish_function_block($context)

subcode: window_proc_core
    my $out=$$(C)out
    my $codes=$MyDef::def->{codes}
    # ---- process window msg ---------------
    MyDef::compileutil::call_sub("\@pre_WndProc_$name")
    push @$out, "switch(msg){";
    &call push_indent_block
        my %msg_hash;
        $foreach $k in sort(keys(%$codes))
            $if $k=~/$name\_on\_(WM_\w+)/
                my $msg=$1
                $call window_proc_on_msg
        $if !$msg_hash{"WM_DESTROY"} and $name eq "main"
            MyDef::compileutil::call_sub("msg_destroy")
            # $call msg_destroy

    push @$out, "}";
    $if $type eq "WndProc"
        push @$out, "return DefWindowProc(hwnd, msg, wparam, lparam);"
    $elif $type eq "DlgProc"
        push @$out, "return FALSE;"

subcode: window_proc_on_msg
    $msg_hash{$msg}=1
    $if $msg eq "WM_ALLKEY"
        push @$out, "case WM_KEYDOWN:"
        push @$out, "case WM_KEYUP:"
        push @$out, "case WM_SYSKEYDOWN:"
        push @$out, "case WM_SYSKEYUP:"
    $elif $msg eq "WM_ALLCOMMAND"
        push @$out, "case WM_COMMAND:"
        push @$out, "case WM_SYSCOMMAND:"
    $else
        push @$out, "case $msg:"
    &call push_indent_block
        # push @$out, "printf(\"$name\_on_$msg\\n\");"
        MyDef::compileutil::call_sub("\@$name\_on_$msg")
        push @$out, "break;"

# -- messages --
subcode: msg_destroy
    case WM_DESTROY:
        PostQuitMessage(0)
        return 1

```
