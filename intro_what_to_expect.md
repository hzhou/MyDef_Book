# What to expect

MyDef is not a programming language. 

A programming language is a rather low level construct, mainly catering toward machines. As such, its structures are rather inflexible. It often demands to be context free, or always have the context well specified.

On the other hand, our mind works on a semantic level, whose structures are quite fluid. We can think about a wheel, and we can think about a material, both quite independently. To our mind, we seperate them when we study them seperately, but we can also mix them when we need put them together. A semantic meaning is almost always within a context, and often the context is complicated but will be taken for granted without being specified.

MyDef is a meta layer that tries to cater toward how our mind works. MyDef does not intrisically respect the syntax of underlying programming language. Its fundamental model simply treats the source code as plain text. Therefore, MyDef can better focus on realizing our semantic structrures.

But don't get it wrong, it is not one or the other. Our mind can easily shift focus back and forth between higher level semantic structure and lower level language constructs. We can program directly in assembly language as well as Basic, and Java. One is easier than the other depend on the degree that our mind need to bend. However, none of the programming language can reach a level what I would call "What you see is what you think" (WYSIWYT). MyDef aims at WYSIWYT.

An analogy can be drawn with our natural language. The language itself can actually be rigidly defined by a set of rules, just like a programming language. But our mind do not work rigidly. If the language is context free, our mind is context dependent. We utilize context to achieve efficiency. Within context, we bend syntax, we borrow words, we imply meanings, it is still very clear within context, but computers no longer can parse, it is now "natural language". We still think, speak, read and write in the underlying language such as English, French, Chinese, but meanwhile, we are thinking beyond the boundary of the language restriction, we freely bend and borrow and sometime break the underlying rules. We speak in natural English, natural French, natural Chinese, and often with an accent which we never notice we had. With MyDef, I think we will still program in nowadays tried and true programming languages such as C and Perl, but it will be natural C and natural Perl.

Take a moment think about how we do our daily communication. We simply speak what we think, which are always confined in a very narrow context. We do not specify or clarify these context to our communication parter. But our partner would not be stomped by this lack of context. He will simply assume a context, based on his own context as well as many conventions, and guess what we mean, and then he would respond. Stripping away the context, we speak in a ambiguous way, but within convention, we guess correctly most of the time and the communication proceed efficiently. Do we always guess correct? Not always, but we simply notice from the response -- a very short feedback loop -- and make necessary adjustment and proceed. 

MyDef wants to be that communication parter, so you can write programs as you think, and MyDef will follow the convention and make guesses. MyDef will strive to guess it correctly for most of the time. MyDef will misinterpret you sometime, but the underlying language compiler, which is designed in a context-free mode that always demand precision, will give you feedbacks (if integrated with editors, can be quick and automatic), and you'll simply make adjustments and proceed like nothing happened. 

We believe this is achievable and we hope you'll like it.





