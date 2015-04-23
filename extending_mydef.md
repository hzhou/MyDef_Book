# Extending MyDef
 
If we view the entire MyDef as a meta-layer that simply transforms the text you write into text the compiler will accept, then you may refer to every feature in MyDef as "macro". In MyDef, there are three layers of macros we can write.

The first layer macros are what we have learned so far. They are more inline with the concept of conventional macros, although MyDef greatly expands features such as scope management to facilitate writing semantic macros.

However, sometime we want to do some macro transformation that does not fit into any common pattern, where it is not practical to implement with the first-layer facility. Nevertheless, the logic is still simple, and often we would simply wish a turing complete macro facility so we can *program* our intended macro transformation. This type of macros are inline with LISP-style macros, where macros are rather programs. In MyDef, we refer to this type of macros as second layer macro. Rather than implement a new language for this purpose (such as lisp), we simply opened the internal and let user directly write programs directly in its internal language. In MyDef's case, this is Perl. You do not need to learn a new langauge. Anything you know how to do in Perl, you can write it.

For the last layer, it is truly the MyDef internal. Here I am referring to MyDef's output modules. Such as output_c, where you can implement variable tracking, function management, type inferrence, or even gabage collection. At this last level, there is no limitation. We are literally extending the language, not just MyDef, but the language you are actually writing -- C, Perl, or whatever.

In this chapter, we'll show some examples of second layer macros and introduce how to write an output module. Both the second and third layers are actual programming. We show you the door, then what you can do is only limited by your ability and imagination.

