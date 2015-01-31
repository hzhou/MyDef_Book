## It Is Easier Than You Think

Assume you just skimed through the gallery examples, what do you think?

Of course my purpose is to impress you that MyDef can achieve amazing things. However, in reality you may be more scared than be impressed. You may think: it seems a lot of features, it is overwhelming, and do I really want to learn?

In reality it is much easier to start MyDef than you think! Let me illustrate.

#### Writing code in MyDef

You can start writing in MyDef before you learn any tricks. It can not be easier.

Let's say you are going to program in perl and make a script called t.pl. As long as you know how to write plain perl script, you can start as writing as you always do, except now under the MyDef thin layer:

```
page: t
    type: pl

    #!/usr/bin/perl
    use strict;
    my @list;
    open In, "directory.txt" or die "Can't open directory.txt.\n";
    while(<In>){
        if(/\bZhou\b/){
            push @list, $_;
        }
    }
    close In;
    foreach my $l (@list){
        if($l=~/^(.* Zhou)\s*(.*)/){
            print "$1: $2\n";
        }
    }
```

That will work! So for the minimum, you only need to learn the page directive (setting the output file) and the type attribute (setting the file extension) and you already know how to write any programs in MyDef (as long as you know how to do it in your language).

Next, as you program your usual way, you must have certain thoughts, such as "why do I have to manuaaly type in the #! line?", "why do I have to worry about the semicolons?", "How I wish I don't have to use curly braces!", etc. But now you are having the MyDef layer, you just need remember: for every complain, there is something you can do! So you search the book or simply ask, and you may learn that you can simply omit the "#!" line, as well as "use strict" already with MyDef's Perl output. "Oh, that is nice", so from now on, you just omit them. Congratulations!, you already reaped a benefit from adopting MyDef.

As you keep writing programs, and as you keep getting new complaining thought, you keep learning new tricks and building up your own conventions, and you find yourself more and more efficient. 

Above all, you don't need worry that nobody else in your work uses MyDef. you simply share your MyDef output files with them. To them, you are still writing plain perl. Maybe sometime they may wonder how you get so productive, by that time, proudly introduce MyDef to them!

#### Reading code in MyDef

Ok, you say, it is easy to write code but how is anyone supposed to read my code in MyDef when they have no idea of tricks and conventions I have used?

Well, for the bottomline, simply run through MyDef and read the output. Assuming you are well capable of reading Perl code, then simply run the above example through MyDef and read the perl code. At the bottomline, you don't need any knowledge of MyDef to read code in MyDef! 

Ok, but since the code is in MyDef, why don't we try read it before fall back to the bottom line:

```
page: t
    my @list
    &call open_r, directory.txt
        $if /\bZhou\b/
            push @list, $_

    $foreach $l in @list
        $if $l=~/^(.* Zhou)\s*(.*)/
            $print "$1: $2\n"
```

With basic English, could you get the hint that it is opening "directory.txt", push into a list of lines with word "Zhou", and then prints the list back? If you did, then you would understand why I claim MyDef can enhance the readablity of the code.

But you will wonder how this seemingly intuitive but unfamiliar code can be equivallent to the code you used to. Unlike a typical programming language which its mechanics are often illusive, MyDef are simply macros and conventions. For example, you wander how open_r works. For choice one, you could compare the perl code side by side to the MyDef code -- just like you used put a text with its translation side by side and you can try draw parallells and deduce what is going on. Or, if you learned where MyDef find its library, you could simply search "open_r" in the library -- which is std_perl.def and it says:

```
subcode: open_r(name)
    $(if:name~")
	$(set:name=$(name:strip))
    open In, "$(name)" or die "Can't open $(name).\n"
    $while <In>
        BLOCK
    close In
```

It is straight Perl! Now you learned the trick!. Now if you are smart, you also learned how to go on and make your own magick!




