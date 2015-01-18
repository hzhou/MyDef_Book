# A quick glimpse

Let's start with the "hello world" program in C:

    #include <stdio.h>
    
    int main(){
        printf("Hello world!\n");
        return 0;
    }

Assuming we have gcc:
    
    $ gcc -o helloworld helloworld.c
    $ ./helloworld
    Hello world!

Now, let's put this simple program in MyDef:

    page: helloworld
        subcode: main
            $include stdio
            $list n_main
            
        fncode: n_main
            printf("Hello world!\n");
            return 0;

To compile:

    $ mydef_page.pl -mc helloworld.def
    PAGE: helloworld
      ---> ./Makefile
      --> [./helloworld.c]
    $ make
    gcc -c   -o helloworld.o helloworld.c
    gcc   -o helloworld helloworld.o
    $ ./helloworld
    Hello world!

Some explanations are needed. First, MyDef files are indentation based, like Python. Experienced programmers always use proper indentations anyway, so using indentation as basic structure unit should not be a burden.

The "page:" block designates one output source file. "page: helloworld" will be compiled into source file helloworld.c. 

The "subcode:" block designates a code block, similar to a function or subroutines in typical programming languages. Similar but not quite the same. In fact, MyDef subcodes are actually macros.

"subcode: main" is the entry point of a "page". MyDef starts compiling (or translating) at subcode: main. All other subcodes are ignored unless being referenced by the main subcode. 

"fncode" is actually an alias for "subcode". We use "fncode: n_main" to signify that this subcode is destined to be a c function.

For the actual code, we write the exact c syntax. If we know how to program in c, then we know how to program in c with MyDef.

At this point, you are probably asking the question "why?" If we write the exact code in MyDef as we do in the primary language directly, why we want to use MyDef just to acquire an additional layer of complexities?

The extra layer MyDef brings allows for extra layer of flexibility. For example, we can tuck most of the routine structures of a basic c program into a library of subcodes -- in this case, subcode: basic_frame, which is included in the MyDef standard library. In addition, we could use some heuristic intelligence provided by this additional layer. So we could write the same program as:

    page: hello_world, basic_frame
        $print Hello world!
            
How about that! Here, MyDef calls subcode: basic_frame in std_c.def (included in MyDef standard installation and always loaded for c), automatically recognize the starting code line as part of main subcode -- which become the main function as the main code is already supplied by basic_frame. MyDef also automatically added include files because $print requires it. And "$print" is a MyDef special function that not only make the quotation marks optional, but also will supply newline automatically and parse variables into proper printf format if necessary; In addition, MyDef applies heuristic intelligence so both semicolons and "return 0" become optional (MyDef adds them when they are missing.)

Instead of thinking about how to write the program to do what you want to do, like novice programmers learning how to program, MyDef let master programmers to focus on what they want to do and write the best way to reflect their vision, even their primal language does not allow that.
