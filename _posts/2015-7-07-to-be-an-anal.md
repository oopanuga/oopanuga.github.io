---
layout: post
title: To be an an anal
---

## 30-07-2015 9.00 AM 

I arrive to the office like any other day, I have just run to there like any other day. I am going to have a shower when another Technical Lead tells me:

_-Could I talk to you for a moment? It is about the code review you did yesterday for my team, you are being an anal in this code._

_- Excuse me, an anal?_

_- Yes, the whole team thinks like that. Your comments are minor details and we don't have time to fix that. We will add a refactoring ticket later on but we need to get this work finished. We have been waiting for your code review a lot and now you can with this. If you keep on doing like this we are not going to send you any code review._

I start sweating, what's going on? Juan Antonio, you don't even know what to be an anal is, certainly not something good.
I search for the definition. Urban dictionary:

> When someone is being anal about something, he is choosing to be an asshole. He is choosing to be tight and strong minded like the anal sphincter. Therefore, you tell him; "Don't be anal".

Ok, let's try another source, Urban Dictionary is not very precise sometimes. Merrian dictionary:

> Extremely or overly neat, careful, or precise.

Let's go back in time a bit.

## 29-07-2015 1.00 PM 

I get a pull request. I am doing pair programming in that moment so I start the code review an hour later. The pull request is not that big but one of the things that catches my attention is a nice litle refactoring. The code before the changes looks like this:

{% highlight c# %}
public interface IBlogPostFactory
{
  BlogPost CreateItem(DateTime date);
}

public class HeadLineBlogPostFactory : IBlogPostFactory
{
  public BlogPost CreateItem(DateTime date)
  {
    //Some logic
    var postCommon = GetCommon();
  }
  
  private IEnumerable<Properties> GetCommon()
  {
    //Do something based on type
  }
}

{% highlight c# %}
public class MassiveBlogPostFactory : IBlogPostFactory
{
  public BlogPost CreateItem(DateTime date)
  {
    //Some logic
    var postCommon = GetCommon(type):
  }
  
  private IEnumerable<Properties> GetCommon(string type)
  {
    //Do something based on type
  }
}
{% endhighlight %}

If we see the **GetCommon** method is duplicated and it is doing pretty much the same. So these guys followed the *Boy scout rule* and wanted to refactor this dupplication. The suggestion was the following:

{% highlight c# %}
public abstract class BlogPostFactory
{
  public abstract BlogPost CreateItem(DateTime date);
  
  protected IEnumerable<Properties> GetCommon()
  {
    //Do something based on type
  }
}

public class HeadLineBlogPostFactory : BlogPostFactory
{
  public override BlogPost CreateItem(DateTime date)
  {
    //Some logic
    var postCommon = base.GetCommon();
  }
}

{% highlight c# %}
public class MassiveBlogPostFactory : BlogPostFactory
{
  public override BlogPost CreateItem(DateTime date)
  {
    //Some logic
    var postCommon = base.GetCommon(type):
  }
}
{% endhighlight %}

The first thing I think is, *inheritance?*. It's been a lot of time since I last use inheritance, these days I use more composition and delegation. I tell that in the code review. Also I notice the new abstract class doesn't have any unit tests around it.

Here is my code review:

_1. I totally see the point of refactoring out the duplicated functionality but I don't see inheritance as the best match in here. Normally I favor composition over inheritance so I can easier extend the functionality later on. Most of the times delegation is a better approach. Happy to discuss this with you._

_2. This class doesn't have any test class? I think you should have refactored the tests after refactoring the logic. This will make the code easier to maintain._

In a few minutes the programmers that have done the code come to my desk to discuss the code review.
For my first comment the tell me that they have settled this abstract class as the *GetCommon* method is the same in all versions of this class. I tell then that this smells a bit like we have broken [Liskov substitution](http://javflores.github.io/NDC-Oslo-CoreSoftwareDesignPrinciples-Second-Part/), using inheritance to reuse some functionality rather than to implement a *is-a* relation. The case is simple, just reusing a method, I would have used delegation and the abstract would be simple service. 

I know that this discussion can take hours and it isn't a big deal, so I tell them: 

_- No problem guys, let's go for it. If this inheritance turns into something unmanageable, we can change it later on. Still I would like to see some unit tests around **GetCommon** protected method of the abstract class_.

_-But Juan, there were no unit tests for that functionality before._

_- Yes, there were but before this method was a private one so it was covered indirectly by unit testing the calling methods. This is not very nice of course, and you could see in those Unit tests that were breaking single responsibility in that class since the set up for the unit tests was massive. Now that you have refactored out the functionality it's a good chance to make those tests simpler and actually checking the **GetCommon method** logic in a better place._

_-But this is an abstract class, we can't instantiate it, therefore we can't unit test it._

_-We can use some techniques to do that. What I normally do is creating another  child of the abstract class for testing purposses. I call it accessor and it allows you to hit the protected methods of the abstract class. It is normally a balance but if I have in my abstract class important logic that I want to unit test a do that to improve the confidence in my changes. Let me go to your computer and we can do the code review together._

We go to their desks and we start using this technique.
The *Accessor* class look like this:

{% highlight c# %}
public class BlogPostFactoryAccessor : BlogPostFactory
{
  public override BlogPost CreateItem(DateTime date)
  {
    //Dummy logic, I don't care, this logic will be better unit tested for the actual concrete classes
    return new BlogPost();
  }
  
  protected IEnumerable<Properties> GetCommon_Accessor()
  {
    return base.GetCommon();
  }
}
{% endhighlight %}

Now we can create tests for *GetCommon* method through the Accessor method:

{% highlight c# %}
[Subject("BlogPostFactory")]
class BlogPostFactoryTests
{
    class When_getting_common_properties
    {
        Because of = () => _result = sut.GetCommon_Acessor();
        It should_return_ordered_properties_based_on_properties_order = () => _result.ShouldContainOnlyOrdered(_orderedProperties);
        Establish context = () =>
            {
                var firstNameProperty = new Property()
                                            {
                                                Name = "FirstName",
                                                Order = 1
                                            };
                var lastNameProperty = new Property()
                                           {
                                               Name = "LastName",
                                               Order = 2
                                           };
                
                _orderedProperties = new List<Property>()
                                        {
                                            firstNameProperty,
                                            lastNameProperty
                                        };
            };
        static IEnumerable<Property> _result;
        static IEnumerable<Property> _orderedProperties;
    }
}
{% endhighlight %}

Now we can test properly and in the right place important things that happen in the parent class, like ordering and filtering. Futhermore we can simplify the tests in the child classes since things like ordering don't belong to there.

My pair programming colleagues like the exercise. We spend an extra hour to fix this. I notice they are a bit pissed off because they feel the preasure of the business to deliver but I think they feel happy because they have learnt a new technique and they have increased the confidence in their code. I tell them:

_Your code was alright, it works and it has some unit tests around it but we strive for the best of the qualities we can in our code we should try to do do it. Most of the times we don't have that preasure to deliver, it is a fake ilussion that we create. I don't normally get the business telling me that the Project is taking longer but what I get is complaints if my code is full of bugs because I haven't taken time to get the quality just right._

## 30-07-2015 9.00 AM

After getting over from the initial shock of the Tech Lead words I'm able to articulate a few words:

_- I don't think I am being an anal (whatever that means). I have done a code review with my best intentions, because I have some principles and rules I always follow, I stick to my principles. At the end of the day this is not yours source code, nor mine. It is ours, more than that, is not ours it is just a way to get something working. If we were carpenters and we were told to do a table, at the end of the day, the table is not ours, it belongs to get customer. We try to do our best quality to get the customer happy and get new orders. We don't relax our quality because the table was done by my team or your team._

...

_If I was an anal I would have not discussed with then the code review._

_I wouldn't have gotten to a common understanding of the problem and a solution._ 

_I wouldn't have gone to do the fix together and show them a technique that they thought it was impossible._
