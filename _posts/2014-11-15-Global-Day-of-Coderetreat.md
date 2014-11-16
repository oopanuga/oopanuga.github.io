---
layout: post
title: Global day of Coderetreat
---
Hi there!

Yesterday I participated in the *Global Day of Coderetreat*. It was hosted by the London Software Craftsmanship Community.
It was an amazing day, I´m really glad I had the chance to go, please bear with me a moment, in this post I´ll explain why.

## What is the Global day of Coderetreat

![Image description](/images/Coderetreat/logo.jpeg)

A Coderetreat is a day-long workshop focused on the fundamentals of software development and design, providing developers the opportunity to take part in focused exercises away from the daily routine of “writing useable code.”

That´s a bit of blah blah blah, but basically we spend a day programming, pairing with different people and go home without a program. Unintuitive as it may sound at first, taking a step back from production realities can be quite liberating. The code retreat takes great advantage of the format to hammer home fundamental principles of good software.

So the Global day of Coderetreat was a coderetreat carried out in [the whole world.](http://globalday.coderetreat.org/) 
That was my basic understanding of the event.

## _Global day of Coderetreat_ in _DigitasLBi_ by the London Software Craftsmanship

7:00 AM Saturday morning, alarm rings... 

_Is it a week day? 

No. Why has the clock rang then? 

Ahmm yes, I´m going to go coding with a few strangers and the worst is that I won´t have any code at the end of the day, we´re going to clean it all!

Wait a minute, this event was organised a few months ago by a bunch of guys that used their free time to make the event possible, furthermore, if I don´t go, I will waste a place that somebody else willing to go could have used.

Ok... let´s go._

So these were my inner angel and devil challenging each other.

I arrive to Digitas LBi offices in Brick Lane and there I find the organisers, Rob (another Brompton´s maniac) and Mashooq, organisers of the [London Craftmanship Community](http://www.meetup.com/london-software-craftsmanship/events/213588962/). They are seting up with the help of other participants the impressive location.

![Image description](/images/Coderetreat/LBi-basement.jpg)

All participants arrive, free breakfast is served, time to meet the people we will be pairing with the whole day.
A few Spaniards, Miguel, Pedro, Francesc, and much more Dmitry, Peter, Alex, Marcelo,... All really nice guys.

The event starts. Mash says something that opens my mind: _Don´t be shy, meet other people, after all you have a lot of things in common with these guys, you came early on a Saturday morning to program_

![Image description](/images/Coderetreat/image2.jpeg)

He follows explaining the basis of the event.

![Image description](/images/Coderetreat/Instructions.png)

The target is clear:

> Write the best code you can!

We don´t have to complete any working thing, in fact you don´t have to write any code if you have a conversation where you solve the target. We are not there to solve the full game but to learn:

> Learn from each other, because everyone brings a new pespective

Sometimes we were using my computer with C# and Visual Studio but many times I sat down in others´ computer, in front of a new IDE, a new language, a new style of testing.
It was great to great not only to meet these great guys, but learn so many things.

The game to solve is everytime the same: Conway´s Game of Life.
We see a video of Conway explaining the game:

Youtube video cornwail game of life.

Based on simple rules a simple game can evolve into something complex. This is a metaphor of life, we start to see in the game similarities to life: birth, death and survive.

We will work with different pairs everytime so we learn something new from everybody. Everytime we change the constraints for the solution of the game.
At the end of the session we get together and talk about how the constraints affect the solution.

These were the different constraints I had.

- Exercise 1: No constraints, just familiarizing with the exercise.

- Exercise 2: **Single level of identation per method, don´t use abbreviations**. So far so good.

## Exercise 3: _Object calisthenics with brutal refactoring_

Here we go that was my favourite one:

![Image description](/images/Coderetreat/image3.jpeg)

How many code reviews I would have failed with these rules! That was so extreme and you don´t have to follow all of these. This is just to open the mind and see how our way of programming changes.
Rob and Mash were going around, making sure that rules were followed. In fact, if you failed to keep one constraint, you get a _postit_ with the failed constraint (number 4).

Wrapping all the primitives leads to something called _Behavior Attractor_, when you take all of the strings, booleans, structs, ints and wrap then into objects, entities,... behavior starts to appear. 

If we try to avoid getter and setters, we get inmutability. When you have a set or get you are exposing the state of an object, by means of its member variables. Anyone can then change the state of an object having the known as _side effects_.
This also leads to the principle _Tell Don´t Ask_. Instead of asking (using the internal state) and modifying the object, you tell the object to modify (do something).

Avoiding the _else_ keyboard makes us writing multiple returning points, as soon as you know a result you return. In my opinion it makes the code more readable and it tends to avoid private variable where we store partial result that it turns leads to bugs.

After that we got a great lunch, courtesy of DigitasLBi.

## Exercise 4: _Baby Steps_

That was even more extrem. Take a look at the following photo:

![Image description](/images/Coderetreat/image4.jpeg)

Isn´t it crazy?! When two minutes passed, Rob rang the bell. If tests were not passing or the code wasn´t compiling, then we have two revert changes done in those two minutes.

Basically that was a practice to improve our skills in TDD. If you have to run a working test in two minutes this makes you writing the minimun code that made a test to pass. Instead of writing huge tests testing too many things, those tests were fast unit tests, checking a single thing.

Progress was great and I had the feeling that we were developing quicker and more safe: hardly ever a previous test became red, our tests were less fragile, because they were too small!!

## Exercise 5: _A litle bit of thinking before programming_

In this case we had the first ten minutes to take a piece of paper and do some writing, sketching, pseudocoding.

Most of my mates found that so useful to have a common understanding of the problem before jumping into the code. This way the pair were pulling in the same direction from the begining.
Whilst I got the same feeling, in my opinion, doing pre design instead of jumping directly to TDD makes the final solution bigger and it is prone to do over-engineering, because you put more things than require. TDD makes the simplest code that satisfies business requirements.

I understand though that if you have a big team and everybody starts from scratch using TDD, it would be maybe a total madness but if I go solo or with another mate to do a user story, I would definitively jump into TDD.

## Exercise 6: _No TDD!_

![Image description](/images/Coderetreat/image6.jpeg)

What!? So we´ve been the whole day doing TDD and learning how it helps and now we don´t have to do it.
Pufff. Hard to take hahaha 
We made a lot of jokes. There we were trying to make a working thingy without any tests to check that we were progressing well.

Everybody agreed that if you can´t run a test to check that something works then you need some User interface to check something.
So we used a command line programme and it took a while to set the whole thing, debugging...

> One of the reasons why I like TDD is because I program faster.

It is really fast to add something and try it straigh forward, without having to run a system.
It also makes you confident that you haven´t broken something without having to test the whole system again and again.

In addition my mates thought that it wasn´t that bad because we had been developing the same thing six times. If we had done that exercise, No TDD, the first one, we would have created the ugliest code ever.

I´m glad I did this exercise, now I know I wouldn´t start working in a company that doesn´t do TDD :D


That was the last exercise, we finished the day with a closing circle, everybody saying what he/she got from that day.

I have tried to explain here what it was for me, because it was so many things for a day.

After that we head up to a pub where we catch up we talked about code, work, football and we had a couple of beers...

Thanks to Rob and Mash for organising this event, it was so much fun.
I will go back again next year. Now I wonder why we don´t organise this kind of things in Granada, let´s see what we can do when I go back down there.

![Image description](/images/Coderetreat/image5.jpeg)


Thanks for reading!
