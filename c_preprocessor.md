Preprocessor
-------------

MyDef's macro system can replace most of the C preprocessors. However, for the convienience of importing legacy C code into MyDef, MyDef follows a simple rule to let most of the preprocessor lines pass through as is. 

The rule is: if a line starts with '#', which may be preceded with spaces, and immediately followed by "define", "undef", "include", "line", "error", "pragma", "if", "ifdef", "ifndef", "elif", "else", or "endif", the line will not be treated as comments and will get pass through. Since comments are being filtered at the beginning of MyDef parsing, we have to make this exception for all MyDef modules. This means even when other module than C is used, MyDef will still parse through lines looks like C preprocessor instead of treat them as comments. I guess those readers who did not read this chapter will have to find out this glitch on their own. 

Even when plain C preprocessor works, it will look like MyDef comments, thus confusing. So MyDef have work arounds for most of the C preprocessor needs. 

* #include
  We can use "$include" to include header files. MyDef will collect all "$include" lines together, and translate into "#include" preprocessor lines at the begining of the output file. The order of included files will not be observed. Rather, they will be ordered alphabetically including the leading angle bracket or quotation marks.

```
page: t
    subcode: main
        #line 1
        #include "foo.h"
        #include <bar>

        $include <c_test>
        $include "d_test"
        $include a_test
        $include b_test.h
```
It compiles into:
```
#include <a_test.h>
#include <c_test>
#include "b_test.h"
#include "d_test"

#line 1
#include "foo.h"
#include <bar>
```

As we see, direct '#' preprocessor line are passed without change, while "$include" lines are collected, translated, and re-ordered at the top. The translation rules are: if the name include leading quotation mark or angle bracket, they are being kept as is; or, if it has an file extension, it will be put under quotation marks; otherwise, ".h" will be appended and put into angle brackets. 

In addition, you will find MyDef will recognize common function calls from stdlib.h, string.h, and math.h, and auto matically include them for you if you forget, a handy feature for writing quick programs.


  



