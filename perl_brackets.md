Avoid brackets
--------------

I don't know about you, but I hate those curly brackets. You put down an opening bracket; then your mind goes on the actual meaning of the statement; by the time you finish the statements, you already forgot you have an openning bracket dangling, and reminding yourself takes effort and is disruptive. So as you become experienced, you get the habit of writing the closing bracket immediately after you put down the opening bracket, which means you need make a new line, consider indentation, and then go back and make new lines in between. By the time you get back, you need remember what you were trying to do because that maneuver is enough to flush your working memory. 

That is only on the writing side. When you read them or trying to fix some bugs, it is alway difficult to figure out the pairing because you often have many nested layers. By the time you figured the pairing, your working memory is again distrupted. 

Use indentaions! 

Experienced programmer already conventionally use indentations, in which case, the brackets are like semicolons, but worse. So we need to avoid them. 

MyDef has following conventions:

If - elif - else
----------------

```
$if cond
    block
$elif cond_2
    block
$else
    block
```
will be compiled into:

```
if(cond){      
    block;     
}              
elsif(cond_2){ 
    block;     
}              
else{          
    block;     
}              
```

Not only MyDef lets you save the curly brackets, it also let's you save the parenthese around condition. Parentheses are a similar evil as brackets. They also need pairing which are disruptive both during writing and reading. But parenthese are more necessary than brackets. Still, MyDef tries to help out as opportunity fits. 

In the above example, the MyDef version is not only more easy to write and read, but also more aesthetically pleasant. Without brackets, we simply write in a straight forward fashion, no more going back and forth. 

This convention goes a long way to clean up our code. We use *a lot of* switches.

Some special cases need mention.

```
$if cond
$else
    block
```
will translate into:
```
if(cond){
}
else{
    block;
}
```
MyDef recognizes the missing indentation and add the empty block for us! 

I would like comment more on this. We could equivallently write:
```
$if !cond
    block
```
However, we should recognize that "!cond" is semantically more complex than cond -- it is a negation after we finish processing "cond", especially when "cond" is some complicated logic expression. I often find my mind goes something like "if condition, not our case, so we need opposite case, ..." when I was figuring outr "!cond"; so at some point, I have switched my habit of writing just as that thinking. 

Actually, I often write as:
```
$if cond
    # do nothing
$else
    proceed
    ...
```
I find the symmetry in the visual blocks shortens my comprehension of the meaning.

Perl programmers are often fans of one-liners. Such as:
```
die "Can't open $foo: $!" unless open (FOO, $foo);
```
I believe these Perl habit is what makes the language ugly. A line of statement is one unit in a sematic structure. A siwtch is a structure of units and its complex to comprehend. Tucking all these semantic units into a one-liner does not reduce the sematic units, but obscure it because now it visually appears to be a single sematic unit. Therefore I prefer:
```
$if open($f)
    $call process the file
$else
    die "..."
```
Of course, quickly as we experience, these kind of construct become conventions and our mind no longer need process them, then we will tuck the whole thing into a library and do something like:
```
&call open_r, $f
    process
```

On the same note, I am also having an opinion against conditional expression:
```
$a=$ok? $b: $c;
```
I would prefer:
```
$if $ok
    $a=$b
$else
    $a=$c
```
It is a switch, putting them together doesn't make the logic simpler. If we want to reduce the logic into an established unit, then make a subcode or macro, where it is really being reduced into a single unit.

But that is just me. Everyone is allowed to have their own preferences or even idiosyncrasies. Whichever it is, you can use MyDef to help realize it.

While loop
----------

```
$while cond
    block
```
will be translated into:
```
while(cond){
    block;
}
```

I often find myself using the following construct:
```
$while 1
    ...
    $if exit_cond
        last
```
Because very often, I just had an idea of loop, then I slowly flesh out the condition when the loop will end. I simply write the way my mind works. And then later, if the condition turns out simple, I would refactor the code to:
```
$while cont_cond
    ...
```
Because the way my mind works changes upon comprehension vs. composing. Our context changes, and MyDef makes reflecting that change easy.

Foreach
-------
```
$foreach $item in @array
    process_item
```
will be translated into
```
foreach my $item (@array){
    process_item;
}
```

In this case, the parentheses are even spared in case of explicit list:
```
$foreach $item in 2,3,5,7
    process_item
```
Again, MyDef do not try to interpret the specific language syntax. It simply trusts that we know what we are doing.

For
----
It is probably me again, but I find the general for loop syntax too obscure for comprehension.
```
for(init;check;step){...}
```
That is like a total three units of semantic structure in a complex relationship, which visually in conflict to the appearence of single line. In practice, most often we use it for simple loops such as:
```
for(my $i=0;$i<$n;$i++){...}
```
which simply means repeat from 0 to $n not including $n. Of course which side of the range is inclusive or exclusive is confusing, but the language and the experienced programmers have well established idioms to follow. In this example, we no longer go through the three units of semantic comprehension. Rather it is simply a range and a loop. To reflect that, MyDef allow you to write:
```
$for $i=0:$n
    # do you stuff
```

More generally, one can use:
```
$for $i=i0:i1:step
```
to mean
```
for (my $i=i0;$i<i1;$i+=step)
```
unless step has a '-' sign in front of it, in which case it means
```
for (my $i=i0;$i>=i1;$i+=step)
```
Pay special attention that condition is "$i>=$i1", which means it is inclusive and is *different* from when counting up. This is because we typically find 10:2:-1 difficult to accept if 2 is not being included. In fact, our naturally mind is inclusive for ranges. Only in the case of C and Perl programing world, we have become used to way of i=0;i<n;i++ sort of counting, and if we use inclusive for counting up, we become confused.

Anyway, if you find it confusing, stick to the non-confused expression such as "$for $i=0:$n". For other, you always can use:
```
$for my $i=$start;$i>=$end;$i--
```
In fact, you can generally use
```
$for init; cond; step
```
and MyDef will faithfully do
```
for(init;cond;step){
    ...
}
```
no matter what text you put into "init", "cond", and "step".

Because in practice we use for range of "0:$n" so much, MyDef recognizes 
```
$for $i=$n:0
```
and it will be translated into
```
for(my $i=$n-1;$i>=0;$i--){
    ...
}
```
Exactly the reverse of "$for $i=0:$n". Again, if you are not comfortable, you don't have to use it.


