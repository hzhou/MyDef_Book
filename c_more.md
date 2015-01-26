## More

There are more features MyDef can offer. However, they are more specialized and should be considered experimental. I would include some examples here simply to expand our vision on what is possible.

The following code are directly from tests/ folder in MyDef repository. 

* Regular expression
```
include: c/regex.def

page: test, basic_frame
    $local string s
    s.$resize(200)
    &call open_r, "spam.mbox"
        $while fgets(s, 200, file_in)
            s_len=strlen(s)
            $if s=~/^From (\S+)\s+(\w+)\s+(\w+)\s+(\d+)\s+(\d+:\d+:\d+)\s+(\d+)/
                $regex_capture ts_addr, ts_day, ts_mon, ts_date, ts_time, ts_year
                printf("Envelop-From: [%s %s %s] %s\n", ts_mon, ts_date, ts_time, ts_addr)
            $elif s=~/^To:.*<(\S+)>/
                $regex_capture ts_to=>1
                printf("\tTo: %s\n", ts_to)

```

* Matrix
```
include: c/matrix.def
macros:
    float: float

page: test, basic_frame
    $call test_matrix_mult

subcode: test_matrix_mult
    $local $(float) A[4]={2, 3, 4, 0}
    $local $(float) B[4]={1, 2, 5, -1}
    $call set_matrix, A, 2, 2
    $call set_matrix, B, 2, 2

    $sumcode sum=A[i]*B[i]
    $print "product: $sum"

    $local $(float) C[4]
    $call set_matrix, C, 2, 2

    $sumcode C[i,j]=A[i,k]*B[k,j]
    $call matrix_print, C

```

* Hash
```
include: c/hash.def

page: test, basic_frame
    $call test_hash

subcode: test_hash
    $my hash H
    
    $(for:i in 1..500)
	H{a$(i)}=$(i)
    H{34}=43
    H{"68"}=86
    H{hello}=12345
    $print "  34: %d", H{34}
    $print "  68: %d", H{68}
    $print "  a17: $H{a17}"
    $print "  b17: $H{b17}"
    $print "  hello: %d", H{hello}
    
    $if H{a18}
	$print "Got a18"
```

* Dynameic array
```
include: c/darray.def

page: test, basic_frame
    $call test_foreach_static
    $call test_foreach_dynamic
    $call test_darray


subcode: test_foreach_static
    $block
	$print "\ntest foreach pn_test ..."
	$my pn_test[10]
	$foreach pn_test
	    $(t)=$(i)*2+1
	    $print "    %d: %d", $(i), $(t)

subcode: test_foreach_dynamic
    $block
	n=10
	$local_allocate(n) pn_test
	$foreach pn_test
	    $(t)=$(i)*2
	    $print "    %d: %d", $(i), $(t)

#---------------------------------------- 
subcode: test_darray
    $my darray P: int, char
    P.$resize 10
    $foreach P
	$(t)=$(i), $(i)+'A'
	$print "    %d: %c", $(i), $(t).2
    P.$resize 0
    P.$push 108, 108
    $print "last item: %d", P[-1].1

    (n, c)=P.$pop()
    $print n = $n, c = $c
```
