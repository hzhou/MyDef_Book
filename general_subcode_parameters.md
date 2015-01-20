# More About Subcode

Subcode with Parameters
-----------------------

One of the main purpose of subcode is to avoid code duplication. However, we often find ourselves writing similar code that are not exactly the same, in which case, the subcode need to carry parameters.

To define a subcode with parameter, simply append the parameter list after the name of the subcode. If there are multiple parameters, simply seperate them with commas:
```
subcode: A(name)
    Hello, $(name)

subcode: B(firstname, lastname)
    Hello, $(firstname) $(lastname)!
```

Inside the subcode, the parameters are refferred exactly the same way as macros. When we call the subcode with parameters, we need to append the values of the parameters:

```
page: t
    $call A, Hui
    $call A "friend"
    $call A  you again!

    $call B, Hui, Zhou
```
This will compile into:
```
Hello, Hui
Hello, "friend"
Hello, you again!
Hello, Hui Zhou!
```

As we seen here, the parameters are sepearated from name of the subcode by either a comma or simply spaces. The comma and spaces will be stripped in passing the parameter. Every thing except comma or trailing spaces are included in the parameter value. This is much more flexible than function parameter passing in a typical programming language and it is even much more flexible than typical macro systems. 

What if sometime we *really* need to pass parameters with comma in it? First of all, if the parameter is delimited by quote or bracket, MyDef is intelligent enough to recognize it, you can direct use it:
```
subcode: A(name)
    Hello, $(name)

page: t
    $call A, "Zhou, Hui"
    $call A, 'Zhou,', Hui
    $call A, (Zhou, Hui)
```
This will be compile into:
```
Hello, "Zhou, Hui"
Hello, 'Zhou,' Hui
Hello, (Zhou, Hui)
```

However, if you try:
```
subcode: A(name)
    Hello, $(name)

page: t
    $call A, Zhou, Hui

```
```
$ make
mydef_page t.def out/t.txt
PAGE: t
    [t.def:6] Code A parameter mismatch (0 + 2) != 1. [pline:Zhou, Hui]
  --> [out/t.txt]

$ cat out/t.txt
Hello, Zhou
```
MyDef complains and omits the second parameter. So what if you *really* want to pass in unquoted comma as a single parameter? One solution is to tell MyDef that "subcode: A" *only* accepts single parameter:
```
subcode: A(@name)
    Hello, $(name)

page: t
    $call A, Zhou, Hui
```
```
$ make
mydef_page t.def out/t.txt
PAGE: t
  --> [out/t.txt]
$ cat out/t.txt
Hello, Zhou, Hui
```
As you wished and no complaints.  In fact, you can force any last parameter to be *single*:
```
subcode: A(greeting, @name)
    $(greeting), $(name)

page: t
    $call A, Hello, Zhou, Hui
```
It works, but to some it is getting uncomfortable. MyDef uses '@' in some other places as well to force a meaning, as well as supress complaining. We admit it is a bit ugly, but hopefully you will not use it often.

When the parameter is being used inside the subcode definition, it is also taken quite literally without any restrictions:
```
subcode: print(v)
    print "variable \$$(v) = $$(v)\n"

subcode: print_v(v)
    print "variable \$(v) = $(v)\n"

subcode: call_print(subname, v)
    $call $(subname), $(v)

page: t
    $call print, v
    $call print_v, $v
    $call call_print, print_v, $v
```
This will compile into:
```
print "variable \$v = $v\n"
print "variable \$v = $v\n"
print "variable \$v = $v\n"
```

It even works with nested macros:
```
macros:
    name1: Alice
    name2: Bob

subcode: A(idx)
    Hello, $(name$(idx))

page: t
    $call A, 1
    $call A, 2
```
And the compiled output:
```
Hello, Alice
Hello, Bob
```

