# Perl

Because MyDef uses indentation as basic structure unit, one may think it is heavily influenced by Python. However, the biggest influencer is Perl. In fact, MyDef itself is written in Perl (through MyDef of course).

Perl is flexible and direct. Quote from the book "Programming Perl": "One thing that's easy about Perl is that you don't have to say much before you say what you want to say." The price for that is Perl often being perceived as one ugly language.

Let's see what MyDef can do. Consider following program:
```
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

In MyDef:

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

No need to write the '#!' line or remember to use strict; no need to remember the semicolons; no need to balance the brackets or remember to close the file. It is not much, but consider these are one-off quick scripts that one wants to write in a jiffy, MyDef can save you a lot of time.
            