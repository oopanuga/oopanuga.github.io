---
layout: post
title: Object Calisthenics
---

I was firstly introduced on **Object Calisthenics** in the CodeRetreat last year. I remember having a mind blowing experience, then trying it, failing, trying again, finally the code produced was amazing.
I thought I could explain the rules of Object Calithenics in case you participate in a Code Kata or in your next Code Review somebody tells you: *You don't satisfy Rule number 4 of Object Calisthenics.*

This is the full list of rules:

----

**1. Only One Level Of Indentation Per Method**

**2. Don't Use The ELSE Keyword**

**3. Wrap All Primitives And Strings**

**4. First Class Collections**

**5. One Dot Per Line**

**6. Don't Abbreviate**

**7. Keep All Entities Small**

**8. No Classes With More Than Two Instance Variables**

**9. No Getters/Setters/Properties**

----

Some of the rules are really difficult to undestand in the beginning. Sometimes an example is worth a thousand words, so I'm going to show you an example that doesn't meet a given rule, and then try to fix it. Let's start.


## 1. Only One Level Of Indentation Per Method

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //Base level of indentation
    IMarkdownWriter writer = new MarkdownWriter();
    if(category == "News")
    {
      //First level of indentation
      writer.WriteBlock(contentBlocks[0]);
    }
    else
    {
      //First level of indentation
      if(addHeadline)
      {
        //Second level of indentation => rule broken
        writer.WriteHeadline(contentBlocks[0]);
      }
      
      for (int i = 1; i < contentBlocks.Length - 1; i++) 
      {
        //Second level of indentation => rule broken
        writer.WriteBlock(contentBlocks[i]);
      }
    }
}
{% endhighlight %}

Too many levels of indentation is often seen as not maintainable code. Firstly it is not very readable. Most importantly, it tells you that you are starting to break **Single Responsibility Principle**.
This also relates to the [**Single Level of Abstraction Principle**](http://javflores.github.io/NDC-Oslo-CoreSoftwareDesignPrinciples/): 

> A method should nicely compose things at a single level of abstraction. If you need more info about a certain abstraction you can go to that method and see the next level of abstraction.

How was your reaction when you found a legacy code like the one above? Was it a good experience?

How many times have you found yourself having to modify a code like the one above, stacking another *if check* with your case? I bet it didn't work in the beginning, maybe the code flow ended up in a previous if check.

One useful technique to split this up is to use [Extract Method](http://refactoring.com/catalog/extractMethod.html) technique described in Martin Fowler's [Refactoring book](http://martinfowler.com/books/refactoring.html).

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //Base level of indentation
    if(category == "News")
    {
      PublishNews(contentBlocks[0]);
    }
    else
    {
      //First level of indentation
      PublishContentPost(contentBlocks, addHeadline);
    }
}

private void PublishContentPost(string[] contentBlocks, bool addHeadline)
{
    //Base level of indentation
    IMarkdownWriter writer = new MarkdownWriter();
    if(addHeadline)
    {
      //First level of indentation
      PublishNews(contentBlocks[0]);
    }
    
    contentBlocks.Except(post.Content.Blocks[0])
                 .Foreach(block => writer.WriteBlock(block);
}
{% endhighlight %}

Now the code has become more explicit about its purpose. If we take a look at the first method, we have one level of abstraction: managing the category.
If you want to know how a post is published, you can navigate to *PublishPost* method and find out its internal details.

## Don't use ELSE Keyword

Mmmm, let's see, I'm already breaking this one in my solution before:

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    if(category == "News")
    {
        PublishNews(contentBlocks[0]);
    }
    else
    {
        PublishContentPost(contentBlocks, addHeadline);
    }
}
{% endhighlight %}

Maybe this is too extreme, but the problem with *if/else* is that they grow to create a really bad code. It is too easy when you are fixing a bug to add another *if branch* to manage your case. 

Let's try to fix it with the nice **multiple return statements** trick:

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //Base level of indentation
    if(category == "News")
    {
        PublishNews(contentBlocks[0]);
        return;
    }
    
    PublishContentPost(contentBlocks, addHeadline);
}
{% endhighlight %}

Now the code is more readable, if/else checks do degrade readability.

This example is really simple but also sometimes, using *if/else* can be seen as a code smell around breaking **Single Responsibility**: it normally means that you are doing too much in your method, that you have several behaviours in one method. Using the former trick doesn't really solve that problem.

How do we encapsulate varying behaviour for the same object based on some state? Behavioural patterns come to rescue. The more common one is **Strategy Pattern**, but you can also use the **Null Object pattern** or **Decorator Pattern**. It really depends on the problem. 

In our current example I see that we clearly have two behaviours, one to publish a headline of the post, one to publish the rest of the content. In this case we can try another *Behavioural pattern*, the [Chain of Responsibility](https://github.com/iluwatar/java-design-patterns#chain-of-responsibility), by creating a common interface that will define the behaviour:

{% highlight c# %}
public interface IPostPublisher
{
    int ExecutionOrder { get; }
    
    void Publish(string category, bool addHeadline, string[] contentBlocks);
}
{% endhighlight %}

After [Mr. Samir Talwar](http://careers.stackoverflow.com/uk/SamirTalwar) has done an excelent [code review of this post](http://45.55.130.240/t/post-about-object-calisthenics/124). He proposes that **Strategy pattern** and **polymorphism** can make a simpler and better code. Let's try to pass his code review. Let's define an **IContent** with a simple **Publish** method.

{% highlight c# %}
public interface IContent
{
    public void Publish();
}
{% endhighlight %}

Now two implementations of that interface:

{% highlight c# %}
public class Headline : IContent
{
    readonly string Content;
    readonly IMarkdownWriter _writer;
    
    public Headline(string[] fullContent, IMarkdownWriter writer)
    {
        _writer = writer;
        
        Content = fullContent[0];
    }
    
    public void Publish()
    {
        _writer.WriteBlock(Content);
    }
}

public class MainContent : IContent
{
    readonly string[] Content;
    readonly IMarkdownWriter _writer;
    
    public MainContent(string[] fullContent, IMarkdownWriter writer)
    {
        _writer = writer;
        
        Content = fullContent.Except(fullContent[0]);
    }
    
    public void Publish()
    {
        Content.Foreach(block => writer.WriteBlock(block);
    }
}
{% endhighlight %}

Let's create a factory for these *IContent* classes:

{% highlight c# %}
public class ContentFactory()
{
    public IList<IContent> Create(string category, string[] contentBlocks, bool addHeadline)
    {
        var allContent = new List<IContent>();
        if(ShoulCreateHeadLine(category, addHeadline))
        {
            allContent.Add(new Headline(contentBlocks));
        }
        
        if(!IsNews(category))
        {
            allContent.Add(new MainContent(contentBlocks));
        }
        
        return allContent;
    }
    
    private bool ShoulCreateHeadLine(string category, bool addHeadline)
    {
        return IsNews(category) || addHeadLine;
    }
    
    private bool IsNews(string category)
    {
        return category == "News";
    }
}
{% endhighlight %}

Now the **BlogPublisher** can call the Factory, and invoke the Publish method for every **IContent**:

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    var allContent = _contentFactory.Create(category, contentBlocks, addHeadline);
    allContent.Foreach(content => content.Publish());
}
{% endhighlight %}

In this case, in order to meet the **No Else rule** we have introduced a pattern. It was probably too much for this simple example, but the point was that this simple rule enforces us to think deeper in the code and come up with better approaches.

In those terrible cases with methods with 9 *if branches*, using this rule not only produces a more elegant and explicit  code, but also promotes a more maintainable code. 
In our case if in the future we have another type of *Publisher*, we won't need to modify the *BlogPublisher*, it will be as easy as introducing another implementation of the *IPostPublisher* interface. Our code will be **Open for Extension - Closed for Modification**

## 3. Wrap All Primitives And Strings

Do you like the following signatures I have so far?

{% highlight c# %}
void Publish(string category, bool addHeadline, string[] contentBlocks);
{% endhighlight %}

The first problem is that the list of parameters can grow unexpectily. Normally it is a code smell to have methods with more than 6 input parameters. If you have more than 2 or 3 input parameters in a method, it probably means that you are doing too much in that method.

Secondly there is a [Primitive Obsession Antipatern](http://c2.com/cgi/wiki?PrimitiveObsession): using primitive data types to represent domain ideas.

In **Domain Driven Design** there is something called **Value Objects** which encapsulates a **domain object**. If you try to wrap your primitives into **Domain objects** your code becomes more expresive about your domain. 
Let's try to do it in our example. I will define a *BlogPost*:

{% highlight c# %}
public class BlogPost
{
    public readonly string[] ContentBlocks;
    public readonly bool AddHeadline;
    public readonly string Category;
    
    
    public BlogPost(string category, string[] contentBlocks, bool addHeadline)
    {
        ContentBlocks = contentBlocks;
        AddHeadline = addHeadline;
        Category = category;
    }
}
{% endhighlight %}

Now we can pass around a BlogPost value object:

{% highlight c# %}
public void PublishBlogPost(BlogPost post)
{
    _publishers.OrderBy(publisher => publisher.ExecutionOrder)
               .Foreach(publisher => publisher.Publish(post));
}
{% endhighlight %}

Now we have a better encapsulation and it has become more readable. *BlogPost* hides all the internal details about a post.

## First Class Collections

Let's see the definition of this rule:

> Any class that contains a collection should contain no other member variables.

Ouch I have spot this code in our solution:

{% highlight c# %}
public class BlogPost
{
    public readonly string[] ContentBlocks;
    //...//
}
{% endhighlight %}

Ok, I will try to create a class for the ContentBlocks:

{% highlight c# %}
public class ContentBlocks
{
    public readonly string[] Blocks;
}

public class BlogPost
{
    public readonly ContentBlocks Content;
    public readonly bool AddHeadline;
    public readonly string Category;
}
{% endhighlight %}

Now I have to modify the users of that ContentBlock, like our main *Publish* method:

{% highlight c# %}
public void Publish(BlogPost post)
{
    if(post.Category == "News")
    {
      return;
    }
    
    contentBlocks.Except(post.Content.Blocks[0])
                 .Foreach(block => writer.WriteBlock(block);
}
{% endhighlight %}

## 5. One Dot Per Line

I have just broken it a second ago:

{% highlight c# %}
post.Content.Blocks[0]
{% endhighlight %}

Let's add a method to encapsulate that behaviour:

{% highlight c# %}
public class ContentBlocks
{
    private readonly string[] Blocks;
    
    public string HeadLine
    {
        get{ return Blocks[0]; }
    }
}

public class BlogPost
{
    public readonly ContentBlocks Content;
    public readonly bool AddHeadline;
    public readonly string Category;
    
    public string GetHeadLine()
    {
        return Content.HeadLine;
    }
}

{% endhighlight %}

Going back to the problem now we can fix it:

{% highlight c# %}
post.GetHeadLine()
{% endhighlight %}

The **Single Dot per Line** is a tricky one. It doesn't apply to **Fluent Interfaces**, like the following composition of functions:

{% highlight c# %}
contentBlocks.Except(post.GetHeadLine())
             .Foreach(block => writer.WriteBlock(block);
{% endhighlight %}

We should follow it for any other case. It is a direct use of the [Law of Demeter](http://c2.com/cgi/wiki?LawOfDemeter): Only talk to your immediate friends. In our case a publisher had to talk to a *post.Content.Blocks[0]*, a totally stranger. Now it only needs to talk to the BlogPost class.

## 6. Don't abbreviate

Why do you want to abbreviate? Is it because *int no* is clearly *int number*?

> Come on Juan... everybody knows that PPV is Pay Per View?

> Call me stupid but I didn't know because I'm new to your domain.

Nowadays we don't have any problems with memory, we can write something readable with long-enough names. If it is because you want to save some time typing, you can use shortkeys to get the word fetched automatically by your IDE.

Sorry guys, I feel like I'm not going to put any example of this rule:)

## 7. Keep All Entities Small

This is quiet related to **Single Responsibility Principle**. In general if your methods and entities are big, they will be hard to read, undestand and therefore to maintain.

If you follow **YAGNI Principle** you will start small with small components. As soon as you start adding things and your components start to grow, it is a good moment to stop and think: *should I break it into a separate component?*.

## 8. No Classes With More Than Two Instance Variables

I think we are breaking this rule already, let's see the *BlogPost* class:

{% highlight c# %}
public class BlogPost
{
    public readonly string[] ContentBlocks;
    public readonly bool AddHeadline;
    public readonly string Category;
}
{% endhighlight %}

I think we can group *AddHeadline* and *Category* into a new class:

{% highlight c# %}
public class PostAttributes
{
    public readonly bool AddHeadline;
    public readonly string Category;
}

public class Post
{
    public readonly ContentBlocks Content;
    public readonly PostAttributes Attributes;
}
{% endhighlight %}

Why does this rule constraint to 2 instance variables? 
Two is an arbitrary number but the main idea here is to force you to decouple your classes. You will end up with high cohesion and encapsulation. You will start finding that suddenly you have another behaviour in your system coming up. 

## 9. No Getters/Setters Properties

Setters and getters break encapsulation, it allows you to modify the state of an object and the so-called side effects start to appear. You should provide your own method to modify the state of your components, something that encapsulates it better and makes more sense to your domain.

This is quite related to the [Tell Don't Ask Principle](http://c2.com/cgi/wiki?TellDontAsk). I don't think we have broken it but let's go back for a second to the original implementation:

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //****//
      
      for (int i = 1; i < contentBlocks.Length - 1; i++) 
      {
        writer.WriteBlock(contentBlocks[i]);
      }
    }
    
    //****//
}
{% endhighlight %}

When we did *contentBlocks[i]* we have used a *get* method. The encapsulation of contentBlocks seems to be broken, we are using the internal details of the object to do something. *TDA principle* totally violated here.

Our refactoring solved that problem:
{% highlight c# %}
contentBlocks.Foreach(block => writer.WriteBlock(block);
{% endhighlight %}

Here we are telling the contentBlocks to do an operation for every of its details. We don't need to know about the length or the *i-th* element. 

## Summary

These rules are so strict and difficult to follow. I don't think I would have passed many code reviews if my reviewers had used Object Calisthenics.
You don't need to apply all these principles all the time. The point here is that you should try, having them when programming in the back of your mind. You may end up using rule number 3 or 4 all the time.
Anyways your code will get to a superior quality level. 100% ensured.
