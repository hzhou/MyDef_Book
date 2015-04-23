## Structure

"struct" is the next essential C component and it is often quite tedious to maintain. In addition to the need to specify both types and names of elements, its member declaration and where it is being used is often forced to be separate, which creates constant need going back and forth. MyDef offers "$struct" syntax to remedy it.

```
page: t, basic_frame
    $struct(mystruct) n_id, s_firstname, s_lastname
    $register_name(record) struct mystruct

    $local record
    record=(1, "Hui", "Zhou")

    $print "Hello %s!", record.s_firstname

    $struct(mystruct) char * interest
    record.interest="programming"

    $print "I see, you like %s.", record.interest

```
It compiles into:
```
#include <stdio.h>
#include <stdlib.h>

struct mystruct {
        int n_id;
        char * s_firstname;
        char * s_lastname;
        char * interest;
};

int main(int argc, char** argv){
    struct mystruct record;

    record.n_id = 1;
    record.s_firstname = "Hui";
    record.s_lastname = "Zhou";
    printf("Hello %s!\n", record.s_firstname);
    record.interest = "programming";
    printf("I see, you like %s.\n", record.interest);
    return 0;
}

```
As we see, all the tricks we have for hinting variable types applies here.  In addition, MyDef recognizes simple tuple-like assignment:
```
record=(1, "Hui", "Zhou")
```
It is not a real support of tuples, simply an understanding what we mean under the context.

MyDef let's us cumulatively define structure members. It will detect duplications, so having the same structure definitions at multiple places are ok. MyDef let's you do it, does not mean it endorses it as good style. MyDef's philosophy is "What you see is what you think", if your mind is thinking like that when writing the code, MyDef let's you write like that.
