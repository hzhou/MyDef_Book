Einstein notation
-----------------

This section is heavily related to mathematics. If you are not particularly math oriented, feel free to skip this section. 

If you have studied linear algebra, you may be already familiar with this convention. I have been intrigued by this notation since the the first time I was introduced by a teacher. Only much later, I read this is actually invented by Einstein, and it is referred to as Einstein notation. Don't be scared though, it is a much simpler concept than general relativity. Because it is a powerful convention to reflect semantic meaning, MyDef implements it as well. In MyDef, it is referred to as "sumcode".

```
If 
    c = SUM a[i]*b[i], where i is over the range of the array 
Then by convention, we can simply write
    c = a[i]*b[i]

```

An example will make it clearer:

```
# assuming array @a and @b having the same size
# further assume $n is the size of both arrays

my $c=0
$for $i=0:$n
    $c+=$a[$i]*$b[$i]

# now $c is the dot-product of array a and b.
```

In MyDef, this is equivallent to
```
$global @a[10], @b[10]
$sumcode $c=$a[i]*$b[i]

```
And here is the translation:
```
our @a;                            
our @b;                            
my $c = 0;                         
for(my $i_i=0; $i_i <$n; $i_i++){  
    $c += $a[$i_i]*$b[$i_i];       
}                                  
```

A two dimensional example:
```
$global @A[4,4], @B[4,4], @C[4,4]
$sumcode $C[i,j]=$A[i,k]*$B[k,j]
```

Of course this is the famaliar matrix multiplication. Let's see the compiled result:
```
our @A;
our @B;
our @C;
for(my $i_i=0; $i_i <4; $i_i++){
    my $k_ij;
    $k_ij = $i_i*4;
    for(my $i_j=0; $i_j <4; $i_j++){
        my $k_ik;
        $k_ik = $i_i*4;
        my $k_kj;
        $k_kj = +$i_j;
        my $sum=0;
        for(my $i_k=0; $i_k <4; $i_k++){
            $sum += $A[$k_ik]*$B[$k_kj];
            $k_ik++;
            $k_kj += 4;
        }
        $C[$k_ij] = $sum;
        $k_ij++;
    }
}
```

Impressive, isn't it? Einstein's genius!

Now let's try summarize the rules. MyDef's $sumcode will look for equal sign and distinguish between left side expression and right side expression. For both sides, MyDef look for array notation such as "$C[i,j]", which is a variable followed by square brackets and a comma seperated index list. MyDef recognizes letter i, j, k, and l as valid indexes. For each index appears in the expressions, sumcode generates a loop over that index, and the loop range is established by variable declaration. The ranges for different array variables over the same index letter has to agree or MyDef will complain. Now if there are indexes only appears on the right side, a summation occurs and the sum will be assigned to the left side. 

It is not as complicated as it appears, but it does takes some getting used to, which is what we went through in our linear algebra class. But once we get used to the process, it reduces to simple units in our comprehension. But if you write it out in code as nested loops, that efficiency we reached in our mind disappears. Einstein's notation is essentially a notations on what our mind goes through, and MyDef allows us write in the exact way as we think.

Einstein's notation is created to manipulate matrixes. But the rule can be extended to basic loops:
```
$global @A[10]
$sumcode $A[i]=i**2
$sumcode print "i: $A[i]\n"
```
Following the rule, no distinct right side indexes means no summation, just a loops:
```
our @A;
for(my $i_i=0; $i_i <10; $i_i++){
    $A[$i_i]=$i_i**2;
}
for(my $i_i=0; $i_i <10; $i_i++){
    print "$i_i: $A[$i_i]\n";
}
```
