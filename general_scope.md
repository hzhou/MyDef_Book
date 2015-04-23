# Scopes

In MyDef, all macros live in a series of nested scopes. On the very top is the global scope. Macros that are defined under "macros:" block lives in global scope. Next is the page scope. Page attributes live in page scope. Followed is the chain of scopes defined by each subcode starting at "subcode: main". 

We can also dynamically create macros with the scope. 

```
subcode: A
    $(set:name=Hui)
    Hello, $(name)!
```
The set macros are effective throughout the remainder of the subcode as well as all the subcodes being called with in.

```
subcode: A
    $(set:name=Hui)
    Hello, $(name)!
    $call B

subcode: B
    Bye, $(name)!

```

In addition to explicit subcode scope, preprocessing directive "$(for:...)" will also create new scope implicitly. However, "$(if:...)" does not create new scopes. So:

```
subcode: A(name)
    $(if:name=Hui)
        $(set:greeting=Hi)
    $(else)
        $(set:greeting=Hello)
    $(greeting), $(name)!

page: t
    $call A, Hui
```

will output "Hi, Hui!". However,

```
page: t
    $(for:i in 1-3)
        $(set:name=Hui_$(i))
        Set $(name).
    $(if:name)
        Hi, $(name)!
    $(else)
        No name here!

```
will compile to
```
Set Hui_1.
Set Hui_2.
Set Hui_3.
No name here!
```

An interesting question is: what scope should the block under "&call" be?

```
subcode: A
    $(set:name=A)
    BLOCK
    in subcode A: name = $(name)

page: t                      
    &call A
        in BLOCK: name=$(name)
        $(set:name=page t)
```
And if we compile, we get:
```
in BLOCK: name=A
in subcode A: name = page t
```
Apparently, the block under "&call" is the same as the subcode itself. 

In addition to "$(set:...)" to create or reset macros at current scope, if we want to set/reset macros at one scope higher, we could use "$(export:...)":

```
subcode: A
    $(export:name=A)

page: t                      
    $call A
    Hello, $(name)!
```
This will compile into:
```
Hello, A!
```

if we want export a macro in the current scope to a higher scope, we can simply use "$(export:name)".

```
subcode: A
    $(set:name=A)
    $(export:name)

page: t                      
    $call A
    Hello, $(name)!
```

If we want to directly set macros at global scope, we could use "$(setmacro:...)".

```
subcode: AA
    $(setmacro:name=A)

subcode: A
    $call AA

page: t
    $call A
    Hello, $(name)!
```


