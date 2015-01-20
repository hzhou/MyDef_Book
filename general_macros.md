# Macros

We already encoutered macros specified as page attributes. How do we reference them? We use the construct "$(macro)".

```
page: t
    name: Hui

    Welcome to $(name)'s page.
```

Because MyDef works all kinds of text files and prgramming languages, the $(macro) construct is deliberately chosen to avoid conflicts with the underlying language syntax. 

In addition to page attribute macros, we can define macros outside the page blocks using a macros block.

```
macros:
    name: Hui

page: t
    Welcome to $(name)'s page.
```

Macros can be nested:
```
macros:
    first_name: Hui
    last_name:  Zhou
    name: $(first_name) $(last_name)

page: t
    Hello, my name is $(name).
```

This will compile into:
```
Hello, my name is Hui  Zhou.
```

Macros can be defined in multiple sections.
```
macros:
    first_name: Hui

macros:
    last_name:  Zhou

page: t
    Hello, my name is $(name).

macros:
    name: $(first_name) $(last_name)
```

If we define multiple macros with the same name, the later definitions will overwrite the previous ones. Such cases are often confusing and we generally should try to avoid. However, sometime we setup default macros in library, and we may intentionally want this behavior to overwrite the default values later.

It is dangerous to try
```
macros:
    name: $(name)
    
page: t
    Hello, my name is $(name).
```

MyDef will crash (I am kidding, simply interrupt MyDef and correct your error and continue). Either MyDef or the programer who need such recursive macros is too stupid. However, if you call a subcode recursively:
```
subcode: A
    $call A

page: t
    $call A
```
MyDef will remind you it is recursive:
```
$ mydef_page -mgeneral t.def
PAGE: t
Recursive subcode: A [1]
```
Remember, MyDef subcodes are block macros. Recursive macros are often silly. However, there are legitimate cases when we need to recurse into certain subcode a couple times, and there is a way to get around that, which we will discuss in a later section.

