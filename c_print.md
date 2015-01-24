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

MyDef's C module currently recognizes following variable types:

* char * ==> %s
* [AnyType *] ==> %p
* float or double ==> %g
* int or long ==> %d
* unsigned char ==> %d
* char ==> %c

Relatively limited compared to the full specification of printf. Again, MyDef is trying to opimize at the semantic side which focuses on the 80% most common conventions. For the rest 20%, we can go specific, e.g. using printf's formal format. MyDef allows you to mix and match:

```
page: t, basic_frame
    s_firstname="Hui"
    s_lastname="Zhou"
    $print "Hello World, $s_firstname %8s!", s_lastname
```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv){
    char * s_firstname;
    char * s_lastname;

    s_firstname = "Hui";
    s_lastname = "Zhou";
    printf("Hello World, %s %8s!\n", s_firstname, s_lastname);
    return 0;
}

```

You may have noticed the detail that $print will append a '\n' for you, which we believe mmakes up 80% of the cases. What if you don't want to have the line break? There is a trick:

```
page: t, basic_frame
    s_firstname="Hui"
    s_lastname="Zhou"
    $print Hello World, -
    $print $s_firstname -
    $print "%s-", s_lastname
    $print !
```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv){
    char * s_firstname;
    char * s_lastname;

    s_firstname = "Hui";
    s_lastname = "Zhou";
    printf("Hello World, ");
    printf("%s ", s_firstname);
    printf("%s", s_lastname);
    puts("!");
    return 0;
}
```

The '-' at the end of the format tells MyDef do not append '\n'. 

Did you notice that MyDef turns simple string print into puts instead of printf? Don't worry, that is not a bad thing. "puts" is much faster than "printf".

The reason MyDef strives to provide a simple $print statement is because most of the usage of direct printf is for quick prototyping and debugging, which requires quick and often temporary print. It is easy to print too much and sometime we may have a difficult time to spot the critical message. One solution is to print in color. Assuming your terminal accepts ANSI escape sequence, MyDef offers some quick solution:
```
page: t, basic_frame
    s_name="Hui"
    $print Hello, $green$s_name$reset!
    $print something
```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv){
    char * s_name;

    s_name = "Hui";
    printf("Hello, \x1b[32m%s\x1b[0m!\n", s_name);
    puts("something");
    return 0;
}

```

MyDef supports red, green, yellow, blue, magenta, and cyan. You need remember to print '$reset' at some point, or all your folloing prints will be in color, which beats your original purpose. MyDef also provides a shorter solution:
```
$print Hello, $green{$s_name}!
```
The curly brackets marks the range, and MyDef will automatically add $reset at the end of your range. 

Did I mention that MyDef never tries to be perfect? If you try something like this:
```
page: t, basic_frame
    $local int greentea
    greentea=10
    $print greentea =  $greentea!
```

You will find you just printed 'tea!' in green! If the scenerio is rare, MyDef will choose to ignore and opt for simplicity. It's a quick print; you see something wrong, just fix it; use '%s' format for example.




