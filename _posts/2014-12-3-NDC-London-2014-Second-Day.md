---
layout: post
title: NDC London, second day
---
Let´s talk about the second day of the NDC London.

## _OWIN, Katana and ASP.Net vNext, eliminating the pain of IIS_, David Simner

Good explanation about Katana and Owin.
A couple of interesting facts about the new vNext and how it will solve some current problems.
He talked about the new .Net Core, cloud optimised version of .Net, the KRunTime.
He also showed that unit testing will be easier as well, thanks to this new library approach.

## _Web Components: The Dawn of the reusable web_, by Cory House

Really good talk about web components Fundamentals.

In PluralSight Cory has a course about this.

### Problems:

- Now we have web pages full of divs. They are meaningless, dificult to find something in the markup.

![Image description](/images/ndcLondon/2day/2.JPG)

- Style conflicts: !important to force styles, you need quite specific selectors to avoid conflicts.

- If you need to bundle css, js and html together? Consider bootstrap.

- There are no standards: jquery, AngularJS, ExtJs.

![Image description](/images/ndcLondon/2day/1.JPG)

- More and more components become standard as industry becomes more mature.

### Web Components

All of these problems are removed with Web Components.

#### Templates: Inert, reusable markup

Poor man's template:

`<template>`: everything inside here won´t do anything, until clone to be used in the page.

#### Custom Elements: Define our own Elements

`<pluralsight-tab>`: everything with a dash will be a custom element.

You can take regular html elements and extend then to make better functionality

You can attach callbacks methods for custom elements.

#### Shadow DOM: Encapsulated Markup & Styling

Shadow DOM is already used today. In Chrome you can see it by turning on Elements/Shown user agent shadow DOM.
Currently used for example for a video tag, that adds a lot of divs behind the scenes that compose everything. 

The idea is the same: 

> Encapsulate something behind a namespace and lets hide it from where it could be manipulated since it is not required.

We can do the same.

We defining a shadow dom, we can apply styling to that very easy.

#### Imports: Bundle everything together

Import of JS, CSS, HTML work quite differently.

To sum up, Web Components are supported in Chrome, Opera, partially in Firefox. You have external tools that ease the creation but Cory mention is better to learn native web components before jumping to Polymer, which is the abstraction over the foundation.

Other alternatives: IE 6, knockout or Ember components. Angular directives IE 9. IE10, Polymer, xtag.

If you just support Opera and Chrome then go for native.

Some tools: nativecomponents.org, and Plunker (similar to fidleJs).

## _Decoupling from ASP.NET, Hexagonal Architectures in .Net_, by Ian Cooper

Talk about general software architecture in the beginning. 
Software Architecture defines styles and patterns, things to be repeated that worked.

Normally the cost in software goes in maintenance not in create new code.

And one of the enemies of maintenance is coupling. We need to avoid it. The way is have separated layers that talk between adjacent layers by a single interface.

He drove a very dry talk with a powerpoint with slides full of information. He will share these slides after the talk as reference. At the end he showed some actual implementations of these patterns.

### Ports and Adapters
Architectural style, variation of layered architecture.

We still have domain to define the rules of the bussiness. We try to keep the techonology specifics out of this layer.
But the adapers abstract the inputs to the system and our outputs. 
Only way to talk to the domain from the adapters is with a port.

It preserves the domain model from the technology changes.

A service facade is ok, it provides way to communicate layers. It provides a contract to client for lower layers.
The problem is that it doesn´t follow single responsibility, it manages too many things. This is called Fat Services.
To solve it we break it in several interfaces to follow interface segregation.

Command design pattern: separate the invoker from the receiver by creating a command.
Macro command is a command that encapsulates other commands.

This commands are great to pass around through those broken interfaces.

Code in GitHub, Ian Cooper: Paramore.TaskManager.
He uses something called Viemu that shows references in methods and classes directly in the code.

Command dispatcher pattern, paramore.commandProcessor in Ian Cooper, gitHub.

Another benefit is to measure quality on the service.

## _Container based deployments with Docker_, by Ben Hall

![Image description](/images/ndcLondon/2day/3.JPG)

Really interesting to see the latest trends in deployment.
We will talk more specifically about this in one blog post.

## _Data Done Right, Ten databases in an Hour_, David Simons

What is wrong with sql? 

Sql is still valid for lots of scenarios. But now we have a lot more choices for other things that typically Sql was used and didn´t fit well.

![Image description](/images/ndcLondon/2day/4.JPG)

### MongoDb

Every document is a thing.
When data is changed so often, it ease deployment with sharding.
Use by foursquare, because of geospatial indexes is managed easier.
The new york times. In MongoDb the database is schemaless. It works really well for storing content where we dont have a schema.

Very famous failure of MongoDb, diaspora, social network. Social network data is highly relational, because of people relations.

### Eloquera

Object oriented database

Every object can be stored in your database, object are a thing in your database.
Development is very quick and we have transparency to the database. SImilar to ORMs.

### Neo4j

Graph database. Relations is a key actor in this kind of databases.
Good framework for a range of languages.
It is really nice to see that the same schema we draw in a piece of paper, exists in the database, in code,... It closely matches the model.
It uses Cypher query language to do graph based queries. Very readable.

But...
it doesn´t scale very well.

### Dynamo

It is used by Amazon, they need a very reliable database.

Several implementations:
Voldemort: key/value store very reliable and fast. But limited because it only stores key value pairs.

### Marmotta

Triple value store, you store subject, predicate, object.
It works the same as Linked Media Framework. Use URIs as names for things. Include links to other URIs, so that they can discover more things.
RebBull media House is using it.

It doesn´t scale very well.

### HBase/Hadoop

Hadoop is more an ecosystem, when you have services that are storing their data.
Use it when you have BigData.
Together with Apache HBase, we have autoatic sharding and consistency. Built-in efficient query methods.

### Elastic Search

Its a document based store that focused around text searching queries.
Searching is done by JSON format

It is used in stackoverflow and github

### TempoDB

Timestamp value pairs. Good choice when time is important. For example stock market, health system.
Really handy that it can do temporal interpolation.

A really dedicated database

### SQL

SQL is a really good standard, very mature:
RDBMS
Schemas: If your data is always be the same, why going for a NoSQL if you can use set operations, really powerfull.
ACID principles: Really good principles that rule SQL. You know your database is always in a valid state.

### PosgreSQL

All the good stuff of sql.
And the open source packages on top of that. We are telling that SQL wasn´t easy for geospatial coordinates, but we have open source packages for postgresql. For example instagram uses it.

It has foreign data wrappers: Select stuff from neo4j join stuff from a file store join stuff from oracle.

It has plugins for webservices.

## Five Essential Things to know about ASP.Net VNext, David Fowler and Damian  Edwards

They carry a comparison between current components and see how it mpas to new components in vNext.

CLR working in Mac and Linux.
.Net framework is moved to .Net BCL.
System.Web is now KRE.
Webserver, From IIS to IIS still and also kestrel (.Net web server cross platform)
Extra libraries will be driven by Nuget.
MSBuild to compile application 

Then they jump into real coding, starting with an asp.net 5 application.

With Roslyn, while running the application, changes in files are compiled and applied inmediately.
KRE is compiling the code behind the scenes.

There are a lot new file templates.
You can set commands to run the same application in several servers.

You can set environment variables and modify then at runtime for different environments.

They are trying to avoid the old filosofy: you get everything in system.web from the beginning.
Now you don´t have anything unless you ask for it. Aka Pay as you go.
You get more things by adding more to a middle ware. This middleware classes are added to the Startup.cs
The yellow screen of dead is replaced by a white screen of dead with a smiley. It is better because it has some information (Try to show it in the blog!!!)

Amazing, David discovered a bug in the Startup.cs of the base asp vnext. He fixed it in the talk and submit changes to github for code review on Damian. This is all in GitHub is open source and i could see it.

![Image description](/images/ndcLondon/2day/5.JPG)

You have IOC inside aspvnext. You add dependencies in the startup by calling Context.AddService. 
Context.RequestService

## Embracing Http in the era of API´s, Hadi Hariri

![Image description](/images/ndcLondon/2day/6.JPG)

Really good talk by Hadi Hariri, the boss in JetBrains.

> With Http, your API is your application.

Http is an application protocol, it is actually in the Application Layer in the OSI model.
People think is at transport layer though and that´s why it has been devaluated.

He went through a really easy example and explain how an Http API should be done.

1. We start with a simple XML that delivers an entry method, _GetCustomer_. We take out this _GetCustomer_ and put in the access url for the XML.

2. Instead of having a method name, _GetCustomer_, we use nouns, _Customer_. We have *Resources*!

3. We define verbs, actions that we can do against a Resource, that is, a set of the CRUD operations.
Be careful: if a user is not able to carry some action, we can play with the _Allow_ tag in the HttpHeader to say: _No, this operation is not available_.
We can even use HttpMethod.Options, that for a given resource, displays the verbs that it can be used.

So far so good. This is why Http is an Application protocol, because it defines Resources and Verbs against a resource.

4. Http API should return correct response values.

5. Content Negotiation: We can see this like:

> A single end point has now multiple representations.

We can do that with Hypermedia tags: hypermedia takes the state out of the server and give it to the client. That is, if we call a server to create an order, the server will give you back the result and next steps you can do after this.

To apply Hypermedia we can use different media types:

- XML: by exposing a link in the response body.

- Statndard state: ATOM

- Custom type: by using the tag Content-type.

- JSON-LD: Mecanism to expose hypermedia through JSON.

Hypermedia is great because we push the state from the server to the client, the api is easier and with let´s bugs, the server doesn´t have to maintain state for every client.

This is how a good Http API should be done. You have a restful system if you follow this.

Recommended reading, _Rest in Practice_.

> Don´t think that you are doing a restful service only because you return JSON.

And that was it for the second day.

Well, it was in terms of awesome programming talks.
But after that we have some live music and some fun with good friends.

![Image description](/images/ndcLondon/2day/7.JPG)

![Image description](/images/ndcLondon/2day/8.JPG)
