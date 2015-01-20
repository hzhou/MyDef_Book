Subcode
=======

Inside a page unit, one can multiple subcodes. Each subcode is a block of text lines that MyDef will translate into output codes. 

```
page: t
    subcode: main
        Start of the text.
	Line 2
	    Line 2.1
	    Line 2.2

    subcode: A
        Line A1
        Line A2

    subcode: B
        Line B1
        Line B2
```
What do you think the compiled output will be? Let's see:

```
$ mydef_page -mgeneral t.def
PAGE: t
  --> [./t.txt]
$ cat t.txt
Start of the text.
Line 2
    Line 2.1
    Line 2.2
$
```
Did it suprise you? MyDef looks for "subcode: main" to start compile, and it compiles "subcode: main" only. If you forget to have a subcode: main, MyDef will silently output an empty file (with correct file extension). To include the content of other subcodes, we should "call" them:

```
page: t
    subcode: main
        Start of the text.
	Line 2
	    Line 2.1
	    Line 2.2
	    $call B
	$call A

    subcode: A
        Line A1
        Line A2

    subcode: B
        Line B1
        Line B2
```

This time, the compiled output will be:
```
Start of the text.
Line 2
    Line 2.1
    Line 2.2
    line B1
    line B2
line A1
line A2
```

MyDef will mark and strip the indentation of each line. However, within  each subcode, all leading spaces below the leading indentation are preserved. 

MyDef recognizes tab setting of 8. Please note that some code editors will default to a tab setting of 4 or even 2. These will work if one exclusively uses tab to indent each line, but will be a diaster if he ever mixes spaces and tab. There will be no visual indication whether the spaces leading a line consists of how many spaces or tabs, and MyDef may interpret the indentation very differently from what visually appears. To work with MyDef -- this is also true to work with Python -- one need make sure their editor's tab setting is set to 8. When tab setting is 8, MyDef will recognize indentation consist to its visual appearance. Any amount of spaces for each indentation is accepted as long as they are consistent within an indented block.

Readers may recall early examples skip subcode all together:

```
page: t
    Some text.
```

This in fact is the same as:
```
page: t
    subcode: main
        Some text.
```

One there is only one subcode inside a page, subcode: main is assumed. But if we take advantage of this default, all subcodes has to be listed outside of the page block.

```
page: t
   Some text.
   $call A
   $call B
   $call A

subcode: A
    Line A1
    Line A2

subcode: B
    Line B1
    Line B2
```

Is there any differences between subcodes listed inside a page block and those outside? Not really, except when multiple pages is included in a same def file, each page may need subcodes with same name but different content, in which case, those subcode with name conflict has to be listed inside each page.

This raises an interesting technique. Let's say we make a general frame subcode which calls for some additional subcode.

```
subcode: basic_frame
    <html>
    <head>
        <link rel="stylesheet" href="gitbook/style.css">
    </head>
    <body>
        $call content
    </body>
    </html>

page: page_1
    subcode: main
        $call basic_frame
    subcode: content
        <h1>Page 1</h1>

page: page_2
    subcode: main
        $call basic_frame
    subcode: content
        <h1>Page 2</h1>
```

This kind of usage turns out to be very common. Think about a website having pages with same headers and footing, or API(application programming interface) routines that require same initializations and finalizations, or some applications such as open gl or mobile apps. Essentially, we are creating our own API. The bad side of this practice is it obscures the higher level structure. It may not immediately clear where subcode: content is being called.  The problem of obscurity is common among all API applications. But unlike the typical API's function interface, the subcode call is vanilla simple and straight forward, and it is never in binary form. So a straight grep should reveal the source of frame subcode for one to study or even freely modify. 

Due to the commonality of frame subcode usage, MyDef also recognizes another shortcut:

```
page: page_1, basic_frame
    subcode: content
        <h1>Page 1</h1>
```

This is equivallent to above but with less typing.

Modern IDE editors will often have templates or ability to automatically generate frame code upon file creation. However, it only solves the complexity problem at creation time. During on-going development, we still need routinely figure out where to insert or edit our code; and during code revision or refactoring, we still need figure out where our relavant code lies among the clutter of frame code. MyDef let us refactor top level structure consistently from creation throughout on-going maintaininence, which we believe provides a better solution.
