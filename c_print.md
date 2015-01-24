Print
------

Now that MyDef holds the knowledge of variable types, it should be able to assist you with all the format details of the printf function, and let you focus on what you want to print. Let's start with an example:

```
page: t, basic_frame
    s_name="Hui"
    $print Hello World, $s_name!
```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv){
    char * s_name;

    s_name = "Hui";
    printf("Hello World, %s!\n", s_name);
    return 0;
}

```



