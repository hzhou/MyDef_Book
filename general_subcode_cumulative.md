# Cumulative subcode

Let's start with a frame subcode:

```
subcode: frame
    $call init
    $call content
    $call exit
```

However, there are some parallel features that we would like to keep together. We want to do something like this:

```
#---- feature 1 related stuff ---
subcode: init
    feature 1 init

subcode: content
    feature 1 content

subcode: exit
    feature 1 exit

#---- feature 2 related stuff ---
subcode: init
    feature 2 init

subcode: content
    feature 2 content

subcode: exit
    feature 2 exit

...
```

Alas, that will not work. upon running through MyDef, you will find the last "subcode: init" will replace all previously defined "subcode: init", likewise for "subcode: content" and "subcode: exit".

However, this will work:
```
#---- feature 1 related stuff ---
subcode:: init
    feature 1 init

subcode:: content
    feature 1 content

subcode:: exit
    feature 1 exit

#---- feature 2 related stuff ---
subcode:: init
    feature 2 init

subcode:: content
    feature 2 content

subcode:: exit
    feature 2 exit

...
```
You have noticed the difference of "subcode::" vs previous "subcode:", don't you? All the subcode blocks will be concatenated in this case.

At this point, it beneficial to understand the order of MyDef processing. MyDef will process your main def file first, from first line to last line, then it will process all the included def file in sequence. If the included def file includes additional def files, they are appended to the list. Lastly, MyDef will process any standard library -- those automatically included by the output modules. 

For example:
```
#---- std_www.def (in $MYDEFLIB/)----
subcode: basic_frame       
    $call @process          
    <!doctype html>         
    $html                   
        $head               
            DUMP_STUB meta  
            $call @meta     
            $call @script   
        $body               
            $call @content  

subcode:: content
    $p
        std_www

#---- my_frame.def ----
subcode: basic_frame
    <!doctype html>         
    $html                   
        $body               
            $h1
                My Page!
            $call content  

subcode:: content
    $p
        my_frame

#---- special_frame.def ----
include: my_frame.def

subcode:: content
    $p
        special_frame

#---- page.def ----
include: special_frame

subcode: basic_frame
    $call content

subcode:: content
    $p
        page specific

page: t, basic_frame

```

Can you guess the output?  The output will be:
```
<p>
    page specific
</p>
<p>
    special_frame
</p>
<p>
    my_frame
</p>
<p>
    std_www
</p>
```

For completeness, I should mention here that in addition to "subcode:" and "subcode::", there are also "subcode:-" and "subcode:@". They only differ in actions when MyDef find previouly existing subcode definitions. "subcode:" will simply replace, "subcode::" will append, "subcode:-" will prepend, and "subcode:@" will only be used as the default (that is, it will be ignored unless nowhere else defines it). In practice, only "subcode:" and "subcode::" are commonly used.
    
You may also be wondering in the std_www.def, what does the '@` in "$call @process" mean? If we have this:
```
page: t
    $call something
```
And MyDef will complain:
```
$ !mydef_page -mgeneral test.def
PAGE: t
    [test.def:3] Code something not found!
  --> [./t.txt]
```
But if we allow calling non-existent subcode (as placeholders), then we can use '@' to supress the complaints:

```
page: t
    $call @something
```
```
$ !mydef_page -mgeneral test.def
PAGE: t
  --> [./t.txt]
```
t.txt will be empty, but we know that, right?

