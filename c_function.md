## Function


### fncode and $list

Even though one can directly write C functions using notice C syntax, we would prefer using MyDef's special syntax. We need let MyDef be aware of the function structure and take control so it can help us manage the declarations and variable scopes. 

We can define function using "fncode" -- similar to "subcode". However, in order to give more control, they are not automatically included in the output. Function has to be explicitly included using "$list" or "$fcall" commands. This is to facilitate def libraries that potentially has many function defined but programmer only want to include those being used. 

For example:
```
fncode: test
    $print Hello world!

fncode: test2
    $print Good morning!

page: t
    $list test

```
It compiles into:
```
void test();

void test(){
    puts("Hello world!");
}

```

Function test is listed, but function test2 is omitted. Also note the declaration for included functions are always included at the beginning so that the order of function definitions will not matter (the orders are according to the order of $list commands).

"$list" only includes the function, but will not run the function. Function are called with native C statements:
```
fncode: test
    $print Hello world!

fncode: main2
    test()

page: t
    $list test, main2
```
It compiled into:
```
void test();

void test(){
    puts("Hello world!");
}

int main(int argc, char** argv){
    test();
    return 0;
}

```
Did you find we name the main function as "main2" strange? There is a reason. MyDef core module do not differentiate subcode or fncode, the C output module recognizes type fncode as C functions. Since each page starts with subcode "main", we cannot name the main function "main" again. So the convention is to name it as "main2" and the C module will translate it back to function "main". This is a little counterintuitive, but usually we will put "$list main2" inside a frame function such as "basic_frame" in std_c.def, then it will take place behind stage and won't matter any more.

There is another syntax to automatically list the function being used, "$fcall":
```
fncode: test
    $print Hello world!

page: t, basic_frame
    $fcall test
```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

void test();

int main(int argc, char** argv){
    test();
    return 0;
}

void test(){
    puts("Hello world!");
}

```
$fcall has to be either followed by function name (doesn't matter what the rest of the line is) or in the form of "var=functionName(...)".  

I should mention that MyDef is able to automatically include all fncode as functions. We can enable that by adding a page attribute "autolist: 1":
```
fncode: test
    $print Hello world!

page: t, basic_frame
    autolist: 1
    test()
```
It compiled into:
```
#include <stdio.h>
#include <stdlib.h>

void test();
char * get_s_int(char * s, int * p_n);

void test(){
    puts("Hello world!");
}

char * get_s_int(char * s, int * p_n){
    int n;

    n = 0;
    while(*s >= '0' && *s <= '9'){
        n *= 10;
        n += *s - '0';
        s++;
    }
    *p_n = n;
    while(*s == ' '){
        s++;
    }
    return s;
}

int main(int argc, char** argv){
    test();
    return 0;
}

```
It works, but what is that function get_s_int? It is a fncode in the current std_c.def. Every fncode is every fncode, include those included in standard library. (std_c.def is being updated quite frequently. By the time you read this book, that get_s_int function may be moved into other libraries.)

### Parameters and returns

Parameters to a function can be specified the same way as subcode parameters, by appending the list in parentheses:
```
fncode: test(s_name)
    $print Hello $s_name!

page: t, basic_frame
    $list test
    test("Hui")
```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

void test(char * s_name);

int main(int argc, char** argv){
    test("Hui");
    return 0;
}

void test(char * s_name){
    printf("Hello %s!\n", s_name);
}
```
Here MyDef adopts the same data type hints as declaring variables. As $global and $local, you can include the full type to make sure or for names that MyDef can't guess types:
```
fncode: test(char * YourName)
    $print Hello $YourName!

page: t, basic_frame
    $list test
    test("Hui")
```
It compiled into:
```
#include <stdio.h>
#include <stdlib.h>

void test(char * YourName);

int main(int argc, char** argv){
    test("Hui");
    return 0;
}

void test(char * YourName){
    printf("Hello %s!\n", YourName);
}

```

MyDef also will attempt to guess return type from "return" statement. And you always can opt to explicitly declare it using "$return_type":
```
fncode: test(char * YourName)
    $print Hello $YourName!
    $local n_ret=2
    return n_ret

fncode: test2
    $return_type char *
    return "Hello World!"

page: t, basic_frame
    $list test, test2
    test("Hui")

```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

int test(char * YourName);
char * test2();

int main(int argc, char** argv){
    test("Hui");
    return 0;
}

int test(char * YourName){
    int n_ret = 2;

    printf("Hello %s!\n", YourName);
    return n_ret;
}

char * test2(){
    return "Hello World!";
}

```
