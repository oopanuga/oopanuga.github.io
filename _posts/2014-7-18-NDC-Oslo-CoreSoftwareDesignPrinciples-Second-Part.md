---
layout: post
title: NDC 2014 Oslo, Core software design principles, TDA, OCP, LSP and DIP
---

Another set of core design principles by Venkat Subramaniam.

## _Tell Don´t Ask Principle_, TDA

> You want to tell a piece of code to do stuff, rather than asking a piece of code for some information and then going back and doing it.

It is violated in so many places because we usually don´t think about it explicitely.

Let´s put one simple example:
We have a collection of books and we want to print information about these books

{% highlight c# %}
var books = new List<Book>();

for(int i = 0; i < books.Count; i++)
{
	books[i].PrintInformation();
}

{% endhighlight %}

We are violating TDA principle: we are going to the collection of books and asking _hey books, give me your count_. Books say, _Ok, here is my count, I don´t know why but here we go_.
One again, inside the loop we go to the books and ask for a low level detail (the element of the _ith_ position).

But how do we do things in the real world?
If somebody comes and say: _hey, what are you doing tomorrow?_ my usual answer is: _what do you want?!_ because you don´t know what he wants. He is asking for a low level detail.

But let´s go back to the book example, how do we commit to TDA principle?

{% highlight c# %}
var books = new List<Book>();

books.Select(b => b.PrintInformation());

{% endhighlight %}

We go to the books collection and tell, _hey, print you books´ information. We are telling not asking. The new logic is:

- More readable.
- Let´s work to do an action.
- Concise and elegant.
- Easier to modify: if in the future we have to modify it, we code is in one place.
- Easier to find and to fix bugs

Similarly, when we design our classes, rather than providing methods that are query methods, methods that affect the state of the object, we should write methods that actually perform the task and give back a result.

This is what we call encapsulation in Object Oriented Programming.
Furthermore, if we remember getter and setter methods, that expose state of an object and those tiny little details, those don´t follow at all the TDA principle.

As we say, it is a little bit more difficult to follow but every time we have a piece of code, we can just stop by and say: _Am I following TDA?_




