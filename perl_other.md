Other
-----

$global
-------

Here is another coflicting interests. On one hand, we would like to declare global variables where we use them, which is most convinient when writing the code and convienient when trying to understand the code. But at another context, we want to group all global variables together, so we can spot any conflicts or extras that we should resolve or spare. 

To help, MyDef let you define global variables on the go, and they will be collected and compiled to the top of the output file with "our" declarer. 

```
page: t
    $for $i=0:10
        $global $sum, $sum2, $count=10
        $sum+=$i
```

And the output:
```
#!/usr/bin/perl
use strict;
our $sum;
our $sum2;
our $count=10;
for(my $i=0; $i <10; $i++){
    $sum+=$i;
}
```

It allows you to put multiple variables declaration on the same line, which is a bonus.

$my
----

This is exactly the same as "my", it is there as a symmetry to $global.

```
page: t
    $my $a, $b, $c
```
Output:
```
#!/usr/bin/perl
use strict;
my $a;
my $b;
my $c;

```

Subroutine
------

You can directly write perl sub routines just like you write any other perl statements. If you would like to get rid of the brackets, you can you $sub:

```
$sub A($a, $b)
    BLOCK
```
It compiles into:
```
sub A{
    my ($a, $b)=@_;
    block;
}
```
In the spirit of flexibility, Perl subroutines do not really use formal parameters. However, semantically parameters are always part of a function structure. MyDef's $sub provides a way for you to specify the formal prameters.

However, if you wouldn't mind where the sub definition occurs in the perl source code, you can use a special kind of subcode -- fncode:
```
page: t
    $global $a=10

    test($a)

fncode: test($a)
    $global $b=1

    print " $a + $b = ", $a+$b, "\n"
```
It compiles into:
```
#!/usr/bin/perl
use strict;
our $a=10;
our $b=1;
sub test {
    my ($a) = @_;
    print " $a + $b = ", $a+$b, "\n";
}

test($a);
```

All the fncode will be collected and turned into Perl subs at the beginning of output code, after the global variables.


Package
--------

If you are writing a *Perl* library, you would want a ".pm" extension, which you can use the page attribute "type":

```
page: t
    type: pm
    
    $call @package_subs

    1;

subcode:: package_subs
    ...
```

That certainly will work. However, most likely, you will have a "package" declaration. You can put the package declaration as a page attribute; and when you do that, MyDef recognizes that you are creating a perl module, and will automatically set file extension to ".pm".

```
page: t
    package: T
    $sub A($a, $b)
        block

    1
```

```
$ mydef_page -mperl t.def
PAGE: t
  --> [./t.pm]
```

```
use strict;
package T;
sub A {
    my ($a, $b)=@_;
    block;
}
1;
```

We notice that the '#!' line is being omited.
