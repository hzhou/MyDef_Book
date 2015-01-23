Other
-----

* $global

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

* $my

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

* Subroutine

$sub is also a way to avoid brackets, as well as let you conviniently specify parameters. 

```
$sub A($a, $b)
    BLOCK
```
It will be translated into:
```
sub A{
    my ($a, $b)=@_;
    block;
}
```

Here is a typical pattern:
```
page: t
    $call @support_subs

    A()
    B()

subcode:: support_subs
    $sub A
        block

subcode:: support_subs
    $sub B
        block
```

The pattern avoids find name for a subcode that defines a "sub" and then have to "$call" it to actually include in the output. 

If we are developing MyDef library, consider this pattern:

```
#---- file a.def ---
subcode: _autoload
    $sub A
        block

#---- file t.def ---
include: a.def

page: t
    A()
```

Autoload automatically put the content at the top of the output file, which is convinient to automatically pull those sub definitions in.

* Package

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
