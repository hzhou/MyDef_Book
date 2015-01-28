std_perl.def
------------

When we tell MyDef to use a specific module, it will automatically load a standard library file that comes with standard MyDef installation. For Perl, this std_perl.def in the folder of $MYDEFLIB, typically ~/lib/MyDef/. In this file, we include some of the subcodes that we find universally useful. 

Instead of listing the entire file here, you can open std_perl yourself, and read the code there. It is relatively short and straight forward to understand. In particular, the following two subcodes are most frequently used:

```
subcode: open_r(name)
    $(if:name~")
        $(set:name=$(name:strip))
    open In, "$(name)" or die "Can't open $(name).\n"
    $while <In>
        BLOCK
    close In

subcode: open_w(name)
    $(if:name~")
        $(set:name=$(name:strip))
    open Out, ">$(name)" or die "Can't write $(name).\n"
    BLOCK
    close Out
```

We can either pass either a variable or a literal string or a combination of them. 

As more users pitch in, this standard library certainly will get expanded or pruned. If you have certain code that you find it universally useful, contact MyDef author and have it included in std_perl.def.
