---
layout: post
title: Using Pattern Matching to write a more idiomatic functional code
---

_(Originally published in: http://tech.findmypast.com/)_

As a functional programming newbie and someone that has done OOP for a few years, I often find myself writing code in Elixir that looks more like C# or Java.

Today I wanted to show how we can use the power of pattern matching to rethink how we define functions when we are writing code in Functional Programming.


#### What is Pattern Matching?

First things first, let's see what pattern matching is. In programming we typically assign values to a variable with an ```=``` sign:
{% highlight c# %}
var x = "Elixir" + "is great";
{% endhighlight %}

Now, remember what ```=``` sign was in your Math class:
{% highlight plaintext %}
x + 1 = 3
{% endhighlight %}
That means the two terms on either side of the ```=``` sign _are equivalent_. 

That's what it is in Elixir:
{% highlight elixir %}
[1, x, 5] = [1, 10, 5]
#x = 10
{% endhighlight %}
The only difference here is that Elixir will also solve the equation for us.

#### Pattern Matching in Functions

Elixir will try to match a function call to a specific implementation:

{% highlight elixir %}
defmodule SimpleMaths do

  def sum([head|tail]), do: head + sum(tail)
  
  def sum([]), do: 0

end

SimpleMaths.sum([1, 2, 3])
#6
{% endhighlight %}

Here we provide two definitions for ```sum``` function. When we pass in an empty list, Elixir will pattern match into the second definition and return a 0.
When we pass in a list, it will pattern match the parameter and get the head and the tail. Here we are simply accumulating the values on the list in order to perform the sum.

#### Using Functional Programming idioms

I remember when I started learning English, I learnt the grammar and vocabulary to make simple structures.
Then I tried to build more complex structures with the former simple structures. In order to do that I applied ideas and concepts that I knew from my mother tongue into my new language. Life was so good. 
Then suddenly something arose: my English was grammatically correct, yet native speakers struggled to understand. I wasn't really speaking English.

Let's see how this is relevant to programming. Let's define a module in Elixir to make payments:

{% highlight elixir %}
defmodule Payments do

  def pay_with_paypal(login: login, password: password) do
    #pay with Paypal
	%{login => login, password => password} |> validate |> do_transaction
  end

  def pay_with_card(number: number, expires: expires, code: code) do
    #pay with card
	%{number => number, expires => expires, code => code} |> connectwithBank |> validate |> do_transaction
  end

end
{% endhighlight %}

We have two ways of making a payment, either with paypal or with credit card. That code above is me speaking Elixir in a C# way.
In C# I need to rely on a change in the function name to identify different implementations. Despite being grammatically correct, the two functions are going to do pretty much the same.

Let's try to be a bit more idiomatic:

{% highlight elixir %}
defmodule Payments do

  def pay(:paypal, login: login, password: password) do
    #pay with Paypal
	%{login => login, password => password} |> validate |> do_transaction
  end

  def pay(:card, number: number, expires: expires, code: code) do
    #pay with card
	%{number => number, expires => expires, code => code} |> connectwithBank |> validate |> do_transaction
  end

end
{% endhighlight %}

Now when calling the pay function, Elixir will use the one with the appropriate matching pattern:

{% highlight elixir %}
Payments.pay(:paypal, login: "juanito", password: "123")
{% endhighlight %}

Notice that we have used an atom to help with the matching, it also helps with clarity of intent and expressiveness of the code.
