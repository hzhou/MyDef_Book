# Perl XS

```
# TEST: test_xs::test("Hui Zhou");
# TEST: my @a; for(my $i=2;$i<10;$i++){push @a, $i*$i;} test_xs::test_array(\@a);

page: test_xs, basic_frame
    subcode: xs_main
	$list test, test_array

fncode: test(s_name)
    $global sv_global
    sv_global=newSV(0)
    sv_setiv(sv_global, 100)
    printf("Hello, %s! %d wishes\n", s_name, SvIV(sv_global))

fncode: test_array(av_list)
    $foreach tn in av_list
	printf("  %4d ", tn)
    printf("\n")

```

Build Makefile:
```
$ mydef_make
Please enter the path to compile into [out]:
test_xs
Please enter module type [perl]:
xs
    output_dir: test_xs
Running h2xs -n test_xs... ...
Defaulting to backwards compatibility with perl 5.14.2
If you intend this module to be compatible with earlier perl versions, please
specify a minimum perl version with the -b option.

Writing test_xs/ppport.h
Writing test_xs/lib/test_xs.pm
Writing test_xs/test_xs.xs
Writing test_xs/fallback/const-c.inc
Writing test_xs/fallback/const-xs.inc
Writing test_xs/Makefile.PL
Writing test_xs/README
Writing test_xs/t/test_xs.t
Writing test_xs/Changes
Writing test_xs/MANIFEST

```

And make:
```
$ make
mydef_page xs_test.def test_xs/test_xs.xs
PAGE: test_xs
  --> [test_xs/test_xs.xs]

```
Perl has its own make process:
```
$ perl Makefile.PL
Checking if your kit is complete...
Looks good
Writing Makefile for test_xs
Writing MYMETA.yml

$ make
cp lib/test_xs.pm blib/lib/test_xs.pm
AutoSplitting blib/lib/test_xs.pm (blib/lib/auto/test_xs)
/usr/bin/perl /usr/share/perl/5.14/ExtUtils/xsubpp  -typemap /usr/share/perl/5.14/ExtUtils/typemap  test_xs.xs > test_xs.xsc && mv test_xs.xsc test_xs.c
Please specify prototyping behavior for test_xs.xs (see perlxs manual)
cc -c  -I. -D_REENTRANT -D_GNU_SOURCE -DDEBIAN -fstack-protector -fno-strict-aliasing -pipe -I/usr/local/include -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -O2 -g   -DVERSION=\"0.01\" -DXS_VERSION=\"0.01\" -fPIC "-I/usr/lib/perl/5.14/CORE"   test_xs.c
Running Mkbootstrap for test_xs ()
chmod 644 test_xs.bs
rm -f blib/arch/auto/test_xs/test_xs.so
cc  -shared -L/usr/local/lib -fstack-protector test_xs.o  -o blib/arch/auto/test_xs/test_xs.so  \
                \

chmod 755 blib/arch/auto/test_xs/test_xs.so
cp test_xs.bs blib/arch/auto/test_xs/test_xs.bs
chmod 644 blib/arch/auto/test_xs/test_xs.bs
Manifying blib/man3/test_xs.3pm

```

To have a quick test:
```
$ perl -I./blib/lib -I./blib/arch/auto/test_xs/ -e'use test_xs; test_xs::test("Hui Zhou");'
Hello, Hui Zhou! 100 wishes

```
