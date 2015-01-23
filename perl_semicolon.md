Semicolons are optional
------------------------

Semicolons have historic reasons, but semantic reason is not one of them. The need to remember to add semicolon is disruptive to the flow of programmer's mind. So as an important goal, we want to make the semicolons optional. This is easily achievable if we stick to one statement inone line -- which is also a semantic structure requirement. 

The current rule that MyDef determines whether to add semicolon is relatively simple: unless the line ends with ',', ':', '(', '[', '{', or ';', MyDef will add the semicolons. In Perl, when

    if ($line !~ /[,:\(\[\{;]\s*$/) { $line .= ';' }

There are a couple special cases:

    if (cond) 
    { statement ; }

will be compiled into:
```
if (cond)      
{ statement ; }; 
```
No semicolon is added after "if (cond)". Neither in the cases of "while ()" or "for ()". However, the semicolon being added after "{ statement ; }" is extra but harmless. 

Most of the time, MyDef does not try to understand the language. Most often, it only parses the text line by line. It does not try to be accurate, it merely follows typical convention. So far, we'll find MyDef's semicolon convention to be sufficient. After all, even the case of "if (cond)" on its own line is not that conventionally common.

