---
layout: post
title: FSharp, the keyword of Shame
---

We have now NDC Oslo 2015 videos available [here](https://vimeo.com/ndcconferences). I really recommend to take a look because the quality of the talks is amazing as usual, and they cover a broad range of topics that may be of interest for you.

I was taking a look to the videos. Of course Venkat Subramaniam didn't want to miss the party: he did a [terrific talk about immutability](https://vimeo.com/131635253). Venkat not only educates but also does really nice jokes, that endow the learning experience.

<iframe src="https://player.vimeo.com/video/131635253" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe> <p><a href="https://vimeo.com/131635253">The Power and Practicalities of Immutability - Venkat Subramaniam</a> from <a href="https://vimeo.com/ndcconferences">NDC Conferences</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

> Immutability is an act of programming where you don't modify something that actually exists, which also includes not causing the so-called _side effects_.
So writting something that interact with the outside world in order to modify the state of the world is considered impure.

## FSharp immutability

In contract to pure functional languages like Haskell or Erlang, we have hybrid languages, like FSharp and Scala. They are somewhere between Object Oriented and Functional. We'll see why in a moment.

In FSharp we can write something like this:

{% highlight c# %}
let max = 100
printfn "%d" max
max = 200
printfn "%d" max
{% endhighlight %}

What do we get in the console? In this case we will print two times 100. The reason for that being that FSharp uses the single equality in the second statement, not as an assignment but as a comparison.
That is, the value of the expression ```max=200``` is evaluated to ```false```.

In pure functional languages this is not possible at all. But in hybrid languages, there is a way to modify a variable. 
In F# is with the operator ```<-```, something like this:

{% highlight c# %}
let max = 100
printfn "%d" max
max <- 200
printfn "%d" max
{% endhighlight %}

What is the result now? In this case the compiler gets really angry:

> Are you kidding me? You can't modify this variable, it is immutable.

## FSharp, the keyword of shame

{% highlight c# %}
let mutable max = 100
printfn "%d" max
max <- 200
printfn "%d" max
{% endhighlight %}

We have replaced the declaration with the keyword *mutable*, that is, we are telling the compiler that this variable can be modified later.
Now we get 100 and 200 printed in the console.

The **Mutable** keyword is also known as **the keyword of shame**. Anytime you use it, you can never make eye-contact with your colleagues, you hide in a corner, thinking in the problem you have caused.
You walk by and some colleagues point at you: 

_Look, that is the guy that used "mutable" keyword_.

Jokes aside, I really like the fact that F# is immutable by default, if you want to be mutable, then you have to treat it differently:

> Erlang, no mutability at all.
> FSharp encourages immutability, you have to work extra to do it mutable.
> CSharp encourages mutability and you have to do something really special to be immutable.

Totally different approaches. Even if you are a C# programmer, learning some functional languages will change the way you program in C#, you will encourage immutability, you will favour composition over assignments.
With so many different functional languages out there, with different approaches for different paradigms, you can choose to learn a pure functional language, like Haskell. You will have a totally different experience, it will totally be a mind blowing.
In contrast, you can choose to go for a Hybrid approach where you can still use your same OOP paradigms while starting to play with functional programming.

