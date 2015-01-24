Variable types
--------------

C is a statically typed language. That is good, so we have ways to really specify the program accurately and the C compiler can do a lot to check for the accuracy and optimize the program. However, having to declare a variable and specify the types explicitly is quite a hassle. We need MyDef try to declare them for us based on hints and convention.

One of the convention is called Hungarian notation, in which the variable name is prefixed with data type. Microsoft promotes Hungarian notation heavily and result in this conventio to be wide spread. However, the type information hinted in the variable name is not used by any of the development tools. That is a shame. 

MyDef does not exactly take the Hungarian notation, but recognizes following type hinting prefixes:

* i => int
* n => int
* n1 => int8_t
* n2 => int16_t
* n4 => int32_t
* n8 => int64_t
* ui => unsigned int
* u => unsigned int
* u1 => uint8_t
* u2 => uint16_t
* u4 => uint32_t
* u8 => uint64_t
* c => unsigned char
* uc => unsigned char
* b => int
* s => char *
* f => float
* d => double
* time => time_t
* file => FILE *
* has => int
* is => int

To declare variables, either use $local or $global. Alternatively, one can simply do an assignement and the new variable will automatically declared as local variable.
For example:
```
page: t, basic_frame
    $local is_winner=1, has_won=0
    $global c_a
    n_a=1
    s_a="hello"
    time_now=time(NULL)
```
which output to:
```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

unsigned char c_a;

int main(int argc, char** argv){
    int is_winner = 1;
    int has_won = 0;
    int n_a;
    char * s_a;
    time_t time_now;

    n_a = 1;
    s_a = "hello";
    time_now = time(NULL);
    return 0;
}

In this case, time.h is also automatically included.

Also note that the prefix f is assigned to type float. However, if there is a macro or page attribute "use_double: 1", prefix f will be assigned to type double instead. Some application can either work with float or double but may need balance its precision and speed. If the program exclusively uses the f prefix, one can use the "user_double" macro to switch between float and double. 

```
In addition, for some common short variable names, MyDef recognizes following:
* c => unsigned char
* i => int
* j => int
* k => int
* m => int
* n => int
* l => long
* f => float
* d => double
* count => int

For example:
```
page: t, basic_frame
    $local c,i,j,k,l,m,n,f,d
    count=0
```
compiles into:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv){
    unsigned char c;
    int i;
    int j;
    int k;
    long l;
    int m;
    int n;
    float f;
    double d;
    int count;

    count = 0;
    return 0;
}

```

All the prefix can be further prefixed with 't' or 'p'. 't' simply means "temporary" and should never be given to global variables. 'p', on the other hand, means "pointer". For example:
```
page: t, basic_frame
    $global pn_list, ppn_matrix
    $local pn_array[10], ppn_pointerarray[10], ts_buffer[100]
```
And t will compiles into:
```
#include <stdio.h>
#include <stdlib.h>

int* pn_list;
int** ppn_matrix;

int main(int argc, char** argv){
    int pn_array[10];
    int* ppn_pointerarray[10];
    char ts_buffer[100];

    return 0;
}

```

Of course, when variable name is not well patterned, MyDef will not able to figure out the type, and then it simply set to "void".
```
page: t, basic_frame
    $local p_list, something
```
It will compile to:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv){
    void p_list;
    void something;

    return 0;
}

```
Of course the C compiler will complain about "void" type, then the programmer will fix it by explictly specify the type. To explictly specify type, simply put the type in front to the varaible name in $local or $global:
```
page: t, basic_frame
    $local T * p_list, T something

```
It will compile into:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv){
    T * p_list;
    T something;

    return 0;
}

```

Finally, MyDef allows you to customize your implicit prefix and name type rules. It even let's you specify include files. For example:
```
page: t, basic_frame
    $register_name(hInst) HINSTANCE
    $register_prefix(hwnd) HWND
    $register_include(HWND) windows

    $global hInst, hwnd_main

```
It will compile into:
```
#include <stdio.h>
#include <stdlib.h>
#include <windows.h>

HINSTANCE hInst;
HWND hwnd_main;

int main(int argc, char** argv){
    return 0;
}

```

By the way, if you find it annoying of the stdlib.h that is always been included, you can always create your own frame subcode in which you can make it special.
