---
layout: post
title: Uncle Bob strikes again, TDD is Alive
---

Skills Matter is a company that provides courses, workshops and much more here in London. They do something called _In the Brain_, these are talks by some famous speaker about anything. Normally it is kind of a master class. And it´s free!

Last Monday 12/10 I could see Robert C. Martin (Uncle Bob) in one of these talks. 
You have the recording available [here](https://skillsmatter.com/skillscasts/5833-in-the-brain-of-uncle-bob-tdd-is-alive-well-and-growing). It´s always great to see this famous programmer in action, not only because of his knowledge but also because of the passion he puts.
The talk was carried out in an impressive building, _News_, the closest building to the famous London´s Shard:

![Image description](/images/tddAlive/londonbridge.jpg)

For those of you who don´t know Uncle Bob, he is an American software professional since 1970, he is one of the leaders of the software craftsmanship movement. He is author of a number of well-known books, such as _Clean Code_ or _The Clean Coder_.

## TDD is dead

![Image description](/images/tddAlive/1.JPG)

Uncle Bob focused his talk criticizing [this](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html) famous article by David Heinemeier, inventor of Ruby on Rails and founder of 37signals.
To sum up, David tries to explain why TDD (_Test Driven Development_) doesn´t work and why he is not anymore trying to hide he isn´t doing TDD.
This controversy also involved an [online debate](https://plus.google.com/events/ci2g23mk0lh9too9bgbp3rbut0k) involving Martin Fowler, Kent Beck and David.

Bear in mind this:

> TDD is not writing unit tests, TDD is a way of writing code.

## The Three Rules of TDD

Uncle Bob published _The Three Rules of TDD_. [Here](http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd) you can see them.

1. You are not allowed to write any production code unless it is to make a failing unit test pass.

2. You are not allowed to write any more of a unit test than is sufficient to fail. And compilation failures are failures.

3. You are not allowed to write any more production code than is sufficient to pass the one failing unit test.

You have probably already thought: _This is hard and tedious._ or _This will make me program slower, it is a waste of time and effort_.

Of course they are difficult rules to meet, but as any other discipline it involves practice. The benefits are such that we should really try to meet them.

1. Imagine that everybody in your team use TDD and follow those rules. If we pick anybody, it doesn´t matter who, her/his code was working a minute ago (or even less). Isn´t this great? 

> All the code was working a minute ago.

2. No debugging required. Who likes debugging? People with those shortkeys, masters of debugging, with their watches and their conditional break points. 

> Do you want your kids to be _debuggers_ when they grow up?

3. We nowadays use third party packages. What we normally do is search in Google, sometimes we read the documentation. But...

> Tests are the best documentation, because they are described with programmers´ natural language, code.

If we need to create an object, we can go to an existing test an see what parameters should be provided. Tests are living examples of how to use the code.

4. TDD makes it easier to write code that meets design principles. 
We know how difficult it is sometimes to write tests for an existing code. You couldn´t test that private method, that huge private method that is doing too many things.

With those Three rules are code will be testable by definition, testable is decoupled:

> In order to test that piece, it need to be decoupled. TDD forces you to write simpler, less coupled designs.

5. Everybody likes Continuous test environments. And the reason for that is because we need to touch code.
When we have a bank of unit tests running around a piece of code we feel confident to touch it.

In some companies people don´t like TDD, ok, but have you ever been to a company that doesn´t have Unit Tests?
Unit tests are required because of the former reason, *trust*. With every checkin we need to deliver some tests.

I recorded this bit in the talk, I let Bob explain how it is to write tests for existing code:

<iframe src="//player.vimeo.com/video/114416823" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe> <p><a href="http://vimeo.com/114416823">Unit test after production code</a> from <a href="http://vimeo.com/user35442471">Juan Antonio Vicaria Flores</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

The lack of trust in this kind of companies, where unit tests are written after writing code, test coverage is usually measured. 
We sometimes hear: _Test coverage should be not less than 60%_.
Let me rephrase this: _We don´t care if 40% of the code is not working_.

What are the figures we reach if we do TDD? 100%?

And it goes worse...
How does it feel when you know that those tests that were written after the code, are now failing because of your changes? You go and read those failing tests and it doesn´t make any sense, you don´t understand what´s going on. Because of those tests were merely written to pass a code review, they are sticked to the implementation, they aren´t readable at all, they are difficult to maintain.
So remember this:

> A bad Unit Test is worse than No Test.

For all of those reasons that´s why I think:

> TDD procedures create the best Unit Testing benchmark.

## When TDD does not work

Ok, nobody is perfect. TDD sometimes just doesn´t work, basically in those scenarios where you can´t write a test first.

When you can´t predict an answer: For example css files style the User Interface. How would you test a CSS file, going to check the _ith_ pixel and see if it is _jth_ positions above another blue one? It really depends on the platform you are delivering to.

Still you can try to move as much as possible of the code out of the User Interface´s dependent modules. Bob suggested the [Model-View-Presenter pattern](http://msdn.microsoft.com/en-us/magazine/cc188690.aspx).

## Software Development is a Discipline

Imagine you have I give you a pen drive with a source code. Imagine you can tweak one single bit and change it to a different value. The program would probably stop working.

Bridges and buildings are made to prevent that kind of problems, they have for example double pillars so if one fails the other just covers it. Can you imagine another business that has the same challenge we have?

Accounters. If the fail one single digit in one of the transactions, the company can bankrupt. So they have something called *Double entry booking*. It is a methodology invented centuries ago. Even experienced accounters always follow that, no doubt. They even go to prison if they don´t follow that discipline.

Software Development is a discipline and as such it has methodologies. TDD is one of them and we don´t follow it sometimes.
We treat our code with less care than accounters treat their transactions.

## TDD in a large Existing code base

This is an usual question, how to do TDD in legacy code?
Bob so clear in his answer: 

> You can´t do TDD for legacy code. 

Legacy code normally doesn´t have much unit tests, it wasn´t design to be testable. It is full of large methods and classes very difficult to test. Bob recommended the book, _Working effectively with legacy code_. In that book Michael Feathers suggests that we eventually will find that we need to touch certain parts of the legacy system more often than others. Those can be refactored and decoupled to make it testable and put around it a bank of unit tests.

Always try to follow the _Boy scout rule, leave the field better than you found it_. This eventually will make a non-tested code into a tested code.

If you have to add a feature that talks to a legacy system, you can decouple it from the legacy and follow TDD to implement it.

## To BDD or TDD

All of the things ending with DD are becoming so popular. BDD, TDD and DDD are some examples.

Basically TDD and BDD are the same, there is no much difference. Behavioral Driven Development just adds the GIVEN, WHEN and THEN, so we are still testing but the difference is the way we express the test.

The main difference between different type of tests is its level:

> The main distinction between types of tests is its audience.

- *Unit Tests* are written by developers for developers.
- *Acceptance Tests* are written by the business for the business.

Acceptance Tests normally involve the User Interface:

{% highlight c# %}
GIVEN I am on the Landing Page,
WHEN I select the Search menu
THEN I see the Search page
{% endhighlight %}

Starting the user interface and doing things there, it´s slow. It is also fragile because UI changes more often than the business rules. 
If we could, we should try to avoid going through the UI that often. Try to isolate the connection with the actual UI.

## My own experience on TDD

### My first approach to TDD

I probably will write some post about the basics of TDD for those that are not familiar with it. For me it was hard to learn in the beginning but I´m glad I did the change.

When I first started in my very first job, Agresso, one of the dogmas to follow was TDD. Red-Green-Refactor. Somebody taught me a couple of rough ideas about it, but nobody explained it properly to me. I didn´t see the benefits, I couldn´t see that properly in action, I simply knew that I had to write a failing test first.

In a couple of months I was able to pass code reviews: all I needed to do was writing an initial test and them I could be adding production code within an hour. 

I was lucky I started in a new team, and there, there was a Norwegian, Jens Askgaard, he was really into TDD. He could smell when I hadn´t done TDD. What really changed for me was the day I saw Jens in action, the way he iterated through the process, adding small simple Unit Test, making then pass and refactor. I tried it.
I had to stop myself from adding more than the minimum code to make a test pass.

I remember we did a prototype, everything was working as expected in that prototype. Then Jens told me to delete it, it shouldn´t be production code. Instead we started gradually using TDD, having that prototype as a guide, as a proof of concept, proving that new feature was possible. When everything was finished, Jens told me to compare that prototype with final production code: the rough idea was there but the final solution was much more different.
The reason was easy:

> A prototype just covers the happy path and few other cases. If you implement a feature using TDD you cover every case, every scenario, you cover every possible failure. You design the code by thinking from outside of the system, how it should behave, you design a system as a consumer of the system. Furthermore it makes you think about new issues, not considered cases.

I remember it was tricky to teach new people in TDD. There are two ways of teaching people:

- Telling it is mandatory to do it.

- Showing the benefit of doing that.

My favorite way was to let them do as they wanted. Then I usually asked how they were doing and if they were stacked I sat with them to do pair programming. We started by deleting all of the code, and doing pair programming with them. We ended up by comparing the initial approach with the final solution.

I remember one colleague used to have one unit test class always opened. Then, he would ask me some question, I would ask for the unit tests to understand the problem. Then I wouldn´t complain because he wasn´t doing TDD. 

I remember there used to be people in my previous job that told me _I don´t really like TDD, it such a waste of time and it doesn´t work_.
Then I would go straight and ask _Really? Have you ever tried it properly?_

### Automating the process of running test

A few months ago I started in a new job. They were using TDD, but they were even using that at a new level. They had a tool, _NCrunch_. That one was running test in the background, while they were writing the code. I could see that they hardly ever debugged the code. They didn´t even run any Unit Test manually.

But that isn´t it. I was lucky as a newbie that they have such a good unit test framework. It ease my life to understand the domain, I just need to go to existing tests and see how the system works. It is letting me get into speed quicker.

Our normal flow there is the following when we need to create a new feature:

- We start with a number of high level acceptance tests, that covers the user story. We normally have them described in the ticket. These are pending to be implemented. We use SpecFlow and FluentAutomation.

- We go straight and follow TDD to implement the whole system. We use a BDD approach for Unit Tests by using MSpec.

- When everything is ready we run acceptance tests and everything is normally working.

### Measuring Coverage

I remember in my previous company some people were measuring test coverage. In my team we never cared, we were doing TDD, our coverage was going to be good.
And actually sometimes I went to check the metrics and we were around 90%. That´s right, 90%, not that 100%, maybe because we weren´t that strict with the Three Rules.

At my current job, we have NCrunch doing the work for us, it shows some ugly black dots in the code not covered. In most cases this is legacy code.

That´s all for now. I´m sorry that this post was so long, you know I like speaking about these things. I recommend you to go to one of these TDD talks, it is so good to see people speaking so passionately about it, it really pushes you up.

Looking forward to hear from your experiences with TDD! See you later!