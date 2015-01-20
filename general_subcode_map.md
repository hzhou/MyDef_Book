Map Call
--------

Let's investigate following example:

```
subcode: A(name)
    Hello, $(name)

page: t
    $call A, Alice
    $call A, Bob
    ...
    $call A, Zack

```

Some one may think above code looks fine. However, when you are actually writing the program, you may start to feel a bit repetitive. Semantically, there are two nodes of information -- call subcode A, call it with a list of names. But when we are forced to type them out, it suddenly become 26 nodes of information, and it is semantically disruptive. Then later in the code we want to do things like:

```
...

subcode: B(name)
    Bye, $(name)


page: t
    $call A, Alice
    ...

    $call B, Alice
    $call B, Bob
    ...
    $call B, Zack
```
Now we are dupicating the code! The problem just become unbearable. Fortunately, MyDef offers a "map" call syntax:

```
page: t
    $map A, Alice, Bob, ..., Zack
    ...
    something complex
    ...
    $map B, Alice, Bob, ..., Zack
```
'$map' applies '$call' over a list, passing each item as parameter in turn. But still we are duplicating the list. Let's keep refactoring:

```
macros:
    name_list: Alice, Bob, ..., Zack

page: t
    $map A, $(name_list)
    ...
    $map B, $(name_list)
```

Now it is finally consistent with our perceived semantic structure. What you see is what you think (WYSIWYT)!

Map call applies easily to subcodes with a single parameter. Can we do that to subcodes with multiple parameters? It is not that simple. Let's look at the following example:
```
subcode: A(greeting, name)
    $(greeting), $(name)!

page: t
    $call A, Hello, Mike
    $call A, Bonjour, Sophie
    $call A, NiHao, Hui
    ... 
```
Do we still perceive above list of calls as two nodes of information? Probably not true any more. Not only in this case it is not trivial to design a map call syntax, but also it is not worth it. However, sometime we do perceive simplifications:

```
page: t
    $call A, Hello, Mike
    $call A, Hello, Sophie
    $call A, Hello, Hui
    ... 
```
In this case, MyDef offers:
```
page: t
    $map A(Hello), Mike, Sophie, Hui, ...
```
"$map" can treat a multi-parameter subcode as a single-parameter one if we supplie the common parameter values (all except last parameter) after the name of the subcode within a bracket. Still, whether this is useful is upto personal taste. I would still prefer refactoring into following:
```
subcode: A(greeting, name)
    $(greeting), $(name)!

subcode: A_hello(name)
    $call A, Hello, $(name)

page: t
    $map A_hello, Hello, Mike
```
It all depends the habit of one's mind.


