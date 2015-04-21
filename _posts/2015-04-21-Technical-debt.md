---
layout: post
title: Managing Technical Debt
---
Technical Debt is widely regarded as a bad thing; that should be avoided or should be paid back as soon as possible.

Should you? Well sometimes I don't think so. 
Let's try to compare Technical Debt with Financial Debt. 
Then let's list the various possibilities to identify the Technical Debt in our code, which you perhaps have to take care of.

Finally, we describe different ways that a project could pay back Technical Debt 
and which considerations must be made in order to decide if you should better repay, convert debt or just pay the interest.

## What is Technical Debt?

Developers have the choice to implement new features in two different ways: one is to do it quickly and messily, which will make future changes very hard. 
The other is a clean and smart solution, which takes longer to implement, but makes changes easier in the future 
([Martin Fowler](http://martinfowler.com/bliki/TechnicalDebt.html)). But why should the sponsors of a project accept higher costs for a clean implementation of a feature if the same feature, implemented with a messy solution, delivers the same functionality and costs less? Why should they spend money on automated test coverage? Tests are not features and therefore don’t deliver business value!

Although messy code or code without tests works perfectly for customers if it delivers the desired business value, this will lead to an uncontrollable code base, extremely specialised developers and eventually to an inflexible software product. A sufficient amount of messy code may bring a whole engineering department to a stand-still.

## Similarities and differences to _Financial Debt_

Code with low quality and no automatic test coverage can be compared with financial debt. This code is like a financial burden, which imposes on all stakeholders - not only on the developers - a debt incurring interest for the future. The principal amount is the cost of refactoring the codebase to a clean design, which allows easy future changes. The interest is the extra costs, which have to be paid in the future if the team has to work with a messy codebase instead of a good one.

Unlike financial debt, you don’t have to pay back Technical Debt. Sometimes paying it back would even be pointless: some pieces of code are rarely or never read or changed. Therefore Technical Debt also needs to take into account a probability of occurrence - how probable and how often will the messy code be touched in the future? Another difference to financial debt is that the originator of Technical Debt won’t necessarily pay back the debt himself, but rather the developers who maintain the codebase later.

Like financial debt, Technical Debt is not necessarily a bad thing. Going into debt to buy a house is responsible if you know how to pay it back. Buying lots of luxury items on your credit card knowing very well you’re not able to cover the bill usually ends up in a disaster. Concerning software Technical Debt might give an advantage of an early release and profit the organization more than it costs to pay back the Technical Debt. In finance, debt is a good thing, if the principal amount plus the interest is lower than the yield of the investment. The same is true for software. If you sacrifice internal quality for being the first on the market, it only pays off if the yield you make with this decision is higher than being later to the market with better internal quality. There is, however, a risk, because it is hard to estimate these benefits upfront since there is a degree of uncertainty.
