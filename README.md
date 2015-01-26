Introduction
=======

Donald Knuth once promoted a new approach to programming called *literate programming*. The idea is we write programs to machines, but human mind is not like machines and cannot easily comprehend the code. So literate programming advocates writing to communicate to human using ordinary human language. 

There are a few advocates for literate programming, but it never really catch on and I don't think it ever will. Because the practice of literate programming is essentially writing two versions of a program, the code, and the documentation, with the documentation much more verbose than the necessary code. People naturally will seek the simpler path.

However, the problem of communiction in programming is not a language issue. Experienced C programmer do think in C, and experienced JavaScript programer do think in JavaScript -- at lease in some sort of pseudo code. Doing literate programming is like being forced to translate into Chinese while thinking and writing in English, certainly can be done if dedicated but never pleasant. The current problem in programing is we are being forced to write code in a way inconsistent to what is happening in our mind. 

For example, let's try look at a typical code. This is start of main.c from the vim project:
```
#define EXTERN
#include "vim.h"

#ifdef SPAWNO
# include <spawno.h>		/* special MS-DOS swapping library */
#endif

...


/* Struct for various parameters passed between main() and other functions. */
typedef struct
{
    int		argc;
    char	**argv;

    int		evim_mode;		/* started as "evim" */

...

```
There is no way a mind can digest that. Let's jump to the main function:
```
main
(argc, argv)
{
    char_u	*fname = NULL;		/* file name from command line */
    mparm_T	params;			/* various parameters passed between
    ...

#ifdef __EMX__
    _wildcard(&params.argc, &params.argv);
#endif

#ifdef FEAT_MBYTE
    (void)mb_init();	/* init mb_bytelen_tab[] to ones */
#endif
#ifdef FEAT_EVAL
    eval_init();	/* init global variables */
#endif

#ifdef __QNXNTO__
    qnx_init();		/* PhAttach() for clipboard, (and gui) */
#endif

...

[Each ... represents many many lines being omited.]
```
In fact, the whole main.c is 4155 lines long. How can any one's mind follow the way these code is written. Of course one needs to corss referencing and jump back and forth and putting in time, but I think you can see my point: the code is not written in a way consistent to how a typical mind would work.

Of course I believe when the first line of vim is being written down, it is in a much easier to follow mode, and what we see here is years of add-ons. But this is not a good excuse. A good book is often being worked on many many revisions, but it never will become in consistent upon reading (if the editor is good). The problem the way we program is obvious: the current programming language structure prevents us to write in a way consistent to our mind. 

MyDef is aiming to change that. It is a semantic macro layer to enable us to write code consistent to how we think about the code in both the writing stage and reading stages. Ideally, the code written in MyDef should be intuitive and natural to comprehend, even to a human mind (given that he is fluent in the underlying programming language and model), with limited amounts of comments.


