# Block call

When duplicated codes are contiguous blocks, they are easy to detect and factor. Simply subcode will do -- with parameters if necessary. However, I often find another class of code duplication very annoying:

```
open In, $file or die "I can't open $file!\n";
while(<In>){
    # process the file line by line
    # potentially very complexed but interesting each time.
}
close In
```

Before MyDef, I must have repeated such code thousands of times. Albeit simple, every time it takes almost a minute -- note that I have to put down the closing bracket and the "close" statement as well -- before I get to the interesting part in the middle, and by that time, my working memory has already been flushed and I need pause to remember what kind of interesting stuff I was thinking ... 

What I wished was write code like this:
```
&call open_r, $file
    # do my interesting stuff ...
``` 

MyDef allows just like that! To achieve that magic, first we have to change from '$call' to '&call' -- we have to tell MyDef what to expect; MyDef never pretends to be as intelligent as we are. Next we have to define the subcode a bit unusual:

```
subcode: open_r(name)
    open In, "$(name)" or die "I can't open $(name)!\n";
    while(<In>){
	BLOCK
    }
    close In;
```

You certainly noticed what special we have done -- a special place holder, BLOCK. And that is all.

It is easy to be carried away. One time I was programming a 3-dimension array, and I have:

```
subcode: loop_x
    for(i_x=0;i_x<n_x;i_x++){
	BLOCK
    }

subcode: loop_y
    for(i_y=0;i_y<n_x;i_y++){
	BLOCK
    }

subcode: loop_z
    for(i_z=0;i_z<n_x;i_z++){
	BLOCK
    }

subcode: loop_xyz
    i=0
    &call loop_z
	&call loop_y
	    &call loop_x
		BLOCK
		i++;

page: t
    module: c

    &call loop_xyz
	# manipulate array[i]
```
I'll leave to you to judge whether that is too obssessive.
