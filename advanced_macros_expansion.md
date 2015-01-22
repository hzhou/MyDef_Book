# More on macro expansion

We already learned we reference a macro useing "$(macroname)". Here are some additional tricks.

Note, in the following syntax we will refer to either character index or list index. They are always 0 based. So index 4 actually means the 5th. If you are programming C or Perl, you understand, right?

* $(macroname:0-3)
    - expands to a substring of $(macroname) from chracter 0 to 3 inclusive (the first 4 characters). The range can be any two integers. If the range doesn't make sense, it expands to empty.

* $(macroname:0:3)
    - same as above

* $(macroname:4:word)
    - First take the substring of $(macroname) starting at character 4 (any number, 0 based), then takes the leading word (chars that are alphabet or digits or '_'). If $(macroname) is "1234num_10.", $(macroname:4:word) is "num_10".

* $(macroname:4:number)
    - First take the substring (same a above), then takes the leading number (including a sign if any). If $(macroname) is "-1234num_10.", $(macroname:0:number) is "-1234".

* $(macroname:4:strip)
    - Simply takes the substring of $(macroname) starting character 4 (any number, 0 based).

* $(macroname:len)
    - Expands to the length of $(macroname).

* $(macroname:strlen)
    - If $(macroname) is a "double quoted string", it expands to the length of the string inside the double quote. Otherwise, same as above.

* $(macroname:list:n)
    - $(macroname) is treated as a comma seperated list, and it expands to the number of items in the list. e.g. If $(macroname) is "a, b, c", $(macroname:list:n) expands to 3.

* $(macroname:list:4)
    - $(macroname) is treated as a comma seperated list, and it expands to item at index 4. 
* $(macroname:strip)
    - If $(macroname) is surrounded by quotation marks or parentheses or brackets, they are stripped. If not, beginning and ending character are stripped anyway, so you better be sure.

Finally, there is inline macros with arguments. This is better explained with an example:

```
macros:
    greet: Hi, my $1 is $2.

page: t
    I said, "$(greet:name, Hui)"
```
This will become:
```
I said, "Hi, my name is Hui."
```

To expand $(macroname:list), first the pattern $(macroname) is looked up, then list is treated as comma seperated list (spaces will be trimmed around comma), then the first item replaces '$1' in the pattern, the second item replaces '$2' in the pattern, and so on. If $1 or any of the $index is mising, that item replaces nothing (simply discarded). You can have multiple $1 or any $index in the pattern. If there is $3 in the pattern but only two and less is supplied in the list, '$3' will remain in the expanded string. In the above example, $(greet) is a perfectly good macro reference.
