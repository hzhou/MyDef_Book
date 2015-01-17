# A quick tour

Let's start with the "hello world" program in C:

    #include <stdio.h>
    
    int main(){
        printf("Hello world!\n");
        return 0;
    }
    
Now, let's put this simple program in MyDef:

    page: hello_world
        module: c
        output_dir: out
        
        subcode: main
            $include stdio.h
            $list n_main
            
        fncode: n_main
            printf("Hello world!\n");
            return 0;

Well, you may immediately get the impression that MyDef is more complicated than basic C! Let me try change your impression by a second version of MyDef:

    page: hello_world, basic_frame
        subcode: main
            $print Hello world!
            
How about that! 

