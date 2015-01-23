Use strict
----------

Let's first try an example:

    page: t
        print "Hello world!";

Compile with mydef_page:

    $ mydef_page -mperl t.def
    PAGE: t
      --> [t.pl]

Let's check "t.pl":

    #!/usr/bin/perl  
    use strict;
    print "Hello world!";

First, the Perl module adds a default ".pl" extension. Next, it adds "#!/usr/bin/perl" at the first line, so the script is ready to run. If you are not working in a unix-like environment or you always run in a fashion of "perl script.pl", the '#!' line will be silently ignored any way.

Next, it adds "use strict;".

The development of programming language has been a struggle between machine accuracy and human convienience. We desire both, but both often cannot be achieved. The balance is always on the machine accuracy side. It is because the bottom line is accuracy. It is also because human is much more capable of bending than machines. Human can bend, but not without complaining. So slowly, the development in programming language start to cater toward human convienience. Unfortunately, human efficiency and accuracy are fundamentally in conflict. Examine how ourselves think, we achieve efficiency by narrowing context, omitting details and live with ambiguity. Ambiguity is ok for us, because we have conventions. We only fix our ambiguity when conventions are in question. But conventions are informal and cannot be put into an accurate programming language which speaks to machines. So as the language caters to human convience more and more, we start to sacrifice accuracy. And very often, these sacrifices are so subtle that we do not realize the mistakes until much later when it become critical. That is bad, and it is the origin of "use strict" in Perl. When "use strict", Perl will do a much better job catching mistakes, but the programmers have to bend more, losing some efficiency. 

Since the accuracy is the bottom line, we should always "use strict". Then Perl become less easy to use.

Perl's dilemma roots from trying to mix two contexts. The way we work is to have rough but efficient thinking, then have a feedback system to signal whether our thinking is good or need correction. We seperate efficiency context and accuracy context, and rapidly switching back and forth, that is how we achive both. But mixing the two context into one context does not really work.

That is essentially the reason of MyDef. MyDef will focus on semantic structure, omiting many details, so it can do a better job at efficiency. Meanwhile, we let lower level programming language focus on getting it right, and give us feedback when there is problem, even a potential one. The programmer will switch between the two layer back and forth. 

So as an example, MyDef let's programmer simply write what he want to do, then it adds what we implied -- following the convention -- and pass to the lower level engine for feedback. Initially, we may find MyDef make a lot of mistakes, but no problem because the strict Perl will alert us. Then the developers will think about what we implied and update MyDef with the more correct convention -- isn't that how our own mind develop?


