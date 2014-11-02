
---
layout: post
title: NDC 2014 Oslo: Core software design principles (DRY, YAGNI and SRP+SLAP)
---

So here we go with one of the greatest talks I went in the conference: *Core Software Design Principles by Venkat Subramaniam*.

![Image description](/images/ndc/venkat.jpg)

I didn´t know Venkat before the conference (you can blame me) and everybody told me he was a good speaker. I went and it was a lot more than what I expected.
If you want to know more about Venkat I think one of the best ways would be to go reading one of his [books](http://www.amazon.com/Venkat-Subramaniam/e/B001JOS4R2).

I went also to his talk *Transforming C# into functional programming*, samely awesome.

Today I wanted to share with you the highlights of his talk *Core Sofware Design Principles*, because it turned out to be the clearest explanation of *SOLID* and many other principles I´ve ever received.
I wish I could explain it as good as he did.
Maybe you are thinking _I know SOLID, leave me alone". Well, I thought I kew but... what about Liskov substitution (its name doesn´t give it any favour)? Do you know YAGNI, DRY?_

If you have been reading about design patterns, code, principles, you´ll probably notice that one of the cornerstones is Simplicity. No matter what you read, simplicity is always the goal. Here you can find a few hints about it: http://codexfun.blogspot.com.es/2014/06/keep-that-simple.html

What I really liked in Venkat´s lecture was the real life examples he put and the passion in his explanations. He also made use of code examples that ease understanding of the concepts so much.

Design Principles

Principles are a set of practices that help us create better quality software, which are found to be effectived for many programmers along the years, if applied in the correct way in the right degree.

We shouldn´t confuse principles with patterns. If we take a pattern and try to apply it to a problem, it may be that we can´t apply it. Nevertheless a principle is something we always strive for so that it offers a bigger value than anything else. A pattern is a specific solution to a specific problem, and the solution is always met by trying to meet some principles. E.g. Strategy pattern solves a problem when a system is prone to change by meeting the Single Responsability Principle.

Nevertheles, principles, as patterns,  offer a common vocabulary to express ideas to communicate each other, so that when you are in technical grooming somebody might say: You can´t do this because, it will break OCP.
We must be careful because we may, as patterns, overuse them.

Venkat told he doesn´t like the term SOLID that many programmers are used to, because there are many other principles that are as important as SOLID.

So let get this party started!

DRY Principle, Don´t Repeat Yourself

Every knowledge in a system must have a single authorative unambigous representation

When we try to follow this principle we must ask the following: where is this piece of knowledge in my code?


Who´s playing the guitar? What if we have a bug in the guitar:D

Imagine we have a system that creates Requisitions. What if in the future we have to change this knowledge to create requistions? If we spread this logic into several places in the code, we would have to change all of these places, making a dupplication effort. And when bugs arise we would have to fix bugs in multiple places.

Imagine we have a web application with several pages. In several of these web pages we have a field called OrderNumber, on which we perform some validations. We should strive to have these validations over OrderNumber in a single place. Because of the same reasons.

DRY is not only about duplication of code, but also about duplication of knowledge.

YAGNI Principle, You Ain´t Gonna Need It

This principle is telling us that something we are coding now, won´t be required at all.
When we are designing a system from scratch we put a lot of different things in place: validations, new data types, generics for better reuse, extensibility mechanism,...
Question is: Do you really need it? If the answer is No, we should postpone the decission until we know it better.


Somebody already invented a lips painting mask, just in case. I don´t think it even works but any girl in the room would better tell:)

Apparently this principle makes Venkat´s mates angry: they took a lot of time to make that generic superb cool class, you better use it!
What he does is that he adds: You Ain´t Gonna Need It + Yet!
Isn´t this cool, we have a way to avoid angry colleagues. Argument is the following: requirements may change, right? So why don´t we postpone the decission to add this generic class for future sprints until we have a better knowledge. It may be that we need it, but we don´t know it YET!
If so, we can track back the history of the project and include this class again.

Bear in mind that most of the extensions and generic component that we add in our code are likely not to be used. Maybe you have experience the following disgusting situation:
-Code reviewer: "This generic interface is cool mate but you have only one concrete implementation."
-Programmer: "Oh, yes, but it is already there, so in one year we could add a second implementaion! Don´t piss me off"

SRP, Single Responsibility Principle

Every piece of code must have ONE purpose, ONE reason. 
One of the best known, in my opinion because of its simple meaningful name, or not? :)

This principle is about Cohesion: things that go together must belong together. If not, they should go separate.

We all have been students and sometimes we didn´t have time to tidy up our room, not gonna blame you if this was your "clothes box":


What if we want to wear our favourite dress for a date with a beautiful girl. It can be tricky, but we can make it, we may use a B-Plan:



Our parents probably tried to teach us how to tidy up our clothes, but it never worked!
We eventually went serious with that girl, we got married... She was a wonderful and tidy girl, our wardrobe became this:



Isn´t this a realm of harmony and peace? Everything in its right place. Awesome. One part for tshirts, one for suits, one for ties,...
Now we have a great advantage: We can find our favourite tshirt very quickly when required!
If we were used to this mess, our girl did a big effort with us to change our mindset because it was much easier to throw things into our box.

Same thing happens in code. We get a task and it is much easier to throw this piece of functionality anywhere in the solution. But we should bear in mind that solving bugs takes more money that adding the code in the beginning. We want to save money and time when it comes to fix something in the future.
If we take discipline and follow SRP, it gets easier and easier, you get a good habit.
We can follow SRP all over our code:

Classes: 
Before this talk I was a lot into applying SRP in classes, because classes are natural representation of a bussiness entity in our software. Some simple rules to apply SRP in classes:
Don´t put multiple classes in one file.
Classes have to be narrow and focus. E.g. imagine one class with multiple calculations, database operations, communication. This is so unstable, every change in the system will affect this class because it does everything. If we create multiple small focus classes, a reason for a class to change becomes lower.
Methods:
Something stupid from my side was that I hadn´t thought in the importance of SRP in methods. Venkat took quite a bit of time to explain this.
Long methods are a bad idea because:

They are hard to read.
Hard to test.
Hard to reuse: how do you reuse a monsterous method when you need that 10 lines of code in the middle? You can´t and it leads to dupplication of these 10 lines.
Hard to maintain, modify and eventually remove if some piece of the method changes.

Please spread this idea: Don´t write long methods. It´s a simple thing to avoid and it leads to a great benefit.
If we write short methods we turn to follow SRP in them.

How long is long? 

Instead of trying to put a number or a size, Venkat gave an example similar to this:

Remember this Kid A mate that is new in your company. Everybody says he writes long methods. You want to check...

-How was your weekend Kid A?
-At 19.00 on Saturday I took my car, drive to Albert Hall using Ring3, arrived to a concert by Radiohead, it was great, they played the full album called as me, Johny Greenwood did a mistake in a song, I drank 6 beers and a few shots so I had to sleep in a park and I couldn´t speak to that girl I like, so on Sunday I had a huge handover....
Ten minutes later...
-Ouch, please stop, TMI. 
-Oh, you told me how was my weekend, what´s up?
-Yeah right, I thought you could explain me how was your weekend without doing the same you do in code. You are the guy that writes long methods, aren´t you?

His answer was too long, he of course violated SRP. We don´t speak to people like this.
We speak to colleagues like this:
-Hi Jose Gonzalez, how was your weekend?
-Well, on Saturday I played in a concert, on Sunday I went to the park.
-Wow a concert! How was that?
-It was great, people liked my new album.

Jose Gonzalez is a SRP guy. He explains in high level of concepts. When we wanted more info about a certain concept, because we prefer music, we follow and ask about his concert.
Maybe another colleague more interested in parks will come and ask Jose about it with more detail.

So please keep this:

We must mention things in one level of abstraction at a time.

This is the so called SLAP principle, Single Level of Abstraction. 
