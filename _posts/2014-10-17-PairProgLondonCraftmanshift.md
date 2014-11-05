---
layout: post
title: London Software Craftsmanship Community, Pair programming
---

Yerterday I went to [7th Talk by the London Software Craftsmanship Community](http://www.meetup.com/london-software-craftsmanship/events/212202712/)

Really glad I could go and meet this great people.

The event consisted of two half-hour talks and one ten-minute live kata. Everything was recorded.

## _A new Model of Testing_, by Paul Gerrard

[Full video](https://skillsmatter.com/skillscasts/5818-a-new-model-of-testing)

I remember sometimes I have talked with some friends about the following: 
_We are programmers, and sometimes we don´t know what kind of magic, charms, terters do. All we see is a smile coming to your place, "it doesn´t work, I found a bug"._

> Testers, designers, developers, we live in the same ecosystem to create a common thing, we should know each other!

It was great to assist to this talk. Paul is a tester who used to be a programmer (he even programs yet).
According to Paul, the old testing way won´t work any more. He is writing a book about that. you can download the [paper](http://dev.sp.qa/download/newModel/NMIntro) and give Paul feedback.

Bassically the old testing way won´t work because our system are changing:

- InternetOfThings is here,

- We have million of devices and it will get humungous in some years

- We´ll have internet everywhere and testers won´t be able to test the same way.

### Key take aways:

- Forget about testing logistics (using sql or oracle, using chrome or IE, using agile or waterfall,..) testing thinking should not rely on it.

+ All testing is exploratory: We identify sources of knowledge, to build test modules, that inform our testing

- All testing is based on models. Humans use models everywhere: maps, class diagrams, uml, mathematics,... Bassically all models are wrong (otherwise it would be reality), but useful!

+ When we are testing we should actively think that we are using models.

- When a test fails, first thing testers think: has my testing model failed? That´s when you ask the programmer "is it ok?" If now, then you report a bug, testing model was good, programmer´s produced a model that doesn´t fit to it.

+ We explore (create a model) and then test (using the model). Devs, we do the same!!

- Tester and programmers, we have the same capabilities.

+ Goal: End manual, let automation do it. Human testers, will produce models (programming) to feed automation frameworks.

- So testers do need to learn how to code

+ Testers don't own testing anymore.

## Quick exercise, Do simple constraints when creating and algorithm, by Sandro Mancuso

{% highlight %}
It was really impressing to see Sandro in action. He implemented an algorithm to calculate the Romans numbers based on a decimal input. He followed TDD sistematically from the beginning, it has the type of "wow" effect that I always feel when somebody is doing TDD. He also used sublime together with some plugin to run from Jasmine tests (impresing to see it).

I´ll try to reproduce the exercise he did but this is the key take away:
When doing an algorithm, enforce simple constraints in the beginning. By trying to use simple constraints along the way (like if), then use more complex if needed (like exceptions treatment).
If use complex when simple is feasable, the algorithm gets more complex.

When I saw that I remember that according to TDD you write the minimun code that make a test to pass. Sandro´s workshop fits {% endhighlight %}

## _Be the best pair you can be!_, by David Morgantini

David is a software developer with a unique experience: he is married to a girl who did a PhD thesis about Pair Programming. When she was writing it, he had to read it, sometimes :)
He did took the practical essences of it. 

I [wrote](http://runxfun.blogspot.co.uk/2012/12/software-creation-using-collaborationii.html) some time ago about the benefit of pair programming. When David explained it, I thought, "ok, this is common sense", but it is great if somebody reminds you common sense things.

The full [video](https://skillsmatter.com/skillscasts/5859-be-the-best-pair-you-can-be) is worth watching.

Some notes I took...

### Some definitions

Driver, navigator
Disengagement
People: Expert, novice, we can have different types of pairs (EE, EN, NN). If you were the novice in one session you can become the expert in the other, because you know more in that case.

### Patterns:

- One keyboard, two keyboards, a laptop, a mirrored lapton,
- Dual station (he prefers it,then everyone has its own space).

### Benefits:

- Economics: number of defects is lower.
- Quality: two heads looking at the same
- Dev statisfaction: you write better code, it makes you happier when you arrive home
- Learning/ knowledge sharing
- Comunication, team building

### Disengagement, the big problem!

If disengaged, the pair looses all of the benefits of Pair Programming!
We see it when...

1. The pair gets distracted: solution mutually agreed disengagement ("let´s do a break")
Expert-Novice: It is very easy novice becomes disengaged. Solution: expert checks attention frequently.

It is the best if pair knows the goal of the session: quality, learning, the two are novices,... Then we can better achieved the goal.

2. Some work doesn't fit for pair programming. We can try to make it work by:

	- Split a U/S into tiny tasks. Some of the tiny tasks can fit Pair Programming.
	- Break the pair if it doesn´t work.
	- Identify tasks that need pairing
	- Expert: ensure novice is driving!! 

3. Uncomfortable dev environment. Solutions: 

	- Swap pairs, 
	- Try to make your pair feel like at home (same IDE he uses, proper monitor, no laptop for Pair programing!!...)

4. Interruptions: A team leader comes over and tell you important stuff... 
Some rules:

	- A pair shouldn´t be disrupted.
	- If pair gets interrupted: "Please, wait a minute..." and complete an ongoing discussion
	- If pair suffers a longer interruption, plan it! Make sure one of the pair can follow and go on when alone
	- Re-establish the pair when interrupted in order to go on: "from where did we leave it?"

5. Time pressure:

	- Plan novice pairing in Planning meeting.
	- Expert: verbalize progress and ask for feedback

6. Social pressure:
Novice: _I don't want to look as a stupid_.

- E: Let some time alone for novice to consider solutions alone, before doing the pairing.
- N: stop if you don't know what´s going on, ask questions!
- E: Should Encourage the novice to drive!
		Establish context before you start: expert explaining the problem before starting!!

Keep coding!!


