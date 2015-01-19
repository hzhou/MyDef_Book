# General Module

Over time, MyDef has developed quite a few speicialized output modules such as C, Perl, WWW, each with unique features specific to each language. however, the most important core features treats all source code as general text and simply refactors the text according to semantic structures perceived by the program author.

In fact, we can use MyDef's general module to compose source code for any programming language, or any text files. For example, we can write a novel:
    
    include: chap1.def
    include: chap2.def
    ...
    
    page: novel
        type: txt
        subcode: main
            $call cover
            $call prefix
            $call chap1
            $call chap2
            ...
            $call epilog
            
    subcode: cover
        ...
    subcode: prefix
        ...
    subcode: epilog
        ...

Or a C program:

    page: helloworld
        type: c
        subcode: main
            #include <stdio.h>
            int main(){
                printf("Hello world!\n");
                return 0;
            }

To compile and run:

    $ mydef_page.pl -mgeneral helloworld.def
    PAGE: helloworld
      --> [./helloworld.c]
    $ gcc helloworld.c
    $ ./a.out
    Hello world!

In this chapter, we will cover all the core features of MyDef, and we'll exclusively use the general module for all our examples. Once we are finished with this chapter, we will be tempted to use MyDef write all text files we encounter in work, except, maybe those hello world type of texts that does not have any semantic structures beyond the surface.    