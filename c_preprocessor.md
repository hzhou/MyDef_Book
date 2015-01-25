Preprocessor
-------------

MyDef's macro system can replace most of the C preprocessors. However, for the convenience of importing legacy C code into MyDef, MyDef follows a simple rule to let most of the preprocessor lines pass through as is. 

The rule is: if a line starts with '#', which may be preceded with spaces, and immediately followed by "define", "undef", "include", "line", "error", "pragma", "if", "ifdef", "ifndef", "elif", "else", or "endif", the line will not be treated as comments and will get pass through. Since comments are being filtered at the beginning of MyDef parsing, we have to make this exception for all MyDef modules. This means even when other module than C is used, MyDef will still parse through lines looks like C preprocessor instead of treat them as comments. I guess those readers who did not read this chapter will have to find out this glitch on their own. 

Even when plain C preprocessor works, it will look like MyDef comments, thus confusing. So MyDef have work around for most of the C preprocessor needs. 

### #include
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

In addition, you will find MyDef will recognize common function calls from stdlib.h, string.h, and math.h, and automatically include them for you if you forget, a handy feature for writing quick programs.

### #define
Single line "#define" will work, like "#include", directly pass through without translations. There is a $define MyDef syntax, MyDef will collect and output near the top of the output file. 
  
There are two flavors of "$define" syntaxes.
```
$define macroname ...
```
and
```
$define(macroname) definition...
```
The former syntax will simply relace "$define" into "#define" and become equivallent. The latter however, will be collected and gathered at the top of the output file, just like "$include" directives. This is similar to "$global" directive to allow user to define global macros near where they are being used but still not scattered everywhere.

A common pattern in C programming is to define a list of constants like:
```
#define name_a 0
#define name_b 1
#define name_c 2
#define name_d 3
#define name_e 4
...
```
Often the actual values of the macros are not important but they need to be unique. They are often associated with features. So every time we add a feature, we need add a new definition. This is become inconvenient. First, at the place of definition -- a global include file -- readers have no clues how these names are used, and become distractions. On the other hand, when we are writing or reading the part of code defines the feature, we have no clue whether the name is being defined and whether it is consistent with names from other features. Of course, nothing we can't resolve by consulting multiple files back and forth, but if we are bothered by such, we can use MyDef to cook up a solution. For example:
```
subcode: _autoload
    $(setmacro:base=0)

subcode: define_name(name)
    $define($(name)) $(base)
    $(setmacro:base+=1)

page: t
    subcode: main
        $call define_name, name_a
        $map define_name, name_b, name_c, name_d, name_e


```
Each call to define_name can be put to where the definition is relavant, and they will all be collected together.  It compiles to the top of file:
```
#define name_a 0
#define name_b 1
#define name_c 2
#define name_d 3
#define name_e 4

```

### Other preprocessor

"$(if)", "$(elif)", and "$(else)" are similar in its preprocessing power as C preprocessor's "#if" and its family. Of course they are not equivallent, and cannot be literarally switched. But they can easily switched semantically. However, MyDef factors the code semantically, which prevents semantic units to be split out of context. MyDef also maintains the indentation to visually reflect semantic structures. On the other hand, C's "#if" switches are often being used to mix codes from different features together. Mixing context, therefore increase complexity, makes semantic comprehension more difficult.

Other preprocessor directives such as "#line", "error", "pragma" are fine, and they should be used directly.







