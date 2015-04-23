## perlcode

Let's say we want to write

```
$call my_macro, line
```

where "line" is a domain-specific language (DSL) that we want to parse and translate into code of our intension. Even though the DSL may be very simple, we can't easily implement it with the first layer subocde. What we can do is to define `perlcode` instead:

```
perlcode: my_macro
    #- the line text is provided in variable $param
    #- write your perl code parse $param
    #- do your translation

    $foreach $l in @translation
        push @$out, $l
```

MyDef is essentially reading your input lines and traslating into `@$out`. So with perl code, you can simply do that in Perl. 
