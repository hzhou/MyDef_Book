Subcode vs. sub
---------------

In MyDef, one can define subcode, and then simply $call them, even with parameters. On the other hand, the underlying language also provides native function structure. In Perl, this is "sub". When do we use MyDef's subcode, and when do we use native sub?

MyDef's subcode are pure semantic structure with zero side effects to the underlying language structures. One can group any portion of his code into a subcode without worry about variable scopes or performance overhead. Subcode serves pure refactoring purpose. In addition, certain semantic structure such as cumulative, decentralized definition, block call, pure text based parameter map, these may only be possible with MyDef subcode.

On the hand, the native function isolates varibles scope and provides safety. Many language native features such as callbacks and API interfaces may require native function. In addition, the native functions isolation from other code can simplify context.

So the answer is depends. 

Typically, we use mostly subcode when we are writing new codes because of its flexibility. Then we will factor some of the code into function when we refactor the code.

But to Perl, this is a less troubled question. Perl is flexible on its own. Most of the time, we can either use subcode or sub and switching from subcode to sub is often trivial.

