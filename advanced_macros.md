# More on setting macros

Setting macros
--------------

When we use $(set:name=...), the value of the macro extends all the way before last closing parenthesis.  This is convinient as we can set almost arbitrary values without any special handling, albeit maybe ugly.

```
page: t
    $(set: name=Long line with "quotes" and (parentheses) and even unbalanced ones }))
    $(name)
```
Output:
```
Long line with "quotes" and (parentheses) and even unbalanced ones })
```

In addition, both the name part and value part may contain macros, even nested ones.

```
macros:
    A1: macro A1
    A2: macro A2

page: t
    $(set:name=A)
    $(set:$(name)_1=set $($(name)1).)
    $(set:$(name)_2=set $($(name)2).)
    $(A_1)
    $(A_2)

```
Output:
```
set macro A1.
set macro A2.
```

$(export:...) and $(setmacro:...) works the same way. 

Spliting macros
---------------

$(split:name) splits macro $(name) into $(p_1), $(p_2), ...

```
macros:
    a_list: a, b, c, d, e, f

page: t
    $(split:a_list)
    a_list[6]: $(p_6)
```
Output:
```
a_list[6]: f
```

Unset
-----

$(unset:name) will delete all macros with "name" at *all* scopes. Use carefully.

```
macros:
    name: Hui

page: t
    $(if:name)
        Hello, $(name)
    $(unset:name)

    $(if:name)
        Still here!
    $(else)
        Oh, you are no longer here!
```
Output:
```
Hello, Hui                 
Oh, you are no longer here!
```

Autoinc
-------

$(autoinc:name) will set a macro at page scope starting with value 1. Each time this statement is encoutered, it increase the value by 1.

```
subcode: A
    $(autoinc:index)
    var A_$(index);

page: t
    $(for:i in 1..10)
        $call A
```
Output:
```
var A_1;  
var A_2;  
var A_3;  
var A_4;  
var A_5;  
var A_6;  
var A_7;  
var A_8;  
var A_9;  
var A_10; 
```
