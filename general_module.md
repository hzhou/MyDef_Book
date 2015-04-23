# Output Modules

Specialized tool works better. So if you tell MyDef what language or application you are working on, MyDef may be able to do a better job in predicting what you are trying to write and offer you more flexibility in expressing what is in your mind.

To tell MyDef what is your underlying language, we specify the module type. For example:

```
#---- t.def ----

page: helloworld
    #include <stdio.h>
    int main(){
        printf "Hello world!\n"
        return 0
    }
```

We can tell MyDef to compile it as C:
```
$ mydef_page -mc t.def
PAGE: helloworld
  --> [./helloworld.c]
```

"mydef_page" is the MyDef compiler. "-m" option specifies which output module to use. "-mc" tells MyDef to compile t.def as a C code. If we do not specify a module:

```
$ mydef_page t.def
Module type not defined in config!
```
It will complain and exit! It asks you to specify module type in config, we'll discuss how to do that later in the book.

The "page:" directive in the def file specifies the filename for the output. As we see, MyDef automatically assume an extension of .c. We'll explain how to specify different file extension in next section.

Let's examine the compiled output:
```
$ cat helloworld.c
#include <stdio.h>
int main(){
    printf("Hello world!\n");
    return 0;
}
```
MyDef completed the function call for printf, and added the semicolons that we've been missing. 

We can tell MyDef to assume no knowledge of underlying language. To do that, we use the general module:

```
$ mydef_page -mgeneral helloworld.def
PAGE: helloworld
    --> [./helloworld.txt]
$ cat helloworld.txt
#include <stdio.h>
int main(){
    printf "Hello world!\n"
    return 0
}
```

This time MyDef only assumes .txt extention for general text file, and it didn't try to guess what we mean here and translated the text as is.

A big portion of MyDef's feature works for general text objects, which includes subcodes and macros, and they are the most important features of MyDef. For this chapter and next chapter, we are going to cover these general features. For most of the example, we are going to assume general text and use the general output module.
