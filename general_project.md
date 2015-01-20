# MyDef Project

So far, we only discussed single def file. However, most of the time, we will be working with multiple def files and multiple output files. We would also need library files and a mechanism to include libraries.

MyDef libraries are simply def files filled with subcodes and macros. To use the library, simply include the def files. For example:

```
#---- A.def ----
subcode: A
    Line A1
    Line A2
```
```
#---- t.def ----
include: A.def

page: t
    $call A
```

To manage a project of def files, we use "make". MyDef provides a utility program to help generate the Makefile -- mydef_make. Let's say in the current directory, you have just created a single def file:

```
page: t
    line 1
```

Now run mydef_make:
```
$ mydef_make
Please enter the path to compile into [out]:

Please enter module type [perl]:
general
    output_dir: out
$ ls
config  Makefile  out  t.def
```

There are two prompts, for each we can simply press <enter> to accept the default or we can type in the values. We typed in "general" for the module type. Upon completion, mydef_make creates a Makefile, as well as a config file and the output directory -- out.

Let's now try "make":
```
$ make
mydef_page t.def out/t.txt
PAGE: t
  --> [out/t.txt]
```

Once we generated the Makefile, for the on-going development, we simply need edit the def file, save, and run "make" again.

Let's create another def file t2.def
```
#---- t2.def ---
include: A.def
page: t2
    $call A
```
For every new def file, we have to re-run mydef_make to update the Makefile:
```
$ mydef_make
    output_dir: out
Can't open A.def
```
We notice two things. First, mydef_make no longer prompt us for questions. It reads in config file for those answers. Second, mydef_make complains about A.def because we included it but MyDef cannot find it.

Let's crate A.def:
```
#---- A.def ----
subcode: A
    line A1
    line A2
```
```
$ mydef_make
    output_dir: out
$ make
mydef_page t2.def out/t2.txt
PAGE: t2
  --> [out/t2.txt]
```
Good, no more complaints. And "make" compiles t2.def as we wished. Let's check the dependency:
```
$ touch A.def
$ make
mydef_page t2.def out/t2.txt
PAGE: t2
  --> [out/t2.txt]
```
Very nice indeed. 

Now the current directory looks like this:
```
$ ls
A.def  config  Makefile  out  t2.def  t.def
```
As a good neat programer, we want to seperate the library def files from page producing def files. We'll create a subdirectory macro_library and move A.def there. But then MyDef won't find A.def again. To remedy that, we can either add the path to the "include:" line:

    #---- t2.def ----
    include: macros_library/A.def

    page: t2
	$call A

Or we could tell MyDef where to look for libraries. To do that, we need to edit the config file. The current config file only has two lines: 
```
output_dir: out
module: general
```

Add we need to add a third line:
```
include_path: macros_library
```
Now change the include line in t2.def back to 
```
include: A.def
```
and run mydef_make
```
$ mydef_make
    output_dir: out
    Skip folder macros_library
```
mydef_make by default will not only check all def files in the current folder, but also will look into every subfolders. However, it takes the convention that a library folder starts with "macros_" and it will skip creating Makefiles for such folders. 

To test this feature, lets create another directory, subproject, and move t2.def there.
```
$ mkdir subproject
$ mv t2.def subproject/
$ mydef_make
    output_dir: out
    Skip folder macros_library
/home/hzhou/bin/mydef_make subproject ...
    output_dir: out
$ touch macros_library/A.def
$ make
true
cd subproject; make
make[1]: Entering directory `/media/whiteplug/projects/test/perl1/subproject'
mydef_page t2.def out/t2.txt
PAGE: t2
  --> [out/t2.txt]
make[1]: Leaving directory `/media/whiteplug/projects/test/perl1/subproject'
$ 
```

Working nicely.

Last, if you want to exclude certain subdirectory from the Makefile, simply add an empty file, skipmake. 

```
$ touch subproject/skipmake
$ mydef_make
    output_dir: out
    Skip folder macros_library
    Skip folder subproject
hzhou@aqua perl1$ touch macros_library/A.def
hzhou@aqua perl1$ make
make: Nothing to be done for `all_targets'.
$
```
subproject has been excluded.
