Subcode basic_frame
-------------------

A C program consists of global declarations and functions. The actual code has to be written inside a function starting at the main function. That is all well, but become hassle when all we want to do is quick programs, like "hello world" or print the all the prime numbers under 1000. 

In std_c.def, which is installed under $MYDEFLIB/ and is always included when we use MyDef's C output module, has a simple subcode: basic_frame:

```
subcode: basic_frame
    $include stdio
    $include stdlib
    $call @global
    $(if:hascode:n_main)
	$list n_main
    $(if:hascode:main2)
	$list main2

```

This basic_frame subcode allows us to write quick program in C. Let's print the prime numbers:

```
page: t, basic_frame
    $global pf_prime[1000], n_prime=0

    pf_prime[n_prime++]=2
    $for i=3:1000
        b_is_prime=1
        $for j=0:n_prime
            $if i % pf_prime[j]==0
                b_is_prime=0
                break
        $if b_is_prime
            pf_prime[n_prime++]=i

    $for i=0:n_prime
        $print $pf_prime[i]
```

```
$make all
PAGE: t
  --> [out/t.c]
make -C out
make[1]: Entering directory `/media/whiteplug/projects/test/C/out'
gcc -c   -o t.o t.c
gcc   -o t t.o
make[1]: Leaving directory `/media/whiteplug/projects/test/C/out'

$ out/t
2
3
5
...
977
983
991
997
```

Don't worry about the syntax, we'll intruduce them next.
