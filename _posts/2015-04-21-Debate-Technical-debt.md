---
layout: post
title: Debate, Technical Debt
---
I remember my last interview, they ask me:

> If you are approaching the project deadline and you haven't finished all the work, would you sacrifize quality in order to finish or would you either sacrifize some of the user stories towards maintaining quality?

Of course my answer was maintaining quality, quality is my Motto (read this article by [Richard Vidis](http://rvidis.github.io/What-is-your-Motto/)).
Technical Debt is widely regarded as a bad thing; that should be avoided or should be paid back as soon as possible. Everybody wants to do things right, isn't it?

Lately I listen a [podcast](http://www.se-radio.net/2015/04/episode-224-sven-johann-and-eberhard-wolff-on-technical-debt/) in Software Engineering radio about Technical Debt and that was a mind blowing for me:

> Is it possible that Technical Debt could be not such a bad thing?

Today I wanted to debate with you on:

- Comparing Technical Debt with Financial Debt and see why Technical Debt could be a good thing.

- Finding Technical Debt in our code.

- Ways of paying back Technical Debt in our projects (considerations to be made in order to decide if we should better repay, convert debt or just pay the interest).

But first things first...

## What is Technical Debt?

> What decissions have I made in the past that prevent me from delivering features today?

Developers have the choice to implement new features in two different ways: one is to do it quickly and messily, which will make future changes very hard. 
The other is a clean and smart solution, which takes longer to implement, but makes changes easier in the future 
([Martin Fowler](http://martinfowler.com/bliki/TechnicalDebt.html)). 

But why should the sponsors of a project accept higher costs for a clean implementation of a feature if the same feature, implemented with a messy solution, delivers the same functionality and costs less? Why should they spend money on automated test coverage? Tests are not features and therefore don’t deliver business value!

Although messy code or code without tests works perfectly for customers if it delivers the desired business value, this will lead to an uncontrollable code base, extremely specialised developers and eventually to an inflexible software product. A sufficient amount of messy code may bring a whole engineering department to a stand-still.

## Similarities and differences to _Financial Debt_

Code with low quality and no automatic test coverage can be compared with financial debt. This code is like a financial burden, which imposes on all stakeholders - not only on the developers - a debt incurring interest for the future.

### Similarities

- The principal amount is the cost of refactoring the codebase to a clean design, which allows easy future changes. 
The interest is the extra costs, which have to be paid in the future if the team has to work with a messy codebase instead of a good one.

- Like financial debt, Technical Debt is not necessarily a bad thing. Going into debt to buy a house is responsible if you know how to pay it back. Buying lots of luxury items on your credit card knowing very well you’re not able to cover the bill usually ends up in a disaster. Concerning software Technical Debt might give an advantage of an early release and profit the organization more than it costs to pay back the Technical Debt. In finance, debt is a good thing, if the principal amount plus the interest is lower than the yield of the investment. The same is true for software. If you sacrifice internal quality for being the first on the market, it only pays off if the yield you make with this decision is higher than being later to the market with better internal quality.

### Differences

- Unlike financial debt, you don’t have to pay back Technical Debt. Sometimes paying it back would even be pointless: some pieces of code are rarely or never read or changed. Therefore Technical Debt also needs to take into account a probability of occurrence - how probable and how often will the messy code be touched in the future? 

- Another difference to financial debt is that the originator of Technical Debt won’t necessarily pay back the debt himself, but rather the developers who maintain the codebase later.

- There is a risk, because it is hard to estimate these benefits upfront since there is a degree of uncertainty.

## Strategic design of Technical Debt

Strategic Design says that: 

> A system can’t have the same high level of quality throughout the system. 

We can choose to:

- Leave to chance which parts of the system have a good or a bad quality or 

- To actively control the quality. Messy code, which is rarely read or touched and doesn’t implement important requirements, does not have to be absolutely perfect and therefore we don’t need to spend a lot of effort on refactoring it into great code. So the question is which parts of the code should have high quality?

Understanding Strategic Design and Technical Debt leads to better communication within a project and therefore to better decisions from the stakeholders of a software project. 

### Which are the stakeholders of Technical Debt:

- *Customers* are annoyed by bugs or missing features due to low productivity.

- This leads to additional costs for the *Customer Service*, which annoys the people there.

- Increased development time and quality issues are also a problem for *marketing*.

- Bugs lead to frequent patches, which annoys the *operations team*.

- Many annoyed parties definitely don’t make the *management* happy - especially if there is bad publicity due to bugs, delays or security problems.

- Last but not least also the *developers* are suffering. No one wants to deliver bad work. Furthermore, no one wants to take over the bad work of others.

## Identifying Technical Debt

A major problem of Technical Debt is that it’s not obvious. Anyone can see the amount of debt on an account statement. But how can a team actually recognize Technical Debt? What are the indicators?

- General “smells” that manifest themselves in statements from the team members: _the only one who can ever change this code is Mahmut_, _let’s just copy & paste this code_, _if I touch that code everything will break_ or _too many TODOs or FIXMEs in the code_.

- Scrum-teams have a velocity. The software system has probably too much Technical Debt if the velocity decreases even though the team wasn’t changed and the external circumstances did not change.

- Software ages. One indicator for Technical Debt is when a system uses very old libraries, that are no longer being maintained or in a new version are much more productive (e.g. updating jQuery). In the worst case, the libraries are already so old, that the developers of the libraries no longer even support them.

- Automatically measuring Technical Debt is partially possible. Correctly configured, tools like StyleCop or CodeInspections can find important violations of best coding practices. This is not the ultimate solution, but a very good one. You ensure that developers follow important code metrics like appropriate class and method size or low cyclomatic complexity.

- Code coverage tools can detect how much code is really covered by automatical tests. This metric should be used with care, since there are no general guidelines. Generally a coverage of more than 90% is a good indicator that there are enough test cases available. Conversely, a coverage below 75% may indicate a serious problem.

- But there are also a lot of cases in which systems have Technical Debt, but it cannot be measured directly in the code, e.g. clumsy solutions, wrong choice of technology, a well-executed, but wrong design for a solution or simply evil hacks that cannot be detected easily by tools. In these cases Technical Debt must be measured differently: *the bugs per release rise rapidly, the velocity decreases permanently or the team is under extreme stress at the end of a release*.

- A very bad indicator are frequent problems in production. This means that the problems in the system are so extensive that reliable operation is no longer possible.

## How to manage Technical Debt

We have seen that Technical Debt cannot be ignored. Even the non-technical management on the client side must have an interest in managing Technical Debt as smartly as possible to achieve the best balance between short-, mid- and long-term success. *What can a team do to avoid wasting its time with unimportant beautification but still make meaningful business decisions for improving their code quality?*

### Buffer-task

The team creates one buffer-task per release with e.g. Engineering day. 
Team members can add that task for not yet scheduled refactorings into the buffer. 
So it is used for yet unknown problems that might appear in the future. Such a buffer is very easy to schedule and use. 

However, it also poses the risk that time is wasted on unimportant work. A buffer doesn’t force anyone to consider whether the time is spent for useful refactorings or not. Most likely the time of the buffer may not be optimally utilized - and especially deciding which refactorings will be done, although that should really be a business decision. Using buffer-tasks unfortunately means that what should be done is not really defined.

### Cleanup-releases

Some teams do a purely technical release to improve the codebase from time to time. This approach is only useful if a list with the really necessary refactorings already exists. Otherwise the team risks wasting time on unimportant refactorings. This approach must also be supported by the business side because it might delay new features. Of course this requires that business people understand Technical Debt. You should think about a pure technical release to clean up the codebase and to rework the architecture if some major effort is needed. For example the same parts of the code might always cause problems during development or operations, the current architecture might no longer fit the current requirements. Such problems cannot be solved within small refactorings. A cleanup release allows extensive changes.

It makes no sense to do a cleanup-release after a very hectic and time-critical release that has created a lot of Technical Debt. There is only little experience with the new code base, so no one can say which part of the code needs to be improved. The danger in changing code which does not really need improvement.

### Technical Backlog

The technical backlog is an established best practice to define purely technical work packages. Tasks for this purpose are created in a task tracker or requirements management tool. Each task has a brief description of the technical change to be made, why this technical change is important for the project and in which part of the code the technical change has to be performed. As with any other task, we need an estimate of how long it takes to develop a good enough solution. In addition we need to estimate the interest which is inherent in this code. A precise estimation is difficult, but often a rough estimate such as 'small', 'medium' or 'high' is sufficient to guide a decision. Ultimately we also need a probability: how likely will this code be read or changed in the not distant future?

#### Big advantage: Prioritize

Strategic Design has taught us that not every part of the codebase must be very good but only those parts whose changeability brings business value or need to be changed frequently for other reasons. So concerning Technical Debt code quality of rare or never changed locations can be ignored. Also important: Code should never be refactored without a good reason. So if a new requirement provides concrete business value then we should never implement this requirement based on badly written code but refactor beforehand. That way the really important requirements can be implemented to a “good enough” standard. When the team has been assigned to implement such an important feature in an upcoming release, the refactoring should be budgeted and then implemented.

This procedure ensures that no unnecessary beautification work is done and that refactorings are always directly associated with a requirement. This allows the customer to discuss and prioritize the refactoring with the team. The customer can also decide whether or not a requirement should be implemented based on debt and interest. There might be other factors to take into account such as time-to-market. The customer might also already know about future changes to the code which are based on these requirements. He might therefore decide to refactor to a clean code base in order to save costs in the future.

Hope these concerns were interesting for you.

