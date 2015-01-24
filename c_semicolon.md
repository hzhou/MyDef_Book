Semicolons
----------

[For this and next section, some readers may find a lot resembalance in text to those corresponding sections under Perl. As I do not assume our C programers necessarily knows Perl and even reads that chapter, many points are intentionally repeated here.]

Semicolons have historic reasons, but semantic reason is not one of them. The need to remember to add semicolon is disruptive to the flow of programmer's mind. So as an important goal, we want to make the semicolons optional. This is easily achievable if we stick to one statement in one line -- which is also a semantic structure requirement. 

The current rule that MyDef determines whether to add semicolon is relatively simple: *unless* the line ends with ',', ':', ';', or '(', '[', '{', MyDef will add the semicolons. 

So in MyDef, you can write C statements without worrying about semicolons. They are optional. You can still use them, but I tend to omit them because I find it cleaner. MyDef do not garantee it will always get the semicolons right, but it would be very rare as long as you do not intentionally write obfuscated code. But if you do and MyDef mistakes, surely the C compiler will catch it. It will be merely a quick feedback.

There are a couple special cases:
```
if (cond) 
{ statement ; }
```
It compiles into:
```
if (cond)      
{ statement ; }; 
```
No semicolon is added after "if (cond)". Neither in the cases of "while ()" or "for ()". However, the semicolon being added after "{ statement ; }" is extra but harmless. 

It will also leave C preprocessor lines, which starts with '#if', '#else', #endif', '#include', '#define'. However, they visually interferes with MyDef comments (even though MyDef do recognize them), and I would recomend to avoid them in MyDef. MyDef provides other mechanism to achive the purpose preprocessor that we will discuss later in this chapter.

