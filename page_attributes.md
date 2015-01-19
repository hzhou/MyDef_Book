# Page attributes

When we compile a def page into an output file,
MyDef always appends an extension defaulted by the corresponding module types. For module general, it is .txt:

```
#-- t.def --
page: t
    Some text
```
```
mydef_page.pl -mgeneral t.def
PAGE: t
  --> [t.txt]
```

What if we want a different extension or without extension? This can be accomplished by given the page a type attribute.

    page: t1
        type: TXT
        
        This file t1.TXT.
        
    page: t2
        type:
        
        This file t2 (without extension).

```
mydef_page.pl -mgeneral t.def
PAGE: t1
  --> [t1.TXT]
PAGE: t2
  --> [t2]
```
        