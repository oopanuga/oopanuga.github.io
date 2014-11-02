---
layout: post
title: Keep that simple
---

You can find beauty in code, beauty is in simplicity.
There´s one quote by Plato that states:

>Beauty of style and harmony and grace and good rythm depends on simplicity.

How many times we have been told that code should be:
  + Readable.
  
  + Maintainable.
  
  + With great quality.
  
  + That we must follow SOLID and a number of other principles.
  
  + That we must use patterns when appliable.

Simplicity is all of them together, this is what Plato is telling.

But what is beauty? This is a very subjective question.
For some architects, beautiful buildings are big ones, full of exotic luxurious stuff, whilst other would say minimalist buildings are the most beatiful ones.

![Image description](/images/keep that simple/Complex house.jpg)
![Image description](/images/keep that simple/SimpleHouse.jpg)

If you are a drummer, and somebody asks what is a beatiful rythm, you may say you like simple grooves that just follow your heartbeat. Other would say beauty is virtuosity in a complex solo fill.
And not to mention human beauty perception... Do you prefer, blondies or a gorgeous brunette, or a beautiful brown haired woman?:)

I see programming as an art, we create beauty, yet beauty that works, that´s useful, but still an art.
We are given a complex requirement, full of use cases and scenarioes to cover, full of abstract concepts, and we create a system that satisfies them all.
Where is beauty in our art?
Well, imagine you are in front of your source code system, and I tell you to find a beautiful code you remember. I bet that something you will find in that code is simplicity.
(Forgot to tell, please study, read,... others´ code. Why? Ok, we agree cooking is an art. Would you be likely to master cooking if you don´t go and taste famous cookers´ creations? If you don´t look for their secrets, their magic ingredients?)

I can remember I once saw a not very beautiful code. It was full of paths, strange names, only a few classes with lots of dependencies and complex inheritance structure. Hard to test, hard to add a single line of code without breaking anything.
On the other hand, I remember we did a beautiful simple code following the strategy pattern, it was like magic how the strategy was triggered. We had a change from customers, it was extremely easy to add another strategy without changing the controller classes. We met a number of principles when we created that code (open-close is one of my favourites, yet not easy to met).

But making something simple implies knowledge. Knowledge of the domain, knowledge of technology and experience.

A couple of weeks ago I did a code that wasn´t so beautiful, I know, I´m not proud of it:)
We are creating a process that runs periodically every day. I was told under code review: check with a test that everything is resumed ok after a power cut in the middle of the process.

*Ouch, that is difficult, how do I simulate a power cut in a test and later on assert that a subsequent run was ok, inside a single test...* *Parallel tasks* was my first thought. I had been reading about CS5 new parallel programming features, I wanted to proof I knew it.
Here is a simplified version of what I did:

```c#
    [TestMethod]
    public void Check_clean_up_after_power_cut()
    {
      //Prepare first run, interrupted by power cut
      bool didPowerFail = false;
      var powerFailSimulatorToken = new CancellationTokenSource();
      Task interruptedProcessTask = new Task(() =>
      {    using (powerFailSimulatorToken.Token.Register(Thread.CurrentThread.Abort))
        {
               try
               {
                  Process.Start();
               }           catch (ThreadAbortException)
               {
                  didPowerFail = true;
               }
         }  }, powerFailSimulatorToken.Token);

      SimulatePowerFailMock(powerFailSimulatorToken);
     //Trigger first run
     interruptedProcessTask.Start(); while (!didPowerFail)
     {     //Still running first process
     }

    //Trigger second run of process
     Process.Start();

     Asserts...
    }
```

Cool eh?...

I felt like a beast: *it´s likely that this test is going to be the most complex test in my company, I´m the best*. But Ok, haven´t we told that beauty is in simplicity?

Yes, this test is ugly, hard to read, fragile, hard to refactor and very likely to be a pain in the ass for a future programmer. In one word, complex. Tests should be simple.
I didn´t have enough knowledge of the domain. I just jump to the code, without taking my time to analyse the system. I just wanted to prove that I can do complex stuff.
Of course, code review came and I did this:

```c#
    [TestMethod]
    public void Check_clean_up_after_power_cut()
    {

       //Simulate first interrupted run by inserting corrupted test data
        SimulatePowerFail(powerFailSimulatorToken);//Trigger second run of process
        Process.Start();

        Asserts...
    }
```

Yes, it´s simpler, but isn´t this better?

A number of ways to achieve simplicity in your code:
  - Don´t jump to code without fully understand the problem. Take your time, run existing tests, make sketchs, do a meeting with your colleagues and explain the problem.

  - Do TDD. Yes, you will be creating a lot of tests. People don´t like TDD because it slows the development process. For me that´s a mistake, while you are incrementally developing the system, you will be creating a simpler class structure that makes all of the tests to pass, while making the code minimun.
  As stated by Uncle Bob, tests grow to specific, logic gets more generic and minimun.
  
  - Read and read code. Even if you are reading something you are not working on. You are in front of somebody else´s work and time. Please sit back and relax and understand why that was written.
  
  - Go to conferences, read books, keep yourself educated. Famous cookers educate themselves, they go to workshops, they go to other famouse cookers´restaurants. Please eat others´dishes.
  
  - Don´t think: if it was hard to write, it should be hard to read. Instead think that your code is going to stay for a long time (at least some years), it should be easy to maintain and fix. Oh yes, it would be eventually fixed:).
  
  - Code reviews... Yes, cookers receive feedback. Imagine you create a new dish, you give it to the jury, but then they don´t tell a word about it, they just go home. Do you think you will improve that dish? Will you think that dish was ok, or that they didn´t like it?

If you just want to keep one thing after this babble, keep this:

>Beauty is born and found in simplicity.

**Jørn Ølmheim, 97 Things Every Programmer Should Know**

What do you think? Have you had any experience about that? Any trick you use? I appreciate feedback:)

Speak soon, keep coding!

