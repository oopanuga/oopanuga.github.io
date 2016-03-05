---
layout: post
title: Introduction to Elixir
---
*Originally published in* [Findmypast tech blog](http://tech.findmypast.com/intro-to-elixir/)

## Introduction

I have been trying Functional Languages for a little while now. F# is in the .Net environment so why not give it a go. Haskell seems like a different monster, so let's give it a try, Erlang and its EVM looked amazing.

I don't really know why but even though I tried, they were not very engaging for me.

Then I learnt Elixir, a new language running in the EVM. I must be honest, I am a *C-guy*, so I like my curly braces and my semicolons. Elixir had some Ruby-like syntax, so no more semicolons to me.
At the end I have come to enjoy programming in Elixir so much.
In this post I want to share the basics with you so that maybe you give it a chance, as I did myself a few months ago.

## Why Functional Programming?

We have all read why FP is awesome, so I don't want to annoy you with yet another post around FP awesomeness. 
I'd like though, to give you some context, about why this thing matters.

Computers are getting wider, more CPUs, many languages don't embrace parallel processing from their inception.
What we are seeing these days is OOP embracing some FP principles, as systems become more distributed.

Functional programming makes a lot easier to work with Distributed systems because of...

### Immutability

*You can't change things.*

Consider this Javascript code:

{% highlight js %}
var ShoppingCart = function(){
  items = [];

  this.addItem = function(item){
    //find the item, if not there push.
  };

  this.total = function(){
    var sum = 0;
    //loop the items and sum it up
  }

  this.count = function(){
    return items.length;
  }
};
{% endhighlight %}

We are exposing functions to mutate the internal state of the shopping cart, in this case **items** property. If you then want to add an item to the cart:

{% highlight js %}
cart.addItem({sku: "ROVER", price: 3333223322});
{% endhighlight %}

In Functional Programming we don't mutate the state of the application but we transform it. 
This means that in our previous example, you would get a new cart when adding an item to it:
{% highlight js %}
cart = ShoppingCart.new()
cart = ShoppingCart.add_item(cart, {sku: "MAV", price: 10000000000})
cart = ShoppingCart.add_item(cart, {sku: "ROVER", price: 3333223322})
{% endhighlight %}

### No Side Effects

In OOP we are really focus on hiding and protecting things. To me it looks like we try not to be honest with ourselves, we hide our code so I can't touch it later.
Then side effects arise its ugly face. Because when you hide things, those things can do other unexpected things, like saving things in database or calling a service that deletes some data.

At the end we are making our lives more complicated because things are harder to follow and maintain. With FP one function does one thing.

So if we mix Immutability and No side effects we get the following benefits:

 - Pushes you to write smaller more predictable functions.
 - Tests are clearer, actually you don't really need mocks.
 - Not much debugging.

### Distributed programming

Remember how C# deals with concurrency: async, await, tasks, locks,... you have to do that because things are mutable/side effects.
A process may be changing something while other process tries to read that something. In Javascript we have callbacks, promises.
In FP, you don't share any state, things are immutable. So having different processes talking to each other is much safer.
 
## Let's meet Elixir

I am assuming that you can run IEX (interactive elixir) in a command line. If you can't just download it from [Elixir web site](http://elixir-lang.org/) or use any sort of Vagrant or Chocoltery.

Let's open IEX and...
 
#### No semicolons, no braces.

Elixir is like Ruby, really clean languages.

{% highlight elixir %}
 #this is a comment
title = "a string"
price = 12.00
quantity = 1
{% endhighlight %}

#### Atoms

Atoms are labels in Elixir:

{% highlight elixir %}
my_atom = :atom
customer_charge = {:card, "4111-1111-1111-1111", 12, 19}
{% endhighlight %}

#### Tuples, lists, keyword list, map, struct

{% highlight elixir %}
#A tuple is just a container of data
tuple = {:label, 1, "Steve", [1,2]}

#Elixirists like using Atoms in the first position of a tuple to describe what's in the tuple.
{:chocolate, yummies}
{:fruit, [:apple, :banana]}

#This is a list, it can contain any type of data
list = ["one", 2, 3.000]

#A Keyword list is just a list with Key and Value pairs
keyword = [one: "one", two: 2, three: "three"]

#You can access a member of a keyword list like this:
keyword[:one]

#A Map looks really similar to a keyword list
map = %{key: "value", another_key: "another value"}
#But you don't have to use an atom to get one of the values
map.key == "value"
{% endhighlight %}

#### Head tail lists decomposition
{% highlight elixir %}
team = ["diogo", "mickey", "darren", "kira", "jae", "liam", "stephen", "waqas"]

#you can get the first element of a list by doing:
[head|tail] = team
#head is equal to "diogo", tail is a list with the rest of the elements
[head|tail] = tail
#head is now equal to "mickey"
{% endhighlight %}

#### Functions
{% highlight elixir %}
#a typical structure for an Elixir function.
def a_function(arg) do
  #do something
end

#call your function like this
a_function("something")
#or like this
a_function "something"
{% endhighlight %}

#### Pattern matching

We have a post around [Pattern matching](http://tech.findmypast.com/tweak-your-mind-into-functional-programming/). There are many ways to define pattern matching but I like to look at it as a Maths equation.
Let's look today at the following.
In OOP we usually control the flow of the application like this:
{% highlight js %}
//eat_a_treat as we'll do in OOP:
def eat_a_treat(treat) do
	if treat == {:chocolate, yummies} do
		//yummies are bound and ready to use
	else if treat == {:fruit, oh_no}
		//eat that boring fruit
	end
end
{% endhighlight %}

In Elixir we hardly ever write if statements, we use pattern matching to control the flow:
{% highlight elixir %}
def eat_a_treat({:chocolate, yummies}) do
  #yummies are bound and ready to use
end

def eat_a_treat({:fruit, oh_no}) do
  #eat that boring fruit
end
{% endhighlight %}

When we do overloading in OOP we can define the same function but with different input parametes. If you have the same input parameters, then you have to change the name.
In Elixir the name of the function is the same and so is the input parameter, a tuple in this case. The appropriate function will be called using Pattern matching.

#### Pipes
In Elixir we can pass the result of a function to the next function:
{% highlight elixir %}
#without pipes
def eat_a_treat({:fruit, fruits}) do
  clean_fruit = peel(fruits)
  eat(clean_fruit)
end

#with pipes
def eat_a_treat({:fruit, fruits}) do
  fruits
    |> peel
    |> eat
end
{% endhighlight %}

By the way you may be wondering how the hell you return something in a function: it is really easy, it will be the last statement of the function

#### Recursion, Pattern matching, and Head tail: head tail recursion

I will leave you with some mind blowing stuff. I have the list of my expenses the last month. I want to sum them all:
{% highlight elixir %}
#this is in Pounds by the way
sum_it([10000000, 1200, 230000, 2345.6])
{% endhighlight %}

In OOP we usually loop through the list and accumulate the sum using some sort of foreach. 
In Elixir it is actually much better to recurse through the list, and operate it.

{% highlight elixir %}
def sum_it([],total), do: total
def sum_it([amount | tail], total), do sum_it([tail], first + total)
{% endhighlight %}

## How do I learn more stuff?

Here you have a list of resources I found useful when learning Elixir:
- Elixir docs, I recommend looking into the Enum module, the most comprenhensive set of functions to manipulate lists I've ever seen:
[http://elixir-lang.org/docs/stable/elixir/Kernel.html](http://elixir-lang.org/docs/stable/elixir/Kernel.html)

- Introduction to Elixir:
[https://github.com/elixir-lang/plug](https://github.com/elixir-lang/plug)

- Some really nice tutorial done by Rob Connery:
[http://www.redfour.io/](http://www.redfour.io/)

- Jose Valim, creator of Elixir guiding you to create a really nice example:
[https://howistart.org/posts/elixir/1](https://howistart.org/posts/elixir/1)

- This page has tone of resources for Elixir:
[https://github.com/h4cc/awesome-elixir](https://github.com/h4cc/awesome-elixir)
 
- And the slack group is really active, you can ask question they are really helpful people:
[https://elixir-lang.slack.com](https://elixir-lang.slack.com)

- Use a lot IEX to try out code, at the end we are only writing functions.

- Finally I recomend that you challenge yourself by doing some practical work. My friend Richard Kotze has contributed to Open Source ([Whitebread](https://github.com/meadsteve/white-bread) and [Eyedrops](https://github.com/rkotze/eye_drops)) and Leigh Shepperson is creating an amazing [Algebra package](https://github.com/leighshepperson/exalgebra).   
In my case I have implemented a [TicTacToe](https://github.com/javflores/tic-tac-toe) in Elixir and ReactJS.


Hope this was useful, thanks for reading!
