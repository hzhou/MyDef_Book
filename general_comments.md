# Comments And Empty Lines

In MyDef, single line comments start with '#', and multiline comments are marked by '/*' and '*/'. This may seems similar to bash and C respectively, but there are some MyDef quirks.

```
#This is a comment, entire line is skipped
subcode: A
    line A1
    # another comment, entireline is skipped
      #so is this
    line A2
  # Another comment, but breaks indentation unit
    line A3

page: t
    $call A
```

The compiled output:
```
Line A1
Line A2
```
When a line starts with '#' with optinal leading spaces, the entire line is skipped. However its indentation remains significant. In the above example, comment above "line A3" is less indented than "line A1", effectively ends the entire subcode: A block. "line A3" is in fact outside either subcode or the page block, therefore, effectively being ingored.

Line comments don't have to start on empty lines. They can be appended to the end of any line. But instead of initiated by '#', there has to be one or more spaces after '#'. This is to allow the construct of '#1', '$#a', etc. that are common in many language syntax.

```
page: t # comment, a page block
    We are #1. # comments only start at the second '#'

```

The multiline comments are restricted whole lines only. C/C++ programers should pay special attention here.

```
page: t
    This does not start a comment /* not a comment
    */
      /* but this does start a
         multiline comments. 
	 And this */ does not end the comments.
	 This does. */
```
The above page compiles to:
```
This does not start a comment /* not a comment
*/
```

All these quirks are designed to minimize interferences with underlying language syntaxes. They are also designed to conform to the most typical usage. One of the aim of MyDef is to reduce the amount of necessary comments by allow the coder to arrange their code blocks semantically. If necessary, coder should apply clustered comments for a group of code rather than line by line commenting. But if line by line commenting (at end of each source line) is preferred, one need remember not to forget the space behind '#'.

Not commented lines are being ignored by MyDef, empty lines are also being ignored.

```
page: t
    line 1

    line 2
```
will compile to
```
line 1
line 2
```

This is not a problem as most common programming languages ignores empty lines anyway. But what if we want to output empty lines? Hopefully these situations are rare, but if necessary, one can use "NEWLINE":

```
page: t
    line 1
    NEWLINE
    line 2
```
And this will compile into:
```
line 1

line 2
```
as we wished.

Similar to "NEWLINE", a line starts with "NOOP" also has special meaning in MyDef, it is treated as empty line. Why would we need it? Most likely you don't. But sometime we want it for cosmetic reasons:
```
page: t
    module: perl
    $if $#ARGV>0
	NOOP
    $else
	die "Argument required!\n"
```

