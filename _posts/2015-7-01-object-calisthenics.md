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

Some of the rules are really difficult to undestand in the beginning. Therefore for each of them, I'm going to put an example that breaks that particular rule, and then try to fix it.
Let's go.

## 1. Only One Level Of Indentation Per Method

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //Base level of identation
    IMarkdownWriter writer = new MarkdownWriter();
    if(category == "News")
    {
      //First level of identation
      writer.WriteBlock(contentBlocks[0]);
    }
    else
    {
      //First level of identation
      if(addHeadline)
      {
        //Second level of identation => rule broken
        writer.WriteHeadline(contentBlocks[0]);
      }
      
      for (int i = 1; i < contentBlocks.Lenght - 1; i++) 
      {
        //Second level of identation => rule broken
        writer.WriteBlock(contentBlocks[i]);
      }
    }
}
{% endhighlight %}

Too many levels of identation is often seen as not maintainable code. Firstly it is not very readable. Most importantly, it tells you that you are starting to break **Single Responsibility Principle**.
This also relates to the [**Single Level of Abstraction Principle**](http://javflores.github.io/NDC-Oslo-CoreSoftwareDesignPrinciples/): 

> A method should nicely compose things at a single level of abstraction. If you need more info about a certain abstraction you can go to that method and see the next level of abstraction.

How was your reaction when you found a legacy code like the one above? Was it a good experience?
How many times have you found yourself having to modify a code like the above and stacking another if check or writing something inside the second level of identation?

One useful technique to split this up it's to use [Extract Method](http://refactoring.com/catalog/extractMethod.html) technique described in Martin Fowler's [Refactoring book](http://martinfowler.com/books/refactoring.html).

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //Base level of identation
    if(category == "News")
    {
      PlublishNews(contentBlocks[0]);
    }
    else
    {
      //First level of identation
      PublishContentPost(contentBlocks, addHeadline)
    }
}

private void PublishPost(string[] contentBlocks, bool addHeadline)
{
    //Base level of identation
    IMarkdownWriter writer = new MarkdownWriter();
    if(addHeadline)
    {
      //First level of identation
      PlublishNews(contentBlocks[0]);
    }
    
    contentBlocks.Except(contentBlock[0])
                 .Foreach(block => writer.WriteBlock(block);
}
{% endhighlight %}

Now the code became more explicit about its purpose. If we take a look at the first method we have one level of abstraction: managing the category.
If you want to know how a post is publish you can navigate to *PublishPost* method.

## Don't use ELSE Keyword

Mmmm, let's see, I'm already breaking this one in my solution before:

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //Base level of identation
    if(category == "News")
    {
      PlublishNews(contentBlocks[0]);
    }
    else
    {
      //First level of identation
      PublishContentPost(contentBlocks, addHeadline)
    }
}
{% endhighlight %}

Maybe this is too extreme but the problem with *if/else* is that they grow to create a really bad code. It is too easy when you are fixing a bug to add another *if branch* to manage your case. Using if/else also tell us about breaking SRP: normally means that you have several behaviours in one method. 

How do we encapsulate varying behaviour for the same object based on some state? Behavioural patterns come to rescue. The simplest and more common one is the **Strategy Pattern** but you can use the *Null Object pattern* or **Decorator Pattern**. It really depends on the problem. In our current example I see that we are have two clear behaviours, one to publish a header, one to publish the rest of the content. In this case we can try the **Chain of Responsibility**:

{% highlight c# %}
public interface IPostPublisher
{
    int ExecutionOrder { get; }
    
    void Publish(string category, bool addHeadline, string[] contentBlocks);
}

public class HeadLinePublisher : IPostPublisher
{
    public int ExecutionOrder
    {
        get
        {
            return 1;
        }
    }
    
    public void Publish(string category, bool addHeadLine, string[] contentBlocks)
    {
        if(category != "News" || !addHeadLine)
        {
          return;
        }
        
        writer.WriteHeadline(contentBlocks[0]);
    }
}

public class MainContentPublisher : IPostPublisher
{
    public int ExecutionOrder
    {
        get
        {
            return 2;
        }
    }
    
    public void Publish(string category, bool addHeadLine, string[] contentBlocks)
    {
        if(category == "News")
        {
          return;
        }
        
        contentBlocks.Except(contentBlock[0])
                     .Foreach(block => writer.WriteBlock(block);
    }
}

public class BlogPublisher
{
    readonly IEnumerable<IPostPublisher> _publishers;
    
    public BlogPublisher(IEnumerable<IPostPublisher> publishers)
    {
        _publishers = publishers;
    }
    
    public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
    {
        _publishers.OrderBy(publisher => publisher.ExecutionOrder)
                   .Foreach(publisher => publisher.Publish(category, contentBlocks, addHeadline));
    }
}
{% endhighlight %}

We are using chain of responsibility pattern to encapsulate different behaviour. Every behaviour is encapsulated in a handler class with a given execution order. Every handler implements the same interface and it will try to manage the request in its own way. If it can't handle, it delegates to the next handler. From the BlogPublisher point of view, it doesn't know anything about the different handler, it only knows how to delegate.

If you see in order to meet the *No else rule* we introduced a pattern. Maybe it was too much for this simple example, but can you imagine this being applied to bigger cases? Not only the code becomes more elegant and explicit, we also get the advantage that if we need to introduce another type of publisher, we don't need to modify the the BlogPublisher, it is as easy as introducing another implementation of the *IPostPublisher* interface.

## 3. Wrap All Primitives And Strings

Do you like the following signatures I have so far?

{% highlight c# %}
void Publish(string category, bool addHeadline, string[] contentBlocks);
{% endhighlight %}

The first problem is that the list of parameters can grow unexpectily. It is common practice to say that more than 6 input parameters is bad practice. I will go even further:

> If you have more than 2 or 3 input parameters in a method, it probably means that you are doing too much in that method.

There even exists a [Primitive Obsession Antipatern](http://c2.com/cgi/wiki?PrimitiveObsession): using primitive data types to represent domain ideas.

It is very easy to fix, if we know about **Domain Driven Design**, there is something called **Value Objects**  that encapsulates a domain object. Let's try to use it in our example:

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

Now we can safely pass around a BlogPost value object:

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    var post = new BlogPost(category, contentBlocks, addHeadline);
    _publishers.OrderBy(publisher => publisher.ExecutionOrder)
               .Foreach(publisher => publisher.Publish(post));
}
{% endhighlight %}

Now we have a better encapsulation and our code becomes more readable since our *BlogPost* hides all the internal details that compose it.

## First Class Collections

Let's see the definition of this rule:

> Any class that contains a collection should contain no other member variables.

Oh ss*££$! I have this:

{% highlight c# %}
public class BlogPost
{
    public readonly string[] ContentBlocks;
    public readonly bool AddHeadline;
    public readonly string Category;
}
{% endhighlight %}

Ok, I will try to follow it, let's create a class for the ContentBlocks:

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

Now I have to modify the users of that ContentBlock, like this one:

{% highlight c# %}
public void Publish(BlogPost post)
{
    if(post.Category == "News")
    {
      return;
    }
    
    contentBlocks.Except(contentBlock[0])
                 .Foreach(block => writer.WriteBlock(block);
}
{% endhighlight %}

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

Ouch I have just broken it:

{% highlight c# %}
post.Content.Blocks[0]
{% endhighlight %}

Nah, I even didn't like it, I'm breaking the **Tell Don't Ask Principle** since I'm asking for the first element.
No prob, let's add a method to encapsulate that behaviour:

{% highlight c# %}
public class ContentBlocks
{
    private readonly string[] Blocks;
    
    public string GetHeadLine()
    {
        return Blocks[0];
    }
}

public class BlogPost
{
    public readonly ContentBlocks Content;
    public readonly bool AddHeadline;
    public readonly string Category;
    
    public string GetHeadLine()
    {
        return Content.GetHeadLine();
    }
}

{% endhighlight %}

This code smells a bit but hopefully Object Calisthenics will solve the problem with some of the next rules. Going back to the problem now we can fix it:

{% highlight c# %}
post.GetHeadLine()
{% endhighlight %}

The **Single Dot per Line** is a tricky one. It doesn't apply to **Fluent Interfaces**, like the following composition of functions:

{% highlight c# %}
contentBlocks.Except(post.GetHeadLine())
                 .Foreach(block => writer.WriteBlock(block);
{% endhighlight %}

We should follow it for any other case. It is a direct use of the [Law of Demeter](http://c2.com/cgi/wiki?LawOfDemeter): Only talk to your immediate friends. In our case a publisher needed to talk to a *post.Content.Blocks[0]*, a totally stranger. Now it only needs to talk to the BlogPost class.

## 6. Don't abbreviate

Why do you want to abbreviate? Is it because *no* is understand by everyone as *number* in your business?
What if tomorrow we have a new comer and she thinks this is the word **NO**.

*Come on Juan... everybody knows that PPV is Pay Per View?* 

*Call me stupid but I didn't know because I'm new to your domain*.

Nowadays we don't have any problems with memory so we can write something readable. If it is because you want to save some time typing, you can use shortkeys to get the word fetched automatically by your IDE.

Sorry guys, I feel like I'm not going to put any example of this rule.

## 7. Keep All Entities Small

This is quiet related to **Single Responsibility Principle**. In general if your methods and entities are big, they will be hard to read, undestand and therefore to maintain.

## 8. No Classes With More Than Two Instance Variables

I think we are breaking this rule already, let's see the BlogPost class:

{% highlight c# %}
public class BlogPost
{
    public readonly string[] ContentBlocks;
    public readonly bool AddHeadline;
    public readonly string Category;
}
{% endhighlight %}

Why does this rule enforce 2 instance variables? Two is an arbitrary number but the main idea is two force you to decouple your classes. You will end up with high cohesion and encapsulation in your classes.
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

I feel like we have a better encapsulation now.

## 9. No Getters/Setters/Properties

Setters and getters break encapsulation, it allows to modify the state of an object and the so-called start to appear.
This is quite related to the [Tell Don't Ask Principle](http://c2.com/cgi/wiki?TellDontAsk). I don't think we have broken it but let's go back for a second to the original implementation:

{% highlight c# %}
public void PublishBlogPost(string category, string[] contentBlocks, bool addHeadline)
{
    //****//
      
      for (int i = 1; i < contentBlocks.Lenght - 1; i++) 
      {
        writer.WriteBlock(contentBlocks[i]);
      }
    }
    
    //****//
}
{% endhighlight %}

When we did *contentBlocks[i]* we have used a get method. The encapsulation of contentBlocks since to be broken, we are using the internal details of the object to do something. TDA principle totally violated here.

Our refactoring solved that problem:
{% highlight c# %}
contentBlocks.Foreach(block => writer.WriteBlock(block);
{% endhighlight %}

Here we are telling the contentBlocks to do an operation for every of its details. We don't need to know about the lenght or the *i-th* element. 

## Summary

If I remember my old code, how many times could I have passed a code review if my reviewer stick to Object Calisthenics?
You don't need to apply all these principles in your code reviews or when you are programming but if you try to remember and apply them, your code won't ever be the same. 100% ensured.
