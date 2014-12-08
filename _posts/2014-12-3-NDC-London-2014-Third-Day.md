---
layout: post
title: NDC London, third day
---
Let´s talk about the third and last day of the NDC London.

## _AngularJS for ASP.Net MVC developers_, Miguel A. Castro

Miguel started his talk sharing some really good features of AngularJS.

And them he talked about Hybrid Applications. That was shocking for me...
I was told about frameworks like Asp.Net MVC where a server builds an HTML markup that´s delivered to the client. I was told about Single Page Applications (SPA) where all necesary resources are dynamically loaded and added to a single page, thus making a rich user experience.

So why don´t we take the goodness of the two worlds and create an _Hybrid Application_, making up _sylos_ of SPAs.

ASP.Net is great to deliver a parent view from the server, setting up JS files, to do the component layout. The MVC routing take you to one of the sylos.

When you are in one of these sylos, you are inside a SPA world and AngularJS routing will take control.

He has a sample project with this architecture in his [web site](http://www.melvicorp.com/downloads.html).

## _Making steaks from sacred cows_, Kevlin Henney

![Image description](/images/ndcLondon/3day/2.JPG)

Again had the pleasure to attend to one talk by Kevlin. Kevlin is the kind of software architect with such a broad knowledge that he can be speaking for an hour about a simple concepts.

This talk was about making you think:

> Critize things you have as good practices. Critize everything.

For example...

### The Open-Closed Principle

The well [known one](http://www.objectmentor.com/resources/articles/ocp.pdf): 

> SOFTWARE ENTITIES (CLASSES, MODULES, FUNCTIONS, ETC.) SHOULD BE OPEN FOR EXTENSION, BUT CLOSED FOR MODIFICATION.

So we have the Closed part. But when should we consider to have the code closed? Can a colleague modify the code a couple of days after commiting the code? Is it even a good idea? What about refactoring?
We need some more *context*. So Closed doesn´t apply when you have influence in the code.

The _Open_ part means extends, a code should be open for *extension*.

Naming is always a good one. Why for example do we normally write _Exception_ in _IndexOutOfRangeException_? That is meant to be used in _catch_ statement so, what else can it be rather than an exception.
Maybe we should avoid Needless words, avoiding noise from the code.

## _The Architecture of Uncertainty_, Kevlin Henney

Key motive of this talk:

> Change is unavoidable in software development. Use uncertainty as a driver. Design an architecture where change is forgiving.

Let´s see some examples. 

### Class designs:

Imagine you have to represent a Date, very likely we would design this:

{% highlight c# %}
public class Date
{
  int Day, Month, Year;
}{% endhighlight %}

This representation is great, we can very easily print a date on screen.
But what if we have to say the number of days since something happened. This class would be a very bad design.

Then we shift our design to the following one:

{% highlight c# %}
public class Date
{
  int daysSinceEpoc;
}{% endhighlight %}

Which one should we take?

Instead of asking the typical question:

_There are two designs, which one do we choose?_

We ask:

_There are two designs, how do I minimize the complexity to move from one design to the other?

### Project planning

A project sometimes starts with an Analysis phase, followed by Design, development, and maybe testing.
Normally Analysis is a big phase where we try to do the best design decissions, the right choice so that later stages don´t need to change the original plans.

But how are we supposed to make the most significant decissions when we have the least knowledge of the system?

> The act of describing a program in unambiguous detail and the act of programming are one and the same.

This is totally the wrong approach: 
> Don´t try to prevent change, what we want is to ease change.

![Image description](/images/ndcLondon/3day/1.jpg)

We are very bad predicting things.

Another consequence is Roadmaps. Normally they are represented as single arrows or lines.

But can you imagine, in real life, single line maps are a bad idea. Really good maps show multiple paths, maybe two paths may take to the same place or 2 paths starting at the same point diverge.

We should have roapmaps that display multiple scenarios and paths, describing how to get from one to the other.

Another great takeaway, decomposing a model into layer help to embrace change in software, every layer may have a different likelihood for change.

I took my _97 things every programmer should know_, edited by Kevlin and he signed it for me.

![Image description](/images/ndcLondon/3day/3.JPG)

That is my summary for NDC.

I´ll do detailed posts with my favourite ones.

See you soon!