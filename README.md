Introduction
=======

Let's say you have been programming for a while, do you find yourself complaining about your programming language and wish some more intuitive way to write it? Did you ever wish your programming language will have more natural way of doing object oriented programming, functional programing, or simply imperative programming? Did you sometime would like to refactor your code into some cleaner structure but the cost of sorting out its effects prohibits you to do so?

I had all these problems.

I hate semicolons. I can read my code without semicolons and understand it with no ambiguity, why I still have to remember to append them each time, and get really annoyed each time I forgot?

I hate curly braces. I was being forced to write opening brace, and from my wisdom, I have to immediately make new line and put down the closing brace, then I need go back into the braces to start writing, by which time my thought flow is often already interrupted. I always have trouble visually matching braces so I always manage indentations. but if I did that, why do I still being forced to use braces, or "endif", "endwhile", or "endfunction"? 

I hate boilerplate code. If they are boilerplate, there is only one information node for me, why do I have to clutter my reading with so many lines and worse, having them spread over my entire code?

I complain, and then I think about doing something about it. I am not going to let it ruin my love for programming. That is why I am now addicted to MyDef.

Often, when we complain, we look for new language. But that is misleading. There isn't a perfect programming language. There are two sides of programming. On one side, we want specific, accurate, context free. We want the machine do things consistently. On the other side, we want efficiency, flexibility, and we want the machine be able to *guess* what we mean. The goals of these two sides are in conflict by nature. The variety of programing language is basically a balance between these two desires. We want both, therefore, we will never find a perfect programming language.

So the solution is to adopt two layers of programming context. In the lower layer, we want the language specific, accurate, and context free. Ideally, we want to be able to prove a program in such language to be bug free. On the upper layer, we want convention, context, and intelligence. Between these two layers are short feedback loops: we say something, either it went fine or we got signal, then we adjust how we say it and the feedback loop gets run again. We adjust our convention, so we got hit by the feedback less and less often -- the way to nirvana. That is actually how we do things in our natural langauge. And as in our natural language, we want to write as we think in our mind.

MyDef is that upper layer, and you can stop complaining about how the lower layer does not understand you and simply focus on its merit of accuracy while let the uppder layer solve these problems for you.
