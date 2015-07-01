I was firstly introduced on **Object Calisthenics** in the CodeRetreat last year. I remember having mind blowing experience, then trying it, then failing, trying again, and the code produced was amazing.
Since we are going to do an exercise using Object Calisthenics today in a Code Kata, I thought I could explain every of the rules with some actual examples.
Some of the rules are really difficult to undestand in the beginning. For each of them, I'm going to put an example that breaks that particular rule, and then how to meet it.
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
