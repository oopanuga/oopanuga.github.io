---
layout: post
title: Jurassic Park, a root cause analysis
---

## Abstract

Devs in the Ditch is a community here in London, sponsored by 7digital company.
Yesterday they carried out a workshop with a very strange name: Jurassic Park: What went wrong?

The title drew my attention and I decided to go.
The goal was to apply something called *Root cause analysis* to the issues that happened in 1993 and lead to a total chaos in Jurassic Park. The workshop was so much fun and also I could take some very good lessons (at least how to avoid problems if I open a Dinosaur park).

Root cause analysis the typical name that when I read it, I always skip because it looks like some management/mba stuff.
It turned to be a great tool of problem solving.

### Root cause analysis basis

1. What went wrong? E.g. _servers went down for an hour and the web page_ wasn´t available.

2. What are the causes? Identify the _whys_.

3. What are the lessons to take to avoid this in the future?

Watch [wikipedia](http://en.wikipedia.org/wiki/Root_cause_analysis) for a longer explanation.

## Jurassic Park, what went wrong?

The talk started by watching a number of fragments from the movie. We could see how the scientists don´t like the Jurassic park project when the Product Owner explain it to them, how the Park keeper is attacked by a velociraptor, how the software developer dissables the security system, how the stakeholder is taken by a T-rex,...

Normally time is a limiting factor so we took the main issues, in this case _some person is eaten by a dinosaur_:
- TRex ate a lawer
- Velociraptor ate a park keeper
- Velociraptor eats a mathematician.

Specifically we took:

> TRex eats a lawyer, Donald Gennaro.

It was because he was the only person that didn´t belong to the company
## Jurassic Park, why Donald Gennaro is eaten by a Tyrannosaurus?

Donald Gennaro represents the investors of the park. He wants to check what these scientists are doing there and the safety of the park.

Let´s see how he dies:

![Image description](/images/JurassicPark/1.jpg)

It all starts with a park drive, he goes to visit it with the other visitors, separate cars.

On the program tour Gennaro rides in the same car as the CEO's grand kids Lex and Tim Murphy. They have a show where they're suppose to see a T. rex eating a goat, but the Tyrannosaurus doesn't appear at all.

With a storm coming, the group headed back in the cars to the Visitor Center but the power was shut off by the Software developer and the cars stopped in front of the Tyrannosaurus Paddock where Gennaro, Lex, and Tim watched in horror as the T. rex ate the goat it was supposed to eat earlier. 

Gennaro runs out of the car and into a bathroom nearby and sits frightfully on a toilet. 

Ian Malcolm tried to distract the T.-Rex but only causes it to charge towards the bathroom. 
Gennaro closes the door, getting the TRex attraction 

The Tyrannosaurus smashes its head through the door and causes the entire structure to collapse around Gennaro. He unsuccessfully attempts to remain still before it grabs his upper body in its mouth, shakes him violently then devours most of him.

We discussed together every big issue that lead to the death of the lawyer, why Donald dies? And we iterate to every reason, getting new whys until we get to an death end.

> Because the dinosaur is hungry.

Dinosaur is hungry because it is in its instincts.

> A TRex walking in freedom in the middle of the park.

Because the security fence was turned off:

1. Because the sofware developer wanted to sell dino embryos:
Because he wasn´t paid enough.

2. Because the fence wasn´t proper enough for a dinosaur park.

3. Because there was a single big software dev.

And so on...

We iterate though every reason and every why until we find an end point.
We got to something like this:

![Image description](/images/JurassicPark/2.JPG)

The following picture is not very good quality but we can see the different branches starting in one cause and letting to different whys:

![Image description](/images/JurassicPark/3.jpg)

Basically you iterate from a problem, asking whys.
If you find several reasons you walk down them.
It´s ok to end up in a dead end or some general fact that we can´t change.

## What are the lessons to take? How can we ensure the next park succeeds?

After all this, we take every branch of whys and try to get actions from there.

- Pay more to developers.
- Pay more expert devs or do pair programming.
- Better access control.
- Pay appropriate TRex distractors.

Some of these actions are more effective than others. 
We can assign priorities to them.

If we don´t have enough time or resources, we can start with the most priority ones.

Another takeaway was:

We routinely deploy complex systems into unpredictable environtments, so...
-Build systems that  expect and tolerate failure.
-Build systems that  expect and prevent malitious attacks.


That was it. I think many times we try to do this kind of thing but that was a systematic way to face issues and finding solutions. And also it was fun.

Don´t you the potential ussage of this in our next sprint retrospective... :D

Have a great weekend!

Whaun Flores (according to Starbucks) 
