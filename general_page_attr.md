# Page attributes

When we compile a def page into an output file,
MyDef always appends an extension defaulted by the corresponding module types. For module general, it is .txt:

```
#-- t.def --
page: t
    Some text
```
```
mydef_page -mgeneral t.def
PAGE: t
  --> [./t.txt]
```

What if we want a different extension or without extension? This can be accomplished by given the page a type attribute.

    page: t
        This file has default extension.
        
    page: t1
        type: TXT
        
        This file t1.TXT.
        
    page: t2
        type:
        
        This file t2 (without extension).

```
mydef_page -mgeneral t.def
PAGE: t
  --> [./t.txt]
PAGE: t1
  --> [./t1.TXT]
PAGE: t2
  --> [./t2]
```
        
In a typical case though, the output module's default is sufficient and we rarely need to specify the type attribute.

Next we can specify the output directory. The default is ".", the current working directory.

We often want to separate def files and its output code files. In a typical project, we often use an "out" subdirectory for the output.

```
page: t
    output_dir: out
    
    This file will be in ./out directory.
```

```
$ mydef_page -mgeneral t.def
PAGE: t
  --> [./out/t.txt]
```

But sometime, we may want to output directly to an existing source tree. For example, when we developing web sites, we may want to directly output to the configured site folder.

We also can specify the output module using page attributes.
```
page: t
    module: general

    This file will be compiled using the general module.
```
```
$ mydef_page t.def
PAGE: t
  --> [./t.txt]
```

Beside type, output_dir, and module, we also can specify arbitrary amount of additional attributes. For example:

```
page: t
    name: arbitrary string values
    string: "quote will be included verbosely"
    ...
    
    All attributes start with "word: ", so MyDef will recognize where the real content starts.
```    

Some of these attributes may have specific meaning in specific output module. For example, the C module recognizes "use_double: 1", and will change the default floating type to "double" instead of "float". However, most of the time we specify page attributes simply to be used as macros.    
    
