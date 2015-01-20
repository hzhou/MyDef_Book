# General Structure of MyDef

To MyDef, all output files are simply text files with underlying structures -- from lower level syntactical structures to higher level semantic structures. Unfortunately, the low level structures do not always align with high level structures. When we encounter conflicts, we are forced to maintain the low level structures that are demanded by the language syntax, or it will not run. However, our comprehension works at semantic level from top to down. We wish to write in a way that preserves the higher level structures but somehow still can feed into the lower level language engine. MyDef is created for such purpose.

Let's look at an example:

    AFunction(){
        local variables a, b, c, ...
        initialization1
        initialization2
        ...
        initialzationM
        process1
        process2
        ...
        processN
        finalization1
        finalization2
        ...
        finalizationM
        return
    }

It is a sequential semantic model with initialization, process, and finalization. However, imagine there are nested complex switches involved in each of above statement and there are so many statements that the whole function takes hundreds of lines. This is the case when low level structures of statements and switches obscures the high level structure of initialization, process, finalization. 

Should we wrap the codes into three functions such as:
    
    AFunction(){
        A_Init();
        A_Proc();
        A_Exit();
        return
    }    
    
As soon as we attempt this, we realize the problem: what about those many variables all three functions would access and change? Should we move them into global space or pass them as paremeters? Either approach adds an extra layer of complexity.

In addition, often the initializations and finalizations are paired. Files initially open need be closed. Memory initially allocated need be deallocated. Values initially pushed onto stack need be poped off and restored. These paired structures are initially obscured, but now with three functions, they are simply broken apart.

Now let's look at the solution MyDef offers. First, MyDef allows grouping code into subcodes, which do not bring side effects to the lower level structures. Neither variable scopes nor parameter parsing need be changed. Next, MyDef provides facility to break the subcodes apart into sematic related groups, and shuffle them back at compile time.

With MyDef, we can write:

    $function AFunction
        $call @init
        $call @process
        $call @exit
        
    #-- init/exit pair a --
    subcode:: init
        a_declare
        a_init
    subcode:: exit
        a_exit
    
    #-- init/exit pair b --    
    subcode:: init
        b_declare
        b_init
    subcode:: exit
        b_exit

    ...
    
    subcode: process
        ...

Of course, I am not insisting everyone to agree with me on how to refactor a given code. Semantic structures often varies from person to person and organization to organization, and what is best can even change within a same programmer or organization as their experiences and needs change over time. What I am trying to convince you is that often you would need some extra flexibility to bend the restrictions required by your language of choice and MyDef may assist your quest.

