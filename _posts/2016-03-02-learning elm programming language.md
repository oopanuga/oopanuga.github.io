---
layout: post
title: Learning Elm, first day
---

## Why Elm

I've been learning Elixir language the last few months. It's been a great experience so far, Elixir is focused on Developers happiness and productivity.
Finally got my head around Functional Programming.

I've been hearing about Elm for a long time now. A functional programming to write Frontend code, a strongly typed language, it compiles to javascript.
If you know ReactJS you are probably familiar with Flux architecture. One of the most popular implementation is Redux. If you go to the [Redux github page](https://github.com/reactjs/redux) you'll see the following:
```
Influences
Redux evolves the ideas of Flux, but avoids its complexity by taking cues from Elm.
```

So Elm seems to be not only an amazing language but also an architecture. 
We have no reason not to try it. Let's go for it.

## Getting started

Installing Elm is as easy as getting the installer from [http://elm-lang.org/install](http://elm-lang.org/install).
It comes with Elm Repl, a command line utility that will help us trying expressions quickly.

## Simple Expressions
{% highlight elm %}
> 5 < 4
False : Bool
> [1, "2"]
Compilation Type Error
{% endhighlight %}

We are starting to see Elm strongly typed features. Remember now how many times we do something like this in Javascript, leading to awkward bugs, arising its ugly face in the browser.

We can use the traditional if statement:
{% highlight elm %}
> juan = "developer"
0 : number
> if juan=="developer" then "what are you doing" else "alright"
"what are you doing" : String
{% endhighlight %}

Or multiline if:
{% highlight elm %}
> juan = "developer"
0 : number
> if juan=="developer" -> "what are you doing" \
> juan=="other" -> "weird" \
> otherwise -> "I do not know"
"what are you doing" : String
{% endhighlight %}

Using pattern matching:
{% highlight elm %}
> languages = ["elixir", "elm", "haskell"]
["elixir", "elm", "haskell"] : [string]
> case languages of \
| head::tail -> tail \
| | [] -> []
["elm","haskell"] : List String
{% endhighlight %}

In Elm weuse ```type``` to create more complex data structures, let's define a car:
{% highlight elm %}
> type Color = Red | Black | White
> type Brand = Renault | Opel | Volkswagen
> type Car = CP Color Brand
> myCar = CP Red Opel
CP Red Opel : Car
{% endhighlight %}
